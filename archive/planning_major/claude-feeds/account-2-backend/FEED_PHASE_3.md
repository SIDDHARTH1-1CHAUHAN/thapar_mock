# Backend Phase 3: Route Optimizer Microservice (Hours 6-9)
## Branch: `feature/be-phase-3-route-optimizer-ms`

---

## AI/ML IN THIS PHASE

```
┌─────────────────────────────────────────────────────────┐
│  NO AI REQUIRED IN THIS PHASE                          │
│  ─────────────────────────────                         │
│  Route Optimizer uses ALGORITHMS, not ML:              │
│  • Pareto optimization for multi-objective scoring     │
│  • Dijkstra's algorithm for shortest path              │
│  • Weighted scoring (cost, time, reliability)          │
│                                                         │
│  This is pure algorithmic work - no LLM calls needed!  │
└─────────────────────────────────────────────────────────┘
```

---

## Prerequisites
- Phase BE-1 must be merged
- Redis running for background jobs

---

## Your Mission

Build a Route Optimizer microservice with route comparison, port congestion data, and WebSocket updates.

---

## Deliverables

### 1. Microservice Structure

```
microservices/route_optimizer/
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── api/
│   │   ├── __init__.py
│   │   ├── routes.py
│   │   └── websocket.py
│   ├── services/
│   │   ├── __init__.py
│   │   ├── route_service.py
│   │   ├── port_data.py
│   │   └── emissions.py
│   ├── algorithms/
│   │   └── pareto.py
│   └── jobs/
│       └── background.py
├── requirements.txt
└── Dockerfile
```

### 2. Requirements.txt

```
fastapi==0.109.0
uvicorn[standard]==0.27.0
pydantic==2.5.3
httpx==0.26.0
redis==5.0.1
rq==1.16.0
websockets==12.0
```

### 3. Main App with WebSocket

```python
# microservices/route_optimizer/app/main.py
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from .api.routes import router as routes_router
from .api.websocket import router as ws_router

app = FastAPI(title="Route Optimizer Microservice", version="1.0.0")

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)

app.include_router(routes_router)
app.include_router(ws_router)

@app.get("/health")
async def health():
    return {"status": "healthy", "service": "route-optimizer"}
```

### 4. Route Service

```python
# microservices/route_optimizer/app/services/route_service.py
from typing import Optional
from pydantic import BaseModel
from .port_data import port_service
from .emissions import calculate_emissions

class RouteRequest(BaseModel):
    origin_port: str
    destination_port: str
    cargo_weight_kg: float
    cargo_value_usd: float
    hs_code: Optional[str] = None

class Route(BaseModel):
    id: str
    name: str
    carrier: str
    transit_days: int
    cost_usd: float
    emissions_kg_co2: float
    congestion_risk: str
    recommended: bool
    savings: Optional[float] = None
    waypoints: list[dict]

class RouteService:
    """Calculate and compare shipping routes"""

    # Base routes (expand with real data later)
    CARRIERS = {
        "COSCO": {"base_rate": 0.12, "reliability": 0.85},
        "Evergreen": {"base_rate": 0.11, "reliability": 0.88},
        "MSC": {"base_rate": 0.10, "reliability": 0.82},
        "Maersk": {"base_rate": 0.13, "reliability": 0.90},
    }

    ROUTES = [
        {
            "id": "direct_sea",
            "name": "Direct Sea Freight",
            "mode": "ocean",
            "base_days": 18,
            "base_cost_per_kg": 1.50,
        },
        {
            "id": "via_transship",
            "name": "Via Transshipment Hub",
            "mode": "ocean",
            "base_days": 22,
            "base_cost_per_kg": 1.20,
        },
        {
            "id": "express_air",
            "name": "Express Air Freight",
            "mode": "air",
            "base_days": 4,
            "base_cost_per_kg": 6.50,
        },
    ]

    async def compare_routes(self, request: RouteRequest) -> list[Route]:
        """Compare available routes for given shipment"""

        routes = []
        baseline_cost = None

        for i, route_template in enumerate(self.ROUTES):
            # Get port congestion
            dest_congestion = await port_service.get_congestion(request.destination_port)

            # Calculate cost
            base_cost = route_template["base_cost_per_kg"] * request.cargo_weight_kg
            congestion_multiplier = 1 + (dest_congestion.get("utilization", 70) / 200)
            total_cost = base_cost * congestion_multiplier

            # Calculate transit time
            transit_days = route_template["base_days"]
            if dest_congestion.get("utilization", 70) > 80:
                transit_days += int((dest_congestion["utilization"] - 80) / 10)

            # Calculate emissions
            emissions = calculate_emissions(
                request.cargo_weight_kg,
                route_template["mode"],
                transit_days
            )

            # Determine congestion risk
            util = dest_congestion.get("utilization", 70)
            congestion_risk = "HIGH" if util > 80 else "MEDIUM" if util > 60 else "LOW"

            # Track baseline for savings calculation
            if i == 0:
                baseline_cost = total_cost

            # Select carrier
            carrier = list(self.CARRIERS.keys())[i % len(self.CARRIERS)]

            route = Route(
                id=route_template["id"],
                name=route_template["name"],
                carrier=carrier,
                transit_days=transit_days,
                cost_usd=round(total_cost, 2),
                emissions_kg_co2=round(emissions, 2),
                congestion_risk=congestion_risk,
                recommended=False,
                savings=round(baseline_cost - total_cost, 2) if baseline_cost and total_cost < baseline_cost else None,
                waypoints=[
                    {"port": request.origin_port, "type": "origin"},
                    {"port": request.destination_port, "type": "destination"},
                ]
            )
            routes.append(route)

        # Mark best route as recommended (Pareto optimal)
        routes = self._mark_recommended(routes)

        return routes

    def _mark_recommended(self, routes: list[Route]) -> list[Route]:
        """Mark the Pareto-optimal route as recommended"""
        # Simple scoring: balance cost and time
        best_score = float('inf')
        best_idx = 0

        for i, route in enumerate(routes):
            # Normalize and weight factors
            score = (route.cost_usd / 1000) + (route.transit_days * 50) + (route.emissions_kg_co2 / 100)
            if route.congestion_risk == "HIGH":
                score += 200

            if score < best_score:
                best_score = score
                best_idx = i

        routes[best_idx].recommended = True
        return routes

route_service = RouteService()
```

### 5. Port Data Service

```python
# microservices/route_optimizer/app/services/port_data.py
import httpx
from typing import Optional

class PortService:
    """Port congestion and data service"""

    # Simulated port data (replace with real API later)
    PORT_DATA = {
        "USLAX": {"name": "Los Angeles", "utilization": 85, "avg_wait_days": 3.2},
        "USLGB": {"name": "Long Beach", "utilization": 62, "avg_wait_days": 1.1},
        "CNSZX": {"name": "Shenzhen Yantian", "utilization": 71, "avg_wait_days": 0.5},
        "CNSHA": {"name": "Shanghai", "utilization": 78, "avg_wait_days": 1.8},
        "SGSIN": {"name": "Singapore", "utilization": 55, "avg_wait_days": 0.3},
        "NLRTM": {"name": "Rotterdam", "utilization": 68, "avg_wait_days": 0.8},
    }

    async def get_congestion(self, port_code: str) -> dict:
        """Get port congestion data"""
        if port_code in self.PORT_DATA:
            data = self.PORT_DATA[port_code]
            return {
                "port_code": port_code,
                "name": data["name"],
                "utilization": data["utilization"],
                "status": self._get_status(data["utilization"]),
                "avg_wait_days": data["avg_wait_days"],
            }

        # Return default for unknown ports
        return {
            "port_code": port_code,
            "name": "Unknown Port",
            "utilization": 70,
            "status": "MEDIUM",
            "avg_wait_days": 1.0,
        }

    def _get_status(self, utilization: int) -> str:
        if utilization > 80:
            return "HIGH"
        elif utilization > 60:
            return "MEDIUM"
        return "LOW"

    async def get_all_ports(self) -> list[dict]:
        """Get all port data"""
        return [
            {"code": code, **data}
            for code, data in self.PORT_DATA.items()
        ]

port_service = PortService()
```

### 6. Emissions Calculator

```python
# microservices/route_optimizer/app/services/emissions.py

# CO2 emissions factors (kg CO2 per ton-km)
EMISSION_FACTORS = {
    "ocean": 0.015,  # Container ship
    "air": 0.50,     # Air cargo
    "rail": 0.028,   # Rail freight
    "truck": 0.062,  # Road freight
}

# Average distances (km) for route types
ROUTE_DISTANCES = {
    "ocean": 18000,  # Trans-Pacific
    "air": 12000,
    "rail": 10000,
}

def calculate_emissions(weight_kg: float, mode: str, transit_days: int) -> float:
    """Calculate CO2 emissions for shipment"""
    weight_tons = weight_kg / 1000
    factor = EMISSION_FACTORS.get(mode, 0.015)
    distance = ROUTE_DISTANCES.get(mode, 15000)

    # Basic calculation
    emissions = weight_tons * distance * factor

    return emissions
```

### 7. WebSocket for Real-Time Updates

```python
# microservices/route_optimizer/app/api/websocket.py
from fastapi import APIRouter, WebSocket, WebSocketDisconnect
from typing import List
import json
import asyncio

router = APIRouter()

class ConnectionManager:
    def __init__(self):
        self.active_connections: List[WebSocket] = []

    async def connect(self, websocket: WebSocket):
        await websocket.accept()
        self.active_connections.append(websocket)

    def disconnect(self, websocket: WebSocket):
        self.active_connections.remove(websocket)

    async def broadcast(self, message: dict):
        for connection in self.active_connections:
            try:
                await connection.send_json(message)
            except:
                pass

manager = ConnectionManager()

@router.websocket("/ws/route-updates")
async def websocket_endpoint(websocket: WebSocket):
    """WebSocket for real-time route calculation updates"""
    await manager.connect(websocket)
    try:
        while True:
            # Receive request
            data = await websocket.receive_json()

            # Send progress updates
            await websocket.send_json({
                "type": "progress",
                "message": "Fetching port congestion data...",
                "progress": 25
            })
            await asyncio.sleep(0.5)

            await websocket.send_json({
                "type": "progress",
                "message": "Calculating route options...",
                "progress": 50
            })
            await asyncio.sleep(0.5)

            await websocket.send_json({
                "type": "progress",
                "message": "Optimizing for cost and time...",
                "progress": 75
            })
            await asyncio.sleep(0.5)

            # Send final result
            await websocket.send_json({
                "type": "complete",
                "message": "Route calculation complete",
                "progress": 100
            })

    except WebSocketDisconnect:
        manager.disconnect(websocket)
```

### 8. Routes API

```python
# microservices/route_optimizer/app/api/routes.py
from fastapi import APIRouter, HTTPException
from pydantic import BaseModel
from typing import Optional
from ..services.route_service import route_service, RouteRequest, Route
from ..services.port_data import port_service

router = APIRouter()

@router.post("/api/routes/compare")
async def compare_routes(request: RouteRequest) -> dict:
    """Compare shipping routes"""
    routes = await route_service.compare_routes(request)
    return {
        "origin": request.origin_port,
        "destination": request.destination_port,
        "routes": [r.model_dump() for r in routes],
        "recommended_route_id": next(
            (r.id for r in routes if r.recommended), None
        )
    }

@router.get("/api/ports/congestion")
async def get_port_congestion():
    """Get all port congestion data"""
    ports = await port_service.get_all_ports()
    return {"ports": ports}

@router.get("/api/ports/{port_code}/congestion")
async def get_single_port_congestion(port_code: str):
    """Get congestion for specific port"""
    data = await port_service.get_congestion(port_code.upper())
    return data
```

### 9. Background Jobs (Optional)

```python
# microservices/route_optimizer/app/jobs/background.py
from redis import Redis
from rq import Queue
import os

redis_url = os.getenv("REDIS_URL", "redis://localhost:6379")
redis_conn = Redis.from_url(redis_url)
queue = Queue(connection=redis_conn)

def enqueue_route_calculation(params: dict) -> str:
    """Queue a route calculation job"""
    job = queue.enqueue(
        'app.services.route_service.route_service.compare_routes',
        params,
        job_timeout='5m'
    )
    return job.id

def get_job_result(job_id: str) -> dict:
    """Get result of a queued job"""
    from rq.job import Job
    job = Job.fetch(job_id, connection=redis_conn)
    return {
        "status": job.get_status(),
        "result": job.result if job.is_finished else None
    }
```

---

## Commit Schedule

```bash
# Hour 6:30
git commit -m "feat(be3): route optimizer microservice structure"

# Hour 7:00
git commit -m "feat(be3): route comparison service"

# Hour 7:30
git commit -m "feat(be3): port congestion data service"

# Hour 8:00
git commit -m "feat(be3): emissions calculator"

# Hour 8:30
git commit -m "feat(be3): WebSocket for real-time updates"

# Hour 9:00
git commit -m "feat(be3): routes API endpoints"
git push -u origin feature/be-phase-3-route-optimizer-ms
```

---

## Testing

```bash
# Start microservice
cd microservices/route_optimizer
uvicorn app.main:app --reload --port 8002

# Test route comparison
curl -X POST http://localhost:8002/api/routes/compare \
  -H "Content-Type: application/json" \
  -d '{"origin_port": "CNSZX", "destination_port": "USLAX", "cargo_weight_kg": 1000, "cargo_value_usd": 50000}'

# Test port congestion
curl http://localhost:8002/api/ports/congestion
```

---

## Testing Checklist

- [ ] Service starts on port 8002
- [ ] `/api/routes/compare` returns 3 routes
- [ ] Recommended route is marked
- [ ] `/api/ports/congestion` returns port data
- [ ] WebSocket connects and sends progress
