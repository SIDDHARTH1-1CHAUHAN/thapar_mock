# Backend Phase 4: Integration APIs (Hours 9-12)
## Branch: `feature/be-phase-4-integration-apis`

---

## AI/ML IN THIS PHASE

```
┌─────────────────────────────────────────────────────────┐
│  AI ASSISTANT (Hour 10-11)                              │
│  ─────────────────────────                              │
│  • Uses Groq API (same as classification)              │
│  • Different prompt (conversational, not classification)│
│  • Handles trade questions, document queries           │
│  • Reuses llm infrastructure from Phase 2              │
└─────────────────────────────────────────────────────────┘

The AI Assistant uses the SAME Groq API key from Phase 2.
No new AI setup required - just different prompts!
```

---

## Prerequisites
- Phases BE-1, BE-2, BE-3 must be merged
- All microservices running
- Groq API key in .env (from Phase 2)

---

## Your Mission

Complete the main backend with Landed Cost, Compliance, AI Assistant APIs, and health monitoring.

---

## Deliverables

### 1. Add to Main Backend Structure

```
backend/app/
├── api/v1/
│   ├── landed_cost.py      # NEW
│   ├── compliance.py       # NEW
│   ├── assistant.py        # NEW
│   ├── cargo.py            # NEW - Cargo Tracking
│   ├── analytics.py        # NEW - Analytics Dashboard
│   └── monitoring.py       # NEW
├── services/
│   ├── landed_cost_service.py
│   ├── compliance_service.py
│   ├── assistant_service.py
│   ├── cargo_service.py    # NEW - Cargo Tracking
│   ├── analytics_service.py # NEW - Analytics
│   └── live_data_service.py
└── utils/
    └── constants.py
```

### 2. Landed Cost Service

```python
# backend/app/services/landed_cost_service.py
from pydantic import BaseModel
from typing import Optional
import httpx

class LandedCostRequest(BaseModel):
    hs_code: str
    product_value: float
    quantity: int
    origin_country: str
    destination_country: str
    shipping_mode: str = "ocean"
    incoterm: str = "FOB"

class LandedCostResult(BaseModel):
    total_landed_cost: float
    cost_per_unit: float
    breakdown: dict
    effective_duty_rate: float
    warnings: list[str]

class LandedCostService:
    """Calculate landed cost with real duty rates"""

    # Section 301 tariff rates (China -> US)
    SECTION_301_RATES = {
        "8504": 0.25,  # Static converters
        "8518": 0.25,  # Headphones
        "8471": 0.25,  # Computers
        "9503": 0.25,  # Toys
        "6110": 0.075, # Sweaters
        "default": 0.0,
    }

    # Base MFN duty rates
    MFN_RATES = {
        "8504.40": 0.0,
        "8518.30": 0.0,
        "6110.20": 0.165,
        "7323.93": 0.034,
        "default": 0.05,
    }

    async def calculate(self, request: LandedCostRequest) -> LandedCostResult:
        """Calculate complete landed cost"""
        warnings = []

        # Get duty rates
        hs_prefix_4 = request.hs_code[:4]
        hs_prefix_7 = request.hs_code[:7]

        mfn_rate = self.MFN_RATES.get(hs_prefix_7,
                   self.MFN_RATES.get(hs_prefix_4,
                   self.MFN_RATES["default"]))

        section_301_rate = 0.0
        if request.origin_country == "CN" and request.destination_country == "US":
            section_301_rate = self.SECTION_301_RATES.get(hs_prefix_4,
                               self.SECTION_301_RATES["default"])
            if section_301_rate > 0:
                warnings.append(f"Section 301 tariff applies: {section_301_rate*100:.0f}%")

        # Calculate duties
        base_duty = request.product_value * mfn_rate
        section_301 = request.product_value * section_301_rate

        # Calculate fees
        mpf_rate = 0.003464
        mpf = max(31.67, min(614.35, request.product_value * mpf_rate))

        hmf_rate = 0.00125
        hmf = request.product_value * hmf_rate

        # Estimate shipping costs
        if request.shipping_mode == "ocean":
            freight = 1800 + (request.quantity * 0.5)
        elif request.shipping_mode == "air":
            freight = 5000 + (request.quantity * 2.0)
        else:
            freight = 2500 + (request.quantity * 1.0)

        # Insurance (0.5% of CIF value)
        cif_value = request.product_value + freight
        insurance = cif_value * 0.005

        # Total
        total_duties = base_duty + section_301 + mpf + hmf
        total_freight = freight + insurance
        total_landed = request.product_value + total_duties + total_freight

        return LandedCostResult(
            total_landed_cost=round(total_landed, 2),
            cost_per_unit=round(total_landed / request.quantity, 2),
            breakdown={
                "product_value": request.product_value,
                "base_duty": round(base_duty, 2),
                "base_duty_rate": mfn_rate,
                "section_301": round(section_301, 2),
                "section_301_rate": section_301_rate,
                "mpf": round(mpf, 2),
                "hmf": round(hmf, 2),
                "freight": round(freight, 2),
                "insurance": round(insurance, 2),
                "total_duties": round(total_duties, 2),
                "total_freight": round(total_freight, 2),
            },
            effective_duty_rate=round((total_duties / request.product_value) * 100, 2),
            warnings=warnings,
        )

landed_cost_service = LandedCostService()
```

### 3. Compliance Service

```python
# backend/app/services/compliance_service.py
from pydantic import BaseModel
from typing import Optional
import httpx

class ComplianceRequest(BaseModel):
    hs_code: str
    origin_country: str
    destination_country: str
    supplier_name: Optional[str] = None
    product_description: Optional[str] = None

class ComplianceResult(BaseModel):
    overall_risk_score: int
    risk_level: str
    checks: list[dict]
    required_documents: list[dict]
    warnings: list[str]

class ComplianceService:
    """Trade compliance checking service"""

    # OFAC SDN list (sample - use real API in production)
    SAMPLE_SDN_ENTITIES = [
        "IRAN SHIPPING LINES",
        "NORTH KOREA TRADING CO",
        "BLOCKED ENTITY LLC",
    ]

    # HS codes requiring special documentation
    SPECIAL_REQUIREMENTS = {
        "8504": ["UN38.3 Test Summary", "MSDS", "Battery Declaration"],
        "8506": ["UN38.3 Test Summary", "MSDS"],
        "8471": ["FCC Declaration"],
        "2825": ["EPA Certificate"],
    }

    async def check_compliance(self, request: ComplianceRequest) -> ComplianceResult:
        """Run compliance checks"""
        checks = []
        warnings = []
        risk_score = 100

        # OFAC Screening
        ofac_result = await self._check_ofac(request.supplier_name)
        checks.append(ofac_result)
        if ofac_result["status"] != "CLEAR":
            risk_score -= 50

        # Section 301 check
        if request.origin_country == "CN" and request.destination_country == "US":
            checks.append({
                "category": "Section 301 Tariffs",
                "status": "WARNING",
                "details": "Product may be subject to additional tariffs",
                "action_required": "Verify tariff rate for HS code"
            })
            risk_score -= 10
            warnings.append("Section 301 tariffs may apply")

        # Special documentation requirements
        hs_prefix = request.hs_code[:4]
        if hs_prefix in self.SPECIAL_REQUIREMENTS:
            checks.append({
                "category": "Special Documentation",
                "status": "ACTION_REQUIRED",
                "details": f"Additional documents required for HS {hs_prefix}",
                "documents": self.SPECIAL_REQUIREMENTS[hs_prefix]
            })
            risk_score -= 15

        # Determine risk level
        if risk_score >= 80:
            risk_level = "LOW"
        elif risk_score >= 50:
            risk_level = "MEDIUM"
        else:
            risk_level = "HIGH"

        # Required documents
        required_docs = [
            {"name": "Commercial Invoice", "status": "required"},
            {"name": "Bill of Lading", "status": "required"},
            {"name": "Packing List", "status": "required"},
            {"name": "Certificate of Origin", "status": "recommended"},
        ]

        # Add special requirements
        if hs_prefix in self.SPECIAL_REQUIREMENTS:
            for doc in self.SPECIAL_REQUIREMENTS[hs_prefix]:
                required_docs.append({"name": doc, "status": "required"})

        return ComplianceResult(
            overall_risk_score=max(0, risk_score),
            risk_level=risk_level,
            checks=checks,
            required_documents=required_docs,
            warnings=warnings,
        )

    async def _check_ofac(self, entity_name: Optional[str]) -> dict:
        """Check entity against OFAC SDN list"""
        if not entity_name:
            return {
                "category": "OFAC Sanctions",
                "status": "SKIPPED",
                "details": "No supplier name provided"
            }

        # Simple check (use real OFAC API in production)
        entity_upper = entity_name.upper()
        for sdn in self.SAMPLE_SDN_ENTITIES:
            if sdn in entity_upper:
                return {
                    "category": "OFAC Sanctions",
                    "status": "BLOCKED",
                    "details": f"Entity matches SDN list: {sdn}"
                }

        return {
            "category": "OFAC Sanctions",
            "status": "CLEAR",
            "details": "No matches found in SDN list",
            "checked_entities": [entity_name]
        }

compliance_service = ComplianceService()
```

### 4. AI Assistant Service

```python
# backend/app/services/assistant_service.py
import httpx
import os
from typing import Optional

class AssistantService:
    """AI Trade Assistant using Groq API"""

    def __init__(self):
        self.groq_api_key = os.getenv("GROQ_API_KEY", "")
        self.groq_url = "https://api.groq.com/openai/v1/chat/completions"

    async def chat(self, message: str, context: Optional[list[dict]] = None) -> dict:
        """Send message to AI assistant"""

        system_prompt = """You are an expert trade compliance and customs assistant for TradeOptimize AI.
You help users with:
- HS code classification questions
- Tariff and duty calculations
- Trade compliance requirements
- Shipping and logistics optimization
- Import/export regulations

Be concise and practical. Provide specific HS codes, tariff rates, and document requirements when relevant.
Format responses clearly with bullet points when listing items."""

        messages = [{"role": "system", "content": system_prompt}]

        if context:
            messages.extend(context)

        messages.append({"role": "user", "content": message})

        if self.groq_api_key:
            try:
                async with httpx.AsyncClient() as client:
                    response = await client.post(
                        self.groq_url,
                        headers={
                            "Authorization": f"Bearer {self.groq_api_key}",
                            "Content-Type": "application/json"
                        },
                        json={
                            "model": "llama-3.1-70b-versatile",
                            "messages": messages,
                            "temperature": 0.7,
                            "max_tokens": 1000
                        },
                        timeout=30.0
                    )
                    response.raise_for_status()
                    data = response.json()
                    return {
                        "response": data["choices"][0]["message"]["content"],
                        "suggestions": self._generate_suggestions(message)
                    }
            except Exception as e:
                pass

        # Fallback response
        return {
            "response": f"I understand you're asking about: {message}\n\nFor detailed assistance, please provide more context about your specific product or trade scenario.",
            "suggestions": ["CLASSIFY_PRODUCT", "CALCULATE_COST", "CHECK_COMPLIANCE"]
        }

    def _generate_suggestions(self, message: str) -> list[str]:
        """Generate contextual suggestions"""
        message_lower = message.lower()

        if "classify" in message_lower or "hs code" in message_lower:
            return ["VIEW_CLASSIFICATION", "CALCULATE_DUTY", "CHECK_RULINGS"]
        elif "cost" in message_lower or "duty" in message_lower:
            return ["COMPARE_ROUTES", "VIEW_BREAKDOWN", "EXPORT_REPORT"]
        elif "document" in message_lower or "compliance" in message_lower:
            return ["VIEW_REQUIREMENTS", "CHECK_OFAC", "GENERATE_CHECKLIST"]
        else:
            return ["NEW_CLASSIFICATION", "LANDED_COST", "COMPLIANCE_CHECK"]

assistant_service = AssistantService()
```

### 5. Cargo Tracking Service

```python
# backend/app/services/cargo_service.py
from pydantic import BaseModel
from typing import Optional, List
from datetime import datetime, timedelta
import random
import math

class Position(BaseModel):
    latitude: float
    longitude: float
    location_name: str
    speed_knots: float
    heading: int
    timestamp: datetime

class TimelineEvent(BaseModel):
    status: str
    location: str
    timestamp: datetime
    completed: bool
    current: bool = False
    estimated: bool = False

class CargoAlert(BaseModel):
    type: str  # INFO, WARNING, CRITICAL
    message: str
    timestamp: datetime

class ShipmentDetails(BaseModel):
    container_id: str
    bill_of_lading: str
    vessel: str
    voyage: str
    carrier: str
    origin_port: str
    origin_country: str
    destination_port: str
    destination_country: str
    departed: datetime
    eta: datetime
    cargo_description: str
    hs_code: str
    weight_kg: float
    value_usd: float

class CargoTrackingResult(BaseModel):
    shipment: ShipmentDetails
    current_position: Position
    timeline: List[TimelineEvent]
    alerts: List[CargoAlert]
    progress_percent: int

class CargoService:
    """Cargo tracking service with simulated real-time data"""

    # Demo shipments database
    DEMO_SHIPMENTS = {
        "TCLU1234567": {
            "container_id": "TCLU1234567",
            "bill_of_lading": "COSCO2024SZX0815",
            "vessel": "CSCL Saturn",
            "voyage": "034W",
            "carrier": "COSCO Shipping",
            "origin_port": "Yantian, Shenzhen",
            "origin_country": "CN",
            "destination_port": "Long Beach, CA",
            "destination_country": "US",
            "cargo_description": "Solar Portable Chargers",
            "hs_code": "8504.40.95",
            "weight_kg": 1250,
            "value_usd": 62500,
            "route": [
                {"lat": 22.5431, "lon": 114.0579, "name": "Yantian Port"},
                {"lat": 21.3069, "lon": 121.5, "name": "Taiwan Strait"},
                {"lat": 25.0, "lon": 140.0, "name": "Pacific Ocean"},
                {"lat": 30.0, "lon": -150.0, "name": "Mid-Pacific"},
                {"lat": 33.7701, "lon": -118.1937, "name": "Long Beach Port"},
            ],
            "transit_days": 18,
        },
        "MSCU9876543": {
            "container_id": "MSCU9876543",
            "bill_of_lading": "MSC2024HKG0922",
            "vessel": "MSC Oscar",
            "voyage": "AE7-WEST",
            "carrier": "MSC",
            "origin_port": "Hong Kong",
            "origin_country": "HK",
            "destination_port": "Rotterdam",
            "destination_country": "NL",
            "cargo_description": "Electronics Components",
            "hs_code": "8542.31.00",
            "weight_kg": 2400,
            "value_usd": 185000,
            "route": [
                {"lat": 22.3193, "lon": 114.1694, "name": "Hong Kong Port"},
                {"lat": 10.0, "lon": 100.0, "name": "South China Sea"},
                {"lat": 5.0, "lon": 80.0, "name": "Indian Ocean"},
                {"lat": 12.0, "lon": 45.0, "name": "Gulf of Aden"},
                {"lat": 30.0, "lon": 32.0, "name": "Suez Canal"},
                {"lat": 36.0, "lon": 5.0, "name": "Mediterranean Sea"},
                {"lat": 51.9244, "lon": 4.4777, "name": "Rotterdam Port"},
            ],
            "transit_days": 28,
        },
        "EGLV5551234": {
            "container_id": "EGLV5551234",
            "bill_of_lading": "EVG2024TPE0301",
            "vessel": "Ever Given",
            "voyage": "1234E",
            "carrier": "Evergreen Marine",
            "origin_port": "Kaohsiung",
            "origin_country": "TW",
            "destination_port": "Los Angeles",
            "destination_country": "US",
            "cargo_description": "Semiconductor Equipment",
            "hs_code": "8486.20.00",
            "weight_kg": 890,
            "value_usd": 450000,
            "route": [
                {"lat": 22.6273, "lon": 120.3014, "name": "Kaohsiung Port"},
                {"lat": 28.0, "lon": 140.0, "name": "Pacific Ocean"},
                {"lat": 33.9425, "lon": -118.4081, "name": "Los Angeles Port"},
            ],
            "transit_days": 14,
        },
    }

    async def track_shipment(self, container_id: str) -> Optional[CargoTrackingResult]:
        """Track a shipment by container ID"""

        # Check if it's a demo shipment
        shipment_data = self.DEMO_SHIPMENTS.get(container_id.upper())

        if not shipment_data:
            # Generate random shipment for any container ID
            shipment_data = self._generate_random_shipment(container_id)

        # Calculate current position based on simulated transit
        departed = datetime.utcnow() - timedelta(days=random.randint(5, 15))
        eta = departed + timedelta(days=shipment_data["transit_days"])

        progress = self._calculate_progress(departed, eta)
        current_position = self._interpolate_position(shipment_data["route"], progress)

        # Build timeline
        timeline = self._build_timeline(departed, eta, progress, shipment_data)

        # Generate alerts
        alerts = self._generate_alerts(progress, shipment_data)

        return CargoTrackingResult(
            shipment=ShipmentDetails(
                container_id=shipment_data["container_id"],
                bill_of_lading=shipment_data["bill_of_lading"],
                vessel=shipment_data["vessel"],
                voyage=shipment_data["voyage"],
                carrier=shipment_data["carrier"],
                origin_port=shipment_data["origin_port"],
                origin_country=shipment_data["origin_country"],
                destination_port=shipment_data["destination_port"],
                destination_country=shipment_data["destination_country"],
                departed=departed,
                eta=eta,
                cargo_description=shipment_data["cargo_description"],
                hs_code=shipment_data["hs_code"],
                weight_kg=shipment_data["weight_kg"],
                value_usd=shipment_data["value_usd"],
            ),
            current_position=current_position,
            timeline=timeline,
            alerts=alerts,
            progress_percent=int(progress * 100),
        )

    async def get_all_shipments(self) -> List[dict]:
        """Get all tracked shipments (demo data)"""
        shipments = []
        for container_id in self.DEMO_SHIPMENTS.keys():
            result = await self.track_shipment(container_id)
            shipments.append({
                "container_id": result.shipment.container_id,
                "vessel": result.shipment.vessel,
                "origin": result.shipment.origin_port,
                "destination": result.shipment.destination_port,
                "eta": result.shipment.eta.isoformat(),
                "progress_percent": result.progress_percent,
                "status": self._get_status_from_progress(result.progress_percent),
            })
        return shipments

    async def search_shipments(self, query: str) -> List[dict]:
        """Search shipments by container ID, B/L, or vessel"""
        query_upper = query.upper()
        results = []

        for container_id, data in self.DEMO_SHIPMENTS.items():
            if (query_upper in container_id or
                query_upper in data["bill_of_lading"].upper() or
                query_upper in data["vessel"].upper()):
                result = await self.track_shipment(container_id)
                results.append({
                    "container_id": result.shipment.container_id,
                    "bill_of_lading": result.shipment.bill_of_lading,
                    "vessel": result.shipment.vessel,
                    "status": self._get_status_from_progress(result.progress_percent),
                })

        return results

    def _calculate_progress(self, departed: datetime, eta: datetime) -> float:
        """Calculate transit progress (0.0 to 1.0)"""
        now = datetime.utcnow()
        total_duration = (eta - departed).total_seconds()
        elapsed = (now - departed).total_seconds()
        progress = elapsed / total_duration if total_duration > 0 else 0
        return max(0.0, min(1.0, progress))

    def _interpolate_position(self, route: List[dict], progress: float) -> Position:
        """Interpolate current position along route"""
        if progress <= 0:
            point = route[0]
        elif progress >= 1:
            point = route[-1]
        else:
            # Find which segment we're on
            segment_progress = progress * (len(route) - 1)
            segment_index = int(segment_progress)
            segment_fraction = segment_progress - segment_index

            if segment_index >= len(route) - 1:
                point = route[-1]
            else:
                p1 = route[segment_index]
                p2 = route[segment_index + 1]

                # Linear interpolation
                lat = p1["lat"] + (p2["lat"] - p1["lat"]) * segment_fraction
                lon = p1["lon"] + (p2["lon"] - p1["lon"]) * segment_fraction

                # Determine location name
                if segment_fraction < 0.3:
                    name = f"Near {p1['name']}"
                elif segment_fraction > 0.7:
                    name = f"Approaching {p2['name']}"
                else:
                    name = f"Between {p1['name']} and {p2['name']}"

                point = {"lat": lat, "lon": lon, "name": name}

        # Calculate heading (simplified)
        heading = random.randint(0, 359)

        return Position(
            latitude=round(point.get("lat", point["lat"]), 4),
            longitude=round(point.get("lon", point["lon"]), 4),
            location_name=point.get("name", "At Sea"),
            speed_knots=round(random.uniform(14.0, 22.0), 1),
            heading=heading,
            timestamp=datetime.utcnow(),
        )

    def _build_timeline(self, departed: datetime, eta: datetime, progress: float, data: dict) -> List[TimelineEvent]:
        """Build shipment timeline"""
        timeline = []

        # Departed
        timeline.append(TimelineEvent(
            status="DEPARTED_ORIGIN",
            location=data["origin_port"],
            timestamp=departed,
            completed=True,
            current=False,
        ))

        # In transit
        if progress < 1.0:
            timeline.append(TimelineEvent(
                status="IN_TRANSIT",
                location="At Sea",
                timestamp=datetime.utcnow(),
                completed=True,
                current=True,
            ))

        # Arrival
        timeline.append(TimelineEvent(
            status="ARRIVAL_AT_DESTINATION",
            location=data["destination_port"],
            timestamp=eta,
            completed=progress >= 0.95,
            current=progress >= 0.95 and progress < 1.0,
            estimated=progress < 0.95,
        ))

        # Customs clearance
        customs_time = eta + timedelta(hours=24)
        timeline.append(TimelineEvent(
            status="CUSTOMS_CLEARANCE",
            location=data["destination_port"],
            timestamp=customs_time,
            completed=False,
            estimated=True,
        ))

        # Delivery
        delivery_time = eta + timedelta(days=3)
        timeline.append(TimelineEvent(
            status="DELIVERED",
            location=f"{data['destination_port']} Warehouse",
            timestamp=delivery_time,
            completed=False,
            estimated=True,
        ))

        return timeline

    def _generate_alerts(self, progress: float, data: dict) -> List[CargoAlert]:
        """Generate shipment alerts"""
        alerts = []

        # On schedule alert
        alerts.append(CargoAlert(
            type="INFO",
            message="Vessel on schedule - ETA unchanged",
            timestamp=datetime.utcnow() - timedelta(hours=6),
        ))

        # Random port congestion warning
        if random.random() > 0.6:
            alerts.append(CargoAlert(
                type="WARNING",
                message=f"Port of {data['destination_port'].split(',')[0]} congestion expected - possible 1-day delay",
                timestamp=datetime.utcnow() - timedelta(hours=18),
            ))

        # Weather alert for mid-transit
        if 0.3 < progress < 0.7 and random.random() > 0.7:
            alerts.append(CargoAlert(
                type="WARNING",
                message="Weather advisory: Rough seas expected, minor delay possible",
                timestamp=datetime.utcnow() - timedelta(hours=12),
            ))

        return alerts

    def _get_status_from_progress(self, progress_percent: int) -> str:
        """Get status string from progress percentage"""
        if progress_percent < 5:
            return "DEPARTED"
        elif progress_percent < 95:
            return "IN_TRANSIT"
        elif progress_percent < 100:
            return "ARRIVING"
        else:
            return "ARRIVED"

    def _generate_random_shipment(self, container_id: str) -> dict:
        """Generate random shipment data for unknown container IDs"""
        carriers = ["COSCO", "MSC", "Maersk", "Evergreen", "CMA CGM"]
        vessels = ["Ocean Star", "Pacific Voyager", "Atlantic Express", "Global Carrier", "Sea Fortune"]

        return {
            "container_id": container_id.upper(),
            "bill_of_lading": f"BL{random.randint(100000, 999999)}",
            "vessel": random.choice(vessels),
            "voyage": f"{random.randint(100, 999)}W",
            "carrier": random.choice(carriers),
            "origin_port": "Shanghai",
            "origin_country": "CN",
            "destination_port": "Los Angeles",
            "destination_country": "US",
            "cargo_description": "General Cargo",
            "hs_code": "8471.30.00",
            "weight_kg": random.randint(500, 5000),
            "value_usd": random.randint(10000, 100000),
            "route": [
                {"lat": 31.2304, "lon": 121.4737, "name": "Shanghai Port"},
                {"lat": 30.0, "lon": -150.0, "name": "Pacific Ocean"},
                {"lat": 33.9425, "lon": -118.4081, "name": "Los Angeles Port"},
            ],
            "transit_days": random.randint(14, 25),
        }


cargo_service = CargoService()
```

### 6. Cargo Tracking API Endpoints

```python
# backend/app/api/v1/cargo.py
from fastapi import APIRouter, HTTPException, WebSocket, WebSocketDisconnect
from typing import Optional
import asyncio
import json
from ...services.cargo_service import cargo_service

router = APIRouter(prefix="/cargo", tags=["Cargo Tracking"])

@router.get("/track/{container_id}")
async def track_shipment(container_id: str):
    """Track a shipment by container ID"""
    result = await cargo_service.track_shipment(container_id)
    if not result:
        raise HTTPException(status_code=404, detail="Shipment not found")
    return result

@router.get("/shipments")
async def list_shipments():
    """List all tracked shipments"""
    return await cargo_service.get_all_shipments()

@router.get("/search")
async def search_shipments(q: str):
    """Search shipments by container ID, B/L, or vessel name"""
    if len(q) < 3:
        raise HTTPException(status_code=400, detail="Query must be at least 3 characters")
    return await cargo_service.search_shipments(q)

@router.websocket("/ws/track/{container_id}")
async def websocket_tracking(websocket: WebSocket, container_id: str):
    """Real-time tracking updates via WebSocket"""
    await websocket.accept()

    try:
        while True:
            # Get updated position
            result = await cargo_service.track_shipment(container_id)

            if result:
                # Send position update
                await websocket.send_json({
                    "type": "position_update",
                    "data": {
                        "container_id": result.shipment.container_id,
                        "latitude": result.current_position.latitude,
                        "longitude": result.current_position.longitude,
                        "location_name": result.current_position.location_name,
                        "speed_knots": result.current_position.speed_knots,
                        "heading": result.current_position.heading,
                        "progress_percent": result.progress_percent,
                        "eta": result.shipment.eta.isoformat(),
                        "timestamp": result.current_position.timestamp.isoformat(),
                    }
                })

            # Update every 10 seconds (simulated real-time)
            await asyncio.sleep(10)

    except WebSocketDisconnect:
        pass
    except Exception as e:
        await websocket.close(code=1000)
```

### 7. API Endpoints

```python
# backend/app/api/v1/landed_cost.py
from fastapi import APIRouter
from ...services.landed_cost_service import landed_cost_service, LandedCostRequest

router = APIRouter(prefix="/landed-cost", tags=["Landed Cost"])

@router.post("/calculate")
async def calculate_landed_cost(request: LandedCostRequest):
    return await landed_cost_service.calculate(request)
```

```python
# backend/app/api/v1/compliance.py
from fastapi import APIRouter
from ...services.compliance_service import compliance_service, ComplianceRequest

router = APIRouter(prefix="/compliance", tags=["Compliance"])

@router.post("/check")
async def check_compliance(request: ComplianceRequest):
    return await compliance_service.check_compliance(request)
```

```python
# backend/app/api/v1/assistant.py
from fastapi import APIRouter
from pydantic import BaseModel
from typing import Optional
from ...services.assistant_service import assistant_service

router = APIRouter(prefix="/assistant", tags=["AI Assistant"])

class ChatRequest(BaseModel):
    message: str
    context: Optional[list[dict]] = None

@router.post("/chat")
async def chat(request: ChatRequest):
    return await assistant_service.chat(request.message, request.context)
```

### 6. Health Monitoring

```python
# backend/app/api/v1/monitoring.py
from fastapi import APIRouter
import httpx
import psutil
from datetime import datetime

router = APIRouter(prefix="/monitoring", tags=["Monitoring"])

@router.get("/health/detailed")
async def detailed_health():
    services = {
        "main_backend": True,
        "hs_classifier": await check_service("http://localhost:8001/health"),
        "route_optimizer": await check_service("http://localhost:8002/health"),
    }

    metrics = {
        "cpu_percent": psutil.cpu_percent(),
        "memory_percent": psutil.virtual_memory().percent,
        "timestamp": datetime.utcnow().isoformat()
    }

    return {
        "status": "healthy" if all(services.values()) else "degraded",
        "services": services,
        "metrics": metrics
    }

async def check_service(url: str) -> bool:
    try:
        async with httpx.AsyncClient() as client:
            r = await client.get(url, timeout=2.0)
            return r.status_code == 200
    except:
        return False

@router.get("/metrics")
async def get_metrics():
    return {
        "cpu": psutil.cpu_percent(),
        "memory": psutil.virtual_memory()._asdict(),
        "disk": psutil.disk_usage('/')._asdict(),
    }
```

### 8. Analytics Service

```python
# backend/app/services/analytics_service.py
from pydantic import BaseModel
from typing import List, Optional
from datetime import datetime, timedelta
import random

class AnalyticsSummary(BaseModel):
    total_classifications: int
    total_shipments: int
    total_value_usd: float
    duties_paid_usd: float
    duties_saved_usd: float
    compliance_score: float
    period: str

class ChapterBreakdown(BaseModel):
    chapter: str
    count: int
    percentage: float

class TradeRoute(BaseModel):
    route: str
    shipments: int
    value: float

class MonthlyTrend(BaseModel):
    month: str
    classifications: int
    shipments: int
    value: float

class AIAccuracy(BaseModel):
    overall: float
    by_confidence: List[dict]

class AnalyticsResult(BaseModel):
    summary: AnalyticsSummary
    classifications_by_chapter: List[ChapterBreakdown]
    top_trade_routes: List[TradeRoute]
    monthly_trend: List[MonthlyTrend]
    ai_accuracy: AIAccuracy
    recent_activity: List[dict]

class AnalyticsService:
    """Analytics and reporting service"""

    async def get_dashboard_analytics(self, period: str = "30d") -> AnalyticsResult:
        """Get comprehensive dashboard analytics"""

        # In production, this would query the database
        # For hackathon demo, we generate realistic mock data

        days = 30 if period == "30d" else 7 if period == "7d" else 90

        # Generate summary
        base_classifications = random.randint(700, 900)
        base_shipments = random.randint(100, 150)
        base_value = random.randint(2000000, 3000000)

        summary = AnalyticsSummary(
            total_classifications=base_classifications,
            total_shipments=base_shipments,
            total_value_usd=base_value,
            duties_paid_usd=round(base_value * 0.08, 2),
            duties_saved_usd=round(base_value * 0.018, 2),
            compliance_score=round(random.uniform(92, 98), 1),
            period=f"Last {days} Days",
        )

        # Classifications by chapter
        chapters = [
            ("Chapter 85 - Electrical", 36.8),
            ("Chapter 84 - Machinery", 23.4),
            ("Chapter 39 - Plastics", 17.1),
            ("Chapter 73 - Iron/Steel", 10.5),
            ("Chapter 94 - Furniture", 6.2),
            ("Other", 6.0),
        ]

        classifications_by_chapter = []
        for chapter, pct in chapters:
            count = int(base_classifications * pct / 100)
            classifications_by_chapter.append(ChapterBreakdown(
                chapter=chapter,
                count=count,
                percentage=pct,
            ))

        # Top trade routes
        top_trade_routes = [
            TradeRoute(route="CN → US", shipments=67, value=1450000),
            TradeRoute(route="VN → US", shipments=23, value=340000),
            TradeRoute(route="IN → US", shipments=18, value=280000),
            TradeRoute(route="DE → US", shipments=16, value=270000),
            TradeRoute(route="TW → US", shipments=12, value=420000),
        ]

        # Monthly trend (last 6 months)
        monthly_trend = []
        months = ["Sep 2025", "Oct 2025", "Nov 2025", "Dec 2025", "Jan 2026", "Feb 2026"]
        for i, month in enumerate(months):
            growth = 1 + (i * 0.05)  # 5% monthly growth
            monthly_trend.append(MonthlyTrend(
                month=month,
                classifications=int(600 * growth),
                shipments=int(80 * growth),
                value=int(1500000 * growth),
            ))

        # AI accuracy metrics
        ai_accuracy = AIAccuracy(
            overall=94.2,
            by_confidence=[
                {"range": "90-100%", "accuracy": 98.7, "count": 512},
                {"range": "80-90%", "accuracy": 91.3, "count": 234},
                {"range": "70-80%", "accuracy": 84.6, "count": 101},
                {"range": "< 70%", "accuracy": 72.1, "count": 53},
            ]
        )

        # Recent activity
        recent_activity = [
            {
                "type": "classification",
                "description": "Solar Portable Charger classified as 8504.40.95",
                "confidence": 94,
                "timestamp": (datetime.utcnow() - timedelta(minutes=15)).isoformat(),
            },
            {
                "type": "shipment",
                "description": "Container TCLU1234567 departed Yantian",
                "status": "IN_TRANSIT",
                "timestamp": (datetime.utcnow() - timedelta(hours=2)).isoformat(),
            },
            {
                "type": "compliance",
                "description": "Compliance check passed for Shenzhen Tech Co",
                "risk_level": "LOW",
                "timestamp": (datetime.utcnow() - timedelta(hours=4)).isoformat(),
            },
            {
                "type": "landed_cost",
                "description": "Landed cost calculated: $81,634 for 5000 units",
                "savings": "$2,340",
                "timestamp": (datetime.utcnow() - timedelta(hours=6)).isoformat(),
            },
            {
                "type": "classification",
                "description": "Wireless Earbuds classified as 8518.30.20",
                "confidence": 97,
                "timestamp": (datetime.utcnow() - timedelta(hours=8)).isoformat(),
            },
        ]

        return AnalyticsResult(
            summary=summary,
            classifications_by_chapter=classifications_by_chapter,
            top_trade_routes=top_trade_routes,
            monthly_trend=monthly_trend,
            ai_accuracy=ai_accuracy,
            recent_activity=recent_activity,
        )

    async def get_classification_stats(self) -> dict:
        """Get classification-specific statistics"""
        return {
            "total_today": random.randint(20, 50),
            "total_week": random.randint(150, 250),
            "total_month": random.randint(700, 900),
            "avg_confidence": round(random.uniform(88, 95), 1),
            "avg_processing_time_ms": random.randint(1800, 2500),
            "cache_hit_rate": round(random.uniform(35, 55), 1),
            "top_chapters": [
                {"chapter": "85", "name": "Electrical", "count": 312},
                {"chapter": "84", "name": "Machinery", "count": 198},
                {"chapter": "39", "name": "Plastics", "count": 145},
            ],
        }

    async def get_cost_savings_report(self) -> dict:
        """Get cost savings and optimization report"""
        return {
            "period": "Last 30 Days",
            "total_imports_value": 2340000,
            "total_duties_paid": 187200,
            "optimized_duties": 144850,
            "total_savings": 42350,
            "savings_percentage": 22.6,
            "optimization_methods": [
                {"method": "FTA Utilization", "savings": 18500, "count": 23},
                {"method": "HS Code Optimization", "savings": 12400, "count": 45},
                {"method": "Route Optimization", "savings": 8200, "count": 31},
                {"method": "Duty Drawback", "savings": 3250, "count": 8},
            ],
            "recommendations": [
                "Consider Vietnam sourcing for Chapter 61-62 products",
                "Apply for C-TPAT certification for reduced inspections",
                "Utilize Foreign Trade Zone for deferred duties",
            ],
        }

    async def export_report(self, format: str = "json") -> dict:
        """Export analytics report"""
        analytics = await self.get_dashboard_analytics("30d")
        cost_report = await self.get_cost_savings_report()

        return {
            "generated_at": datetime.utcnow().isoformat(),
            "format": format,
            "analytics": analytics.dict(),
            "cost_savings": cost_report,
            "export_note": "Full PDF/Excel export available in frontend",
        }


analytics_service = AnalyticsService()
```

### 9. Analytics API Endpoints

```python
# backend/app/api/v1/analytics.py
from fastapi import APIRouter, Query
from ...services.analytics_service import analytics_service

router = APIRouter(prefix="/analytics", tags=["Analytics"])

@router.get("/dashboard")
async def get_dashboard(period: str = Query("30d", regex="^(7d|30d|90d)$")):
    """Get dashboard analytics summary"""
    return await analytics_service.get_dashboard_analytics(period)

@router.get("/classifications")
async def get_classification_stats():
    """Get classification statistics"""
    return await analytics_service.get_classification_stats()

@router.get("/cost-savings")
async def get_cost_savings():
    """Get cost savings and optimization report"""
    return await analytics_service.get_cost_savings_report()

@router.get("/export")
async def export_report(format: str = Query("json", regex="^(json|csv)$")):
    """Export analytics report"""
    return await analytics_service.export_report(format)

@router.get("/summary")
async def get_quick_summary():
    """Get quick summary for sidebar/header display"""
    analytics = await analytics_service.get_dashboard_analytics("30d")
    return {
        "classifications_today": 47,
        "active_shipments": 12,
        "compliance_score": analytics.summary.compliance_score,
        "duties_saved_mtd": analytics.summary.duties_saved_usd,
    }
```

### 10. Update Main Router

```python
# backend/app/api/v1/router.py
from fastapi import APIRouter
from .health import router as health_router
from .landed_cost import router as landed_cost_router
from .compliance import router as compliance_router
from .assistant import router as assistant_router
from .cargo import router as cargo_router
from .analytics import router as analytics_router
from .monitoring import router as monitoring_router

api_router = APIRouter()

api_router.include_router(health_router, tags=["Health"])
api_router.include_router(landed_cost_router)
api_router.include_router(compliance_router)
api_router.include_router(assistant_router)
api_router.include_router(cargo_router)
api_router.include_router(analytics_router)
api_router.include_router(monitoring_router)
```

---

## Commit Schedule

```bash
# Hour 9:30
git commit -m "feat(be4): landed cost service and API"

# Hour 10:00
git commit -m "feat(be4): compliance service with OFAC check"

# Hour 10:30
git commit -m "feat(be4): AI assistant service"

# Hour 11:00
git commit -m "feat(be4): cargo tracking service with demo shipments"

# Hour 11:15
git commit -m "feat(be4): cargo tracking API and WebSocket"

# Hour 11:30
git commit -m "feat(be4): analytics service with dashboard data"

# Hour 11:45
git commit -m "feat(be4): health monitoring endpoints"

# Hour 12:00
git commit -m "feat(be4): integrate all routes and final polish"
git push -u origin feature/be-phase-4-integration-apis
```

---

## Testing

```bash
# Test landed cost
curl -X POST http://localhost:8000/api/v1/landed-cost/calculate \
  -H "Content-Type: application/json" \
  -d '{"hs_code": "8504.40.95", "product_value": 50000, "quantity": 1000, "origin_country": "CN", "destination_country": "US"}'

# Test compliance
curl -X POST http://localhost:8000/api/v1/compliance/check \
  -H "Content-Type: application/json" \
  -d '{"hs_code": "8504.40.95", "origin_country": "CN", "destination_country": "US", "supplier_name": "Shenzhen Tech Co"}'

# Test assistant
curl -X POST http://localhost:8000/api/v1/assistant/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "What documents do I need for importing electronics from China?"}'

# Test cargo tracking - single shipment
curl http://localhost:8000/api/v1/cargo/track/TCLU1234567

# Test cargo tracking - list all shipments
curl http://localhost:8000/api/v1/cargo/shipments

# Test cargo tracking - search
curl "http://localhost:8000/api/v1/cargo/search?q=COSCO"

# Test cargo tracking - any container ID (generates random data)
curl http://localhost:8000/api/v1/cargo/track/MYCONTAINER123

# Test analytics - dashboard
curl http://localhost:8000/api/v1/analytics/dashboard?period=30d

# Test analytics - classification stats
curl http://localhost:8000/api/v1/analytics/classifications

# Test analytics - cost savings report
curl http://localhost:8000/api/v1/analytics/cost-savings

# Test analytics - quick summary
curl http://localhost:8000/api/v1/analytics/summary

# Test analytics - export
curl http://localhost:8000/api/v1/analytics/export?format=json
```

### Test WebSocket (using websocat or browser console)
```bash
# Install websocat: cargo install websocat
websocat ws://localhost:8000/api/v1/cargo/ws/track/TCLU1234567

# Or in browser console:
const ws = new WebSocket('ws://localhost:8000/api/v1/cargo/ws/track/TCLU1234567');
ws.onmessage = (e) => console.log(JSON.parse(e.data));
```

---

## Testing Checklist

- [ ] Landed cost calculation works
- [ ] Section 301 warning appears for CN->US
- [ ] Compliance check returns risk score
- [ ] OFAC check works
- [ ] AI assistant responds
- [ ] **Cargo tracking returns shipment details**
- [ ] **Cargo tracking shows current position**
- [ ] **Cargo tracking lists all demo shipments**
- [ ] **Cargo search works**
- [ ] **WebSocket sends position updates**
- [ ] **Analytics dashboard returns summary**
- [ ] **Analytics shows classifications by chapter**
- [ ] **Analytics shows trade routes**
- [ ] **Analytics shows monthly trends**
- [ ] **Cost savings report works**
- [ ] Health monitoring shows all services
- [ ] Swagger docs at `/docs` work

---

**Backend Complete! Coordinate with Frontend for final integration.**
