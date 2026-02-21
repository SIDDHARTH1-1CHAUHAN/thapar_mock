# PRD - Claude Account 2: Backend + Microservices
## TradeOptimize AI | Hackathon Development Track

---

# CRITICAL REFERENCES (READ FIRST!)

| Document | Purpose |
|----------|---------|
| `HACKATHON_MASTER_PLAN.md` | Overall architecture, FREE AI stack, live APIs |
| `HACKATHON_WINNING_STRATEGY.md` | Demo script, Q&A prep, technical talking points |
| `DEMO_MOCK_DATA.md` | Fallback data if APIs fail during demo |
| `PRD_INTEGRATION_FINAL.md` | Final merge and testing phase |

**IMPORTANT**: All AI calls use FREE Groq API (Llama 3 70B) + Ollama fallback. NO paid OpenAI/Claude APIs.

---

# OVERVIEW

**Assigned To**: Claude Pro Account 2 (Developers: Saksham G + Saksham G)
**Focus Areas**: Main Backend + 2 Dedicated Microservices
**Total Phases**: 4 (3 hours each = 12 hours)
**Branch Prefix**: `feature/backend-*` and `feature/microservice-*`

---

# RESPONSIBILITIES SUMMARY

| Component | Description | Priority |
|-----------|-------------|----------|
| Main Backend (FastAPI) | Core API server, business logic | P0 |
| HS Classification Microservice | AI-powered classification | P0 (MICROSERVICE) |
| Route Optimizer Microservice | Route comparison engine | P0 (MICROSERVICE) |
| Database Setup | PostgreSQL + MongoDB + Redis | P0 |
| Landed Cost Engine | Duty/fee calculations | P0 |
| Compliance Engine | Document validation, screening | P1 |
| AI Assistant Backend | Chat with Claude | P1 |

---

# ARCHITECTURE OVERVIEW

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        BACKEND ARCHITECTURE                                 │
└─────────────────────────────────────────────────────────────────────────────┘

                    ┌─────────────────────────────────────┐
                    │        MAIN BACKEND (FastAPI)       │
                    │             Port: 8000              │
                    │                                     │
                    │  ┌─────────────────────────────┐   │
                    │  │ API Routes:                 │   │
                    │  │ • /api/v1/landed-cost      │   │
                    │  │ • /api/v1/compliance       │   │
                    │  │ • /api/v1/assistant        │   │
                    │  │ • /api/v1/users            │   │
                    │  │ • /api/v1/analytics        │   │
                    │  └─────────────────────────────┘   │
                    │                                     │
                    │  Services:                          │
                    │  • LandedCostService                │
                    │  • ComplianceService                │
                    │  • AssistantService                 │
                    │  • UserService                      │
                    └──────────────┬──────────────────────┘
                                   │
         ┌─────────────────────────┼─────────────────────────┐
         │                         │                         │
         ▼                         ▼                         ▼
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│ CLASSIFICATION  │     │ ROUTE OPTIMIZER │     │    DATABASES    │
│  MICROSERVICE   │     │  MICROSERVICE   │     │                 │
│  Port: 8001     │     │  Port: 8002     │     │ PostgreSQL:5432 │
│  (FastAPI)      │     │  (Node.js)      │     │ MongoDB:27017   │
│                 │     │                 │     │ Redis:6379      │
│ • /classify     │     │ • /optimize     │     │                 │
│ • /batch        │     │ • /compare      │     │                 │
│ • /validate     │     │ • /ws/tracking  │     │                 │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

---

# PHASE 1: PROJECT SETUP + DATABASES (Hours 0-3)

## Branch: `feature/backend-foundation`

### Tasks

#### 1.1 Docker Compose Setup

**File**: `docker-compose.yml` (root)

```yaml
version: '3.8'

services:
  # Main Backend
  backend:
    build: ./backend
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://postgres:postgres@postgres:5432/tradeoptimize
      - MONGODB_URL=mongodb://mongo:27017/tradeoptimize
      - REDIS_URL=redis://redis:6379
      - CLAUDE_API_KEY=${CLAUDE_API_KEY}
    depends_on:
      - postgres
      - mongo
      - redis

  # Classification Microservice
  classification-service:
    build: ./services/classification
    ports:
      - "8001:8001"
    environment:
      - CLAUDE_API_KEY=${CLAUDE_API_KEY}
      - PINECONE_API_KEY=${PINECONE_API_KEY}
      - REDIS_URL=redis://redis:6379
    depends_on:
      - redis

  # Route Optimizer Microservice
  route-optimizer:
    build: ./services/route-optimizer
    ports:
      - "8002:8002"
    environment:
      - MONGODB_URL=mongodb://mongo:27017/tradeoptimize
      - REDIS_URL=redis://redis:6379
    depends_on:
      - mongo
      - redis

  # Databases
  postgres:
    image: postgres:15-alpine
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
      - POSTGRES_DB=tradeoptimize
    volumes:
      - postgres_data:/var/lib/postgresql/data

  mongo:
    image: mongo:7
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  postgres_data:
  mongo_data:
```

#### 1.2 Main Backend Project Structure

```
backend/
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── config.py
│   ├── api/
│   │   ├── __init__.py
│   │   ├── deps.py
│   │   └── v1/
│   │       ├── __init__.py
│   │       ├── router.py
│   │       ├── landed_cost.py
│   │       ├── compliance.py
│   │       ├── assistant.py
│   │       └── analytics.py
│   ├── services/
│   │   ├── __init__.py
│   │   ├── landed_cost_service.py
│   │   ├── compliance_service.py
│   │   ├── assistant_service.py
│   │   └── analytics_service.py
│   ├── models/
│   │   ├── __init__.py
│   │   ├── user.py
│   │   ├── classification.py
│   │   ├── shipment.py
│   │   └── compliance.py
│   ├── schemas/
│   │   ├── __init__.py
│   │   ├── landed_cost.py
│   │   ├── compliance.py
│   │   └── assistant.py
│   ├── db/
│   │   ├── __init__.py
│   │   ├── postgres.py
│   │   ├── mongodb.py
│   │   └── redis.py
│   └── utils/
│       ├── __init__.py
│       ├── tariff_data.py
│       └── calculations.py
├── data/
│   ├── hs_codes.json
│   ├── duty_rates.json
│   ├── countries.json
│   └── fta_rules.json
├── Dockerfile
├── requirements.txt
└── pyproject.toml
```

**File**: `backend/app/main.py`
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from app.api.v1.router import api_router
from app.db.postgres import init_db
from app.db.mongodb import init_mongodb
from app.db.redis import init_redis

app = FastAPI(
    title="TradeOptimize AI - Main Backend",
    description="AI-Powered Tariff & Trade Optimization API",
    version="1.0.0"
)

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.on_event("startup")
async def startup():
    await init_db()
    await init_mongodb()
    await init_redis()

app.include_router(api_router, prefix="/api/v1")

@app.get("/health")
async def health():
    return {"status": "healthy", "service": "main-backend"}
```

#### 1.3 Database Schemas

**PostgreSQL Models** (SQLAlchemy):
```python
# backend/app/models/user.py
from sqlalchemy import Column, Integer, String, DateTime, ForeignKey
from sqlalchemy.orm import relationship
from app.db.postgres import Base

class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    email = Column(String, unique=True, index=True)
    company_name = Column(String)
    created_at = Column(DateTime, server_default="now()")

    classifications = relationship("Classification", back_populates="user")

class Classification(Base):
    __tablename__ = "classifications"

    id = Column(Integer, primary_key=True, index=True)
    user_id = Column(Integer, ForeignKey("users.id"))
    product_description = Column(String)
    hs_code = Column(String(10))
    confidence = Column(Integer)
    reasoning = Column(String)
    created_at = Column(DateTime, server_default="now()")

    user = relationship("User", back_populates="classifications")

class LandedCostCalculation(Base):
    __tablename__ = "landed_cost_calculations"

    id = Column(Integer, primary_key=True, index=True)
    user_id = Column(Integer, ForeignKey("users.id"))
    hs_code = Column(String(10))
    origin_country = Column(String(2))
    destination_country = Column(String(2))
    product_value = Column(Integer)  # cents
    total_landed_cost = Column(Integer)  # cents
    created_at = Column(DateTime, server_default="now()")
```

**MongoDB Collections**:
```javascript
// Tariff Data Collection
{
  "hs_code": "8518.30.20",
  "description": "Headphones and earphones",
  "chapter": 85,
  "heading": "8518",
  "subheading": "8518.30",
  "rates": {
    "US": {
      "mfn": 0,
      "col2": 35,
      "section_301": 25,
      "ad_cvd": null
    },
    "EU": {
      "mfn": 2.0,
      "preferential": {}
    }
  },
  "notes": "Chapter 85 covers electrical machinery"
}

// FTA Rules Collection
{
  "agreement": "USMCA",
  "countries": ["US", "MX", "CA"],
  "rules": {
    "8518": {
      "tariff_shift": "CTH from any other heading",
      "rvc": 75,
      "specific_process": null
    }
  }
}

// Carrier Routes Collection
{
  "carrier": "MAERSK",
  "origin_port": "CNSHA",
  "destination_port": "USLAX",
  "transit_days": 14,
  "base_rate_20ft": 1800,
  "base_rate_40ft": 2400,
  "reliability_score": 94,
  "co2_tons_per_teu": 1.2
}
```

#### 1.4 LIVE DATA APIs (All FREE)

**File**: `backend/app/services/live_data_service.py`
```python
"""
LIVE DATA SERVICE - All FREE APIs
Fetches real-time data from official government sources
"""
import httpx
import json
from typing import Optional, Dict, List
from datetime import datetime, timedelta
import redis

class LiveDataService:
    """Fetches real tariff data from official APIs"""

    def __init__(self):
        self.redis = redis.Redis(host='localhost', port=6379, decode_responses=True)
        self.cache_ttl = 3600  # 1 hour cache

    async def get_tariff_rate(self, hs_code: str, country: str = "US") -> Dict:
        """
        Fetch LIVE tariff rates from USITC
        API: https://hts.usitc.gov/api
        Cost: FREE (Official US Government)
        """
        cache_key = f"tariff:{country}:{hs_code}"

        # Check cache first
        cached = self.redis.get(cache_key)
        if cached:
            return json.loads(cached)

        async with httpx.AsyncClient() as client:
            # USITC HTS API - FREE, official
            response = await client.get(
                f"https://hts.usitc.gov/api/search",
                params={
                    "query": hs_code[:6],
                    "queryType": "htsno"
                },
                timeout=10.0
            )

            if response.status_code == 200:
                data = response.json()
                result = self._parse_usitc_response(data, hs_code)

                # Cache the result
                self.redis.setex(cache_key, self.cache_ttl, json.dumps(result))
                return result

        # Fallback to local data if API fails
        return self._get_fallback_rate(hs_code)

    def _parse_usitc_response(self, data: dict, hs_code: str) -> Dict:
        """Parse USITC API response"""
        results = data.get("results", [])
        for item in results:
            if item.get("htsno", "").replace(".", "") == hs_code.replace(".", "")[:8]:
                return {
                    "hs_code": item.get("htsno"),
                    "description": item.get("description"),
                    "mfn_rate": self._parse_rate(item.get("general")),
                    "col2_rate": self._parse_rate(item.get("special")),
                    "unit": item.get("units"),
                    "source": "USITC_LIVE",
                    "fetched_at": datetime.utcnow().isoformat()
                }
        return self._get_fallback_rate(hs_code)

    def _parse_rate(self, rate_str: str) -> float:
        """Parse rate string like '2.5%' to float"""
        if not rate_str:
            return 0.0
        import re
        match = re.search(r'([\d.]+)%', str(rate_str))
        return float(match.group(1)) if match else 0.0

    def _get_fallback_rate(self, hs_code: str) -> Dict:
        """Fallback to local JSON data"""
        with open("data/duty_rates.json") as f:
            rates = json.load(f)
        prefix = hs_code[:7]
        return rates.get(prefix, {"mfn": 0, "section_301": 0})


class ExchangeRateService:
    """
    LIVE Exchange Rates
    API: exchangerate-api.com
    Cost: FREE (1,500 requests/month)
    """

    def __init__(self):
        self.api_key = "YOUR_FREE_API_KEY"  # Get free at exchangerate-api.com
        self.redis = redis.Redis(host='localhost', port=6379, decode_responses=True)

    async def get_rate(self, from_currency: str, to_currency: str) -> float:
        """Get live exchange rate"""
        cache_key = f"fx:{from_currency}:{to_currency}"

        cached = self.redis.get(cache_key)
        if cached:
            return float(cached)

        async with httpx.AsyncClient() as client:
            response = await client.get(
                f"https://v6.exchangerate-api.com/v6/{self.api_key}/pair/{from_currency}/{to_currency}",
                timeout=5.0
            )

            if response.status_code == 200:
                data = response.json()
                rate = data.get("conversion_rate", 1.0)
                # Cache for 5 minutes
                self.redis.setex(cache_key, 300, str(rate))
                return rate

        return 1.0  # Fallback


class ComplianceScreeningService:
    """
    LIVE Compliance Screening
    APIs: OFAC SDN List, BIS Entity List
    Cost: FREE (Official Government Data)
    """

    def __init__(self):
        self.ofac_url = "https://sanctionslistservice.ofac.treas.gov/api/PublicationPreview/exports/SDN.XML"
        self.redis = redis.Redis(host='localhost', port=6379, decode_responses=True)

    async def screen_entity(self, name: str) -> Dict:
        """Screen entity against OFAC SDN list"""
        # Load cached SDN list or fetch fresh
        sdn_list = await self._get_sdn_list()

        # Fuzzy match against list
        matches = self._fuzzy_match(name, sdn_list)

        return {
            "entity": name,
            "screened_at": datetime.utcnow().isoformat(),
            "lists_checked": ["OFAC_SDN", "BIS_ENTITY"],
            "matches": matches,
            "clear": len(matches) == 0,
            "source": "LIVE_OFAC"
        }

    async def _get_sdn_list(self) -> List[str]:
        """Fetch OFAC SDN list (cached daily)"""
        cache_key = "ofac:sdn_list"

        cached = self.redis.get(cache_key)
        if cached:
            return json.loads(cached)

        async with httpx.AsyncClient() as client:
            response = await client.get(
                "https://sanctionslistservice.ofac.treas.gov/api/PublicationPreview/exports/SDN_CSV.ZIP",
                timeout=30.0
            )
            # Parse CSV and extract names
            # (simplified - real impl would unzip and parse)
            names = self._parse_sdn_response(response.content)
            self.redis.setex(cache_key, 86400, json.dumps(names))  # Cache 24h
            return names

    def _fuzzy_match(self, name: str, sdn_list: List[str]) -> List[Dict]:
        """Fuzzy match name against SDN list"""
        from difflib import SequenceMatcher
        matches = []
        name_lower = name.lower()

        for sdn_name in sdn_list:
            ratio = SequenceMatcher(None, name_lower, sdn_name.lower()).ratio()
            if ratio > 0.85:  # 85% similarity threshold
                matches.append({
                    "matched_name": sdn_name,
                    "similarity": round(ratio * 100, 2)
                })

        return matches


class ShippingRatesService:
    """
    LIVE Shipping Rates
    API: Searates API
    Cost: FREE tier (100 requests/day)
    """

    async def get_freight_rate(
        self,
        origin_port: str,
        dest_port: str,
        container_type: str = "20ft"
    ) -> Dict:
        """Get live freight rates"""
        async with httpx.AsyncClient() as client:
            # Searates API (free tier)
            response = await client.get(
                "https://www.searates.com/api/v1/freight/rates",
                params={
                    "origin": origin_port,
                    "destination": dest_port,
                    "container": container_type
                },
                timeout=10.0
            )

            if response.status_code == 200:
                return response.json()

        # Fallback to estimated rates
        return self._estimate_rate(origin_port, dest_port, container_type)

    def _estimate_rate(self, origin: str, dest: str, container: str) -> Dict:
        """Fallback rate estimation"""
        base_rates = {
            ("CNSHA", "USLAX"): {"20ft": 1800, "40ft": 2400},
            ("CNSHA", "USNYC"): {"20ft": 2200, "40ft": 2900},
            ("VNSGN", "USLAX"): {"20ft": 1600, "40ft": 2200},
        }
        rate = base_rates.get((origin, dest), {}).get(container, 2000)
        return {
            "rate": rate,
            "currency": "USD",
            "source": "ESTIMATED",
            "note": "Live API unavailable, using estimates"
        }
```

#### 1.5 Seed Data Files (Fallback)

**File**: `backend/data/duty_rates.json`
```json
{
  "8518.30": {
    "description": "Headphones and earphones",
    "mfn": 0,
    "section_301": 25,
    "ad_cvd": null
  },
  "8517.62": {
    "description": "Machines for reception/transmission",
    "mfn": 0,
    "section_301": 25,
    "ad_cvd": null
  },
  "6109.10": {
    "description": "T-shirts, singlets, cotton",
    "mfn": 16.5,
    "section_301": 7.5,
    "ad_cvd": null
  },
  "8504.40": {
    "description": "Static converters",
    "mfn": 1.5,
    "section_301": 25,
    "ad_cvd": null
  },
  "9503.00": {
    "description": "Toys and models",
    "mfn": 0,
    "section_301": 25,
    "ad_cvd": null
  },
  "6912.00": {
    "description": "Ceramic tableware",
    "mfn": 6.0,
    "section_301": 25,
    "ad_cvd": null
  },
  "8471.30": {
    "description": "Portable digital computers",
    "mfn": 0,
    "section_301": 0,
    "ad_cvd": null
  },
  "8528.72": {
    "description": "Television receivers, color",
    "mfn": 3.9,
    "section_301": 25,
    "ad_cvd": null
  }
}
```

### Git Commit (Phase 1)
```bash
git checkout -b feature/backend-foundation
git add .
git commit -m "feat(backend): project foundation and database setup

- Create Docker Compose with all services
- Set up main FastAPI backend structure
- Define PostgreSQL models (User, Classification, LandedCost)
- Configure MongoDB collections (tariffs, FTA rules, routes)
- Set up Redis for caching and rate limiting
- Add seed data for duty rates and HS codes

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
```

---

# PHASE 2: HS CLASSIFICATION MICROSERVICE (Hours 3-6)

## Branch: `feature/microservice-classification`

### Tasks

#### 2.1 Microservice Structure

```
services/classification/
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── config.py
│   ├── api/
│   │   ├── __init__.py
│   │   └── routes.py
│   ├── services/
│   │   ├── __init__.py
│   │   ├── classifier.py
│   │   ├── claude_service.py
│   │   ├── embedding_service.py
│   │   └── ruling_matcher.py
│   ├── models/
│   │   ├── __init__.py
│   │   └── schemas.py
│   └── utils/
│       ├── __init__.py
│       ├── prompts.py
│       └── preprocessing.py
├── data/
│   ├── hs_descriptions.json
│   └── binding_rulings.json
├── Dockerfile
└── requirements.txt
```

#### 2.2 Classification Service Implementation

**File**: `services/classification/app/main.py`
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from app.api.routes import router

app = FastAPI(
    title="HS Classification Microservice",
    description="AI-Powered HS Code Classification Service",
    version="1.0.0"
)

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)

app.include_router(router, prefix="/api/v1")

@app.get("/health")
async def health():
    return {"status": "healthy", "service": "classification-microservice"}
```

**File**: `services/classification/app/api/routes.py`
```python
from fastapi import APIRouter, HTTPException
from app.models.schemas import ClassifyRequest, ClassifyResponse, BatchClassifyRequest
from app.services.classifier import HSClassifier

router = APIRouter()
classifier = HSClassifier()

@router.post("/classify", response_model=ClassifyResponse)
async def classify_product(request: ClassifyRequest):
    """
    Classify a product into its HS code using AI.
    """
    try:
        result = await classifier.classify(
            description=request.description,
            image_url=request.image_url,
            origin_country=request.origin_country,
            destination_country=request.destination_country
        )
        return result
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@router.post("/batch")
async def batch_classify(request: BatchClassifyRequest):
    """
    Classify multiple products in batch.
    """
    results = []
    for product in request.products:
        result = await classifier.classify(
            description=product.description,
            origin_country=request.origin_country,
            destination_country=request.destination_country
        )
        results.append(result)
    return {"results": results}

@router.post("/validate")
async def validate_classification(hs_code: str, description: str):
    """
    Validate if an existing HS code is correct for a product.
    """
    suggested = await classifier.classify(description=description)
    is_valid = suggested.hs_code[:6] == hs_code[:6]
    return {
        "is_valid": is_valid,
        "suggested_code": suggested.hs_code,
        "confidence": suggested.confidence,
        "reasoning": suggested.reasoning if not is_valid else "Classification matches"
    }
```

**File**: `services/classification/app/services/classifier.py`
```python
import json
from typing import Optional
from app.services.claude_service import ClaudeService
from app.services.ruling_matcher import RulingMatcher
from app.models.schemas import ClassifyResponse
from app.utils.prompts import get_classification_prompt
from app.utils.preprocessing import preprocess_description

class HSClassifier:
    def __init__(self):
        self.claude = ClaudeService()
        self.ruling_matcher = RulingMatcher()
        self._load_hs_data()

    def _load_hs_data(self):
        with open("data/hs_descriptions.json") as f:
            self.hs_data = json.load(f)

    async def classify(
        self,
        description: str,
        image_url: Optional[str] = None,
        origin_country: str = "CN",
        destination_country: str = "US"
    ) -> ClassifyResponse:
        # Preprocess input
        cleaned_description = preprocess_description(description)

        # Get Claude classification
        prompt = get_classification_prompt(
            description=cleaned_description,
            destination=destination_country
        )

        claude_response = await self.claude.classify(prompt, image_url)

        # Find similar rulings
        similar_rulings = await self.ruling_matcher.find_similar(
            description=cleaned_description,
            hs_code=claude_response["hs_code"]
        )

        # Calculate confidence
        confidence = self._calculate_confidence(
            claude_confidence=claude_response["confidence"],
            ruling_matches=similar_rulings
        )

        # Get duty rates
        duty_info = self._get_duty_info(
            hs_code=claude_response["hs_code"],
            origin=origin_country,
            destination=destination_country
        )

        return ClassifyResponse(
            hs_code=claude_response["hs_code"],
            confidence=confidence,
            description=claude_response["description"],
            chapter=claude_response["chapter"],
            heading=claude_response["heading"],
            reasoning=claude_response["reasoning"],
            duty_rate=duty_info["mfn"],
            section_301_rate=duty_info.get("section_301"),
            similar_rulings=[
                {"id": r["id"], "similarity": r["score"]}
                for r in similar_rulings[:3]
            ]
        )

    def _calculate_confidence(
        self,
        claude_confidence: int,
        ruling_matches: list
    ) -> int:
        base_confidence = claude_confidence

        # Boost if we have matching rulings
        if ruling_matches and ruling_matches[0]["score"] > 0.9:
            base_confidence = min(base_confidence + 5, 99)

        return base_confidence

    def _get_duty_info(
        self,
        hs_code: str,
        origin: str,
        destination: str
    ) -> dict:
        hs_prefix = hs_code[:7]  # e.g., "8518.30"
        if hs_prefix in self.hs_data:
            return self.hs_data[hs_prefix]
        return {"mfn": 0, "section_301": 0}
```

**File**: `services/classification/app/services/llm_service.py`

**FREE AI Stack: Groq API (Primary) + Ollama (Fallback)**

```python
# services/classification/app/services/llm_service.py
import httpx
import json
import os
from typing import Optional

class LLMService:
    """
    FREE LLM Service using:
    1. Groq API (FREE: 30 requests/min, Llama 3 70B)
    2. Ollama (FREE: Local, Mistral 7B fallback)
    """

    def __init__(self):
        self.groq_api_key = os.getenv("GROQ_API_KEY")  # FREE tier
        self.groq_url = "https://api.groq.com/openai/v1/chat/completions"
        self.ollama_url = "http://localhost:11434/api/generate"

    async def classify(self, prompt: str, image_url: str = None) -> dict:
        # Try Groq first (FREE, fast, Llama 3 70B)
        try:
            return await self._groq_classify(prompt)
        except Exception as e:
            print(f"Groq failed: {e}, falling back to Ollama")
            # Fallback to local Ollama (Mistral 7B)
            return await self._ollama_classify(prompt)

    async def _groq_classify(self, prompt: str) -> dict:
        """Groq API - FREE tier: 30 req/min, 6000 tokens/min"""
        async with httpx.AsyncClient() as client:
            response = await client.post(
                self.groq_url,
                headers={
                    "Authorization": f"Bearer {self.groq_api_key}",
                    "Content-Type": "application/json"
                },
                json={
                    "model": "llama-3.1-70b-versatile",  # FREE
                    "messages": [
                        {
                            "role": "system",
                            "content": self._get_system_prompt()
                        },
                        {
                            "role": "user",
                            "content": prompt
                        }
                    ],
                    "temperature": 0.1,
                    "max_tokens": 1000,
                    "response_format": {"type": "json_object"}
                },
                timeout=30.0
            )
            response.raise_for_status()
            data = response.json()
            return json.loads(data["choices"][0]["message"]["content"])

    async def _ollama_classify(self, prompt: str) -> dict:
        """Ollama - FREE, runs locally with Mistral 7B"""
        async with httpx.AsyncClient() as client:
            response = await client.post(
                self.ollama_url,
                json={
                    "model": "mistral",  # or "llama2" - both FREE
                    "prompt": f"{self._get_system_prompt()}\n\nUser: {prompt}\n\nRespond with JSON only:",
                    "stream": False,
                    "format": "json"
                },
                timeout=60.0
            )
            response.raise_for_status()
            data = response.json()
            return json.loads(data["response"])

    def _get_system_prompt(self) -> str:
        return """You are an expert customs classification specialist.
Analyze the product and provide HS code classification.
Always respond with valid JSON in this exact format:
{
    "hs_code": "XXXX.XX.XX",
    "confidence": 85,
    "description": "Official HS description",
    "chapter": 85,
    "heading": "8518",
    "reasoning": "Detailed explanation of classification logic using GIR rules..."
}"""


class OCRService:
    """FREE OCR using Tesseract (open source)"""

    def __init__(self):
        import pytesseract
        self.pytesseract = pytesseract

    async def extract_text(self, image_path: str) -> str:
        """Extract text from image using Tesseract OCR"""
        import cv2
        from PIL import Image

        # Read and preprocess image
        img = cv2.imread(image_path)
        gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

        # Adaptive thresholding for better OCR
        thresh = cv2.adaptiveThreshold(
            gray, 255,
            cv2.ADAPTIVE_THRESH_GAUSSIAN_C,
            cv2.THRESH_BINARY, 11, 2
        )

        # Denoise
        denoised = cv2.fastNlMeansDenoising(thresh)

        # OCR with optimized config
        text = self.pytesseract.image_to_string(
            denoised,
            config='--psm 6 --oem 3'  # Assume uniform text block, LSTM engine
        )

        return text

    async def extract_from_pdf(self, pdf_path: str) -> str:
        """Extract text from PDF using pdf2image + Tesseract"""
        from pdf2image import convert_from_path
        import tempfile

        # Convert PDF pages to images
        images = convert_from_path(pdf_path, dpi=300)

        all_text = []
        for i, image in enumerate(images):
            # Save temp image
            with tempfile.NamedTemporaryFile(suffix='.png', delete=False) as tmp:
                image.save(tmp.name, 'PNG')
                text = await self.extract_text(tmp.name)
                all_text.append(text)

        return "\n\n".join(all_text)

    def extract_hs_codes(self, text: str) -> list:
        """Extract HS codes from OCR text using regex"""
        import re
        # Pattern matches: 8518.30.20, 8518.30, 851830
        patterns = [
            r'\b\d{4}\.\d{2}\.\d{2}\b',
            r'\b\d{4}\.\d{2}\b',
            r'\b\d{6,10}\b'
        ]
        hs_codes = []
        for pattern in patterns:
            matches = re.findall(pattern, text)
            hs_codes.extend(matches)
        return list(set(hs_codes))


class ImageClassifier:
    """FREE Image Classification using HuggingFace CLIP"""

    def __init__(self):
        from transformers import CLIPProcessor, CLIPModel
        # FREE - downloads model locally
        self.model = CLIPModel.from_pretrained("openai/clip-vit-base-patch32")
        self.processor = CLIPProcessor.from_pretrained("openai/clip-vit-base-patch32")

    async def get_product_features(self, image_path: str) -> dict:
        """Extract product features from image using CLIP"""
        from PIL import Image

        image = Image.open(image_path)

        # Define category labels for zero-shot classification
        categories = [
            "electronics", "clothing", "toys", "machinery",
            "food products", "chemicals", "textiles", "furniture",
            "vehicles", "medical devices", "sports equipment"
        ]

        materials = [
            "plastic", "metal", "wood", "glass", "fabric",
            "leather", "rubber", "ceramic", "paper"
        ]

        # Process image with categories
        inputs = self.processor(
            text=categories,
            images=image,
            return_tensors="pt",
            padding=True
        )

        outputs = self.model(**inputs)
        probs = outputs.logits_per_image.softmax(dim=1)

        # Get top category
        top_category_idx = probs.argmax().item()
        top_category = categories[top_category_idx]
        confidence = probs[0][top_category_idx].item()

        # Process with materials
        inputs_mat = self.processor(
            text=materials,
            images=image,
            return_tensors="pt",
            padding=True
        )
        outputs_mat = self.model(**inputs_mat)
        probs_mat = outputs_mat.logits_per_image.softmax(dim=1)
        top_material_idx = probs_mat.argmax().item()
        top_material = materials[top_material_idx]

        return {
            "category": top_category,
            "category_confidence": round(confidence * 100, 2),
            "material": top_material,
            "suggested_chapters": self._category_to_chapters(top_category)
        }

    def _category_to_chapters(self, category: str) -> list:
        """Map detected category to likely HS chapters"""
        mapping = {
            "electronics": [84, 85],
            "clothing": [61, 62],
            "toys": [95],
            "machinery": [84],
            "food products": [1, 2, 3, 4, 16, 17, 18, 19, 20, 21],
            "chemicals": [28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38],
            "textiles": [50, 51, 52, 53, 54, 55, 56, 57, 58, 59, 60],
            "furniture": [94],
            "vehicles": [87],
            "medical devices": [90],
            "sports equipment": [95]
        }
        return mapping.get(category, [])
```

**requirements.txt for Classification Microservice**:
```
fastapi==0.109.0
uvicorn==0.27.0
httpx==0.26.0
pytesseract==0.3.10
opencv-python==4.9.0.80
Pillow==10.2.0
pdf2image==1.17.0
transformers==4.37.0
torch==2.1.2
chromadb==0.4.22
sentence-transformers==2.3.1
python-multipart==0.0.6
```

**File**: `services/classification/app/utils/prompts.py`
```python
def get_classification_prompt(description: str, destination: str = "US") -> str:
    return f"""Classify the following product into its correct HS (Harmonized System) code for import into {destination}.

PRODUCT DESCRIPTION:
{description}

CLASSIFICATION REQUIREMENTS:
1. Determine the correct 6-10 digit HS code
2. Apply General Interpretive Rules (GIR) in order
3. Consider the product's:
   - Primary function/essential character
   - Material composition
   - End use/intended purpose
4. Provide your confidence level (0-100)
5. Explain your reasoning citing relevant GIRs and chapter notes

For {destination} imports, also note:
- Applicable duty rates
- Any Section 301 tariffs (if CN origin)
- Any AD/CVD considerations

Respond with valid JSON only."""
```

### Git Commit (Phase 2)
```bash
git checkout -b feature/microservice-classification
git add .
git commit -m "feat(microservice): HS Classification service with Claude AI

- Create FastAPI microservice on port 8001
- Implement Claude API integration for classification
- Add confidence scoring algorithm
- Create ruling matcher for similar precedents
- Add batch classification endpoint
- Include HS code validation endpoint
- Set up preprocessing and prompt engineering

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
```

---

# PHASE 3: ROUTE OPTIMIZER MICROSERVICE + LANDED COST (Hours 6-9)

## Branch: `feature/microservice-route-optimizer`

### Tasks

#### 3.1 Route Optimizer Microservice Structure

```
services/route-optimizer/
├── src/
│   ├── index.ts
│   ├── config.ts
│   ├── routes/
│   │   ├── index.ts
│   │   └── optimizer.ts
│   ├── services/
│   │   ├── routeOptimizer.ts
│   │   ├── carrierService.ts
│   │   ├── ftaAnalyzer.ts
│   │   └── paretoScoring.ts
│   ├── models/
│   │   └── types.ts
│   ├── data/
│   │   ├── carriers.json
│   │   ├── ports.json
│   │   └── fta-rules.json
│   └── utils/
│       ├── calculations.ts
│       └── graph.ts
├── package.json
├── tsconfig.json
└── Dockerfile
```

#### 3.2 Route Optimizer Implementation

**File**: `services/route-optimizer/src/index.ts`
```typescript
import express from 'express'
import cors from 'cors'
import { WebSocketServer } from 'ws'
import { createServer } from 'http'
import optimizerRoutes from './routes/optimizer'

const app = express()
const server = createServer(app)
const wss = new WebSocketServer({ server, path: '/tracking' })

app.use(cors())
app.use(express.json())

app.use('/api/v1', optimizerRoutes)

app.get('/health', (req, res) => {
  res.json({ status: 'healthy', service: 'route-optimizer-microservice' })
})

// WebSocket for live tracking
wss.on('connection', (ws, req) => {
  const trackingId = req.url?.split('/').pop()
  console.log(`Tracking connection: ${trackingId}`)

  // Simulate real-time updates
  const interval = setInterval(() => {
    ws.send(JSON.stringify({
      type: 'position',
      payload: {
        lat: 32.7 + Math.random() * 0.1,
        lng: -130 + Math.random() * 0.5,
        speed: 17 + Math.random() * 3,
        heading: 85 + Math.random() * 10
      }
    }))
  }, 5000)

  ws.on('close', () => clearInterval(interval))
})

const PORT = process.env.PORT || 8002
server.listen(PORT, () => {
  console.log(`Route Optimizer running on port ${PORT}`)
})
```

**File**: `services/route-optimizer/src/routes/optimizer.ts`
```typescript
import { Router } from 'express'
import { RouteOptimizer } from '../services/routeOptimizer'
import { FTAAnalyzer } from '../services/ftaAnalyzer'

const router = Router()
const optimizer = new RouteOptimizer()
const ftaAnalyzer = new FTAAnalyzer()

router.post('/optimize-route', async (req, res) => {
  try {
    const {
      originPort,
      destinationPort,
      hsCode,
      productValue,
      quantity,
      cargoType
    } = req.body

    const routes = await optimizer.findOptimalRoutes({
      origin: originPort,
      destination: destinationPort,
      hsCode,
      productValue,
      quantity,
      cargoType
    })

    res.json({
      baseline: routes.baseline,
      alternatives: routes.alternatives,
      recommendation: routes.recommendation,
      emissionsImpact: routes.emissions
    })
  } catch (error) {
    res.status(500).json({ error: error.message })
  }
})

router.post('/compare-routes', async (req, res) => {
  try {
    const { routes, hsCode, productValue, originCountry, destinationCountry } = req.body

    const comparisons = await Promise.all(
      routes.map(route => optimizer.calculateRouteCost(route, hsCode, productValue))
    )

    // Pareto scoring
    const scored = optimizer.paretoScore(comparisons)

    res.json({
      comparisons: scored,
      recommended: scored[0]
    })
  } catch (error) {
    res.status(500).json({ error: error.message })
  }
})

router.post('/fta-check', async (req, res) => {
  try {
    const { hsCode, originCountry, destinationCountry, transshipment } = req.body

    const eligibility = await ftaAnalyzer.checkEligibility({
      hsCode,
      origin: originCountry,
      destination: destinationCountry,
      transshipmentCountry: transshipment
    })

    res.json(eligibility)
  } catch (error) {
    res.status(500).json({ error: error.message })
  }
})

export default router
```

**File**: `services/route-optimizer/src/services/routeOptimizer.ts`
```typescript
import carriers from '../data/carriers.json'
import ports from '../data/ports.json'
import { ParetoScorer } from './paretoScoring'

interface RouteRequest {
  origin: string
  destination: string
  hsCode: string
  productValue: number
  quantity: number
  cargoType: '20ft' | '40ft' | '40hc' | 'lcl'
}

interface RouteOption {
  carrier: string
  transitDays: number
  cost: number
  reliability: number
  isDirect: boolean
  transshipmentPort?: string
  emissions: number
}

export class RouteOptimizer {
  private scorer = new ParetoScorer()

  async findOptimalRoutes(request: RouteRequest): Promise<{
    baseline: RouteOption
    alternatives: RouteOption[]
    recommendation: RouteOption
    emissions: number
  }> {
    // Get available routes from carriers
    const availableRoutes = this.getAvailableRoutes(
      request.origin,
      request.destination
    )

    // Calculate costs for each route
    const routesWithCosts = availableRoutes.map(route => ({
      ...route,
      cost: this.calculateTotalCost(route, request)
    }))

    // Sort by cost
    routesWithCosts.sort((a, b) => a.cost - b.cost)

    // Get baseline (direct cheapest)
    const baseline = routesWithCosts.find(r => r.isDirect) || routesWithCosts[0]

    // Get alternatives
    const alternatives = routesWithCosts
      .filter(r => r !== baseline)
      .slice(0, 3)

    // Score and recommend
    const allRoutes = [baseline, ...alternatives]
    const scored = this.scorer.score(allRoutes)
    const recommendation = scored[0]

    return {
      baseline,
      alternatives,
      recommendation,
      emissions: this.calculateEmissions(recommendation, request)
    }
  }

  private getAvailableRoutes(origin: string, destination: string): RouteOption[] {
    // Filter carriers that serve this lane
    return carriers
      .filter(c => c.routes.some(r =>
        r.origin === origin && r.destination === destination
      ))
      .map(c => {
        const route = c.routes.find(r =>
          r.origin === origin && r.destination === destination
        )!
        return {
          carrier: c.name,
          transitDays: route.transitDays,
          cost: 0, // Calculated later
          reliability: c.reliabilityScore,
          isDirect: route.isDirect,
          transshipmentPort: route.transshipmentPort,
          emissions: route.co2PerTeu
        }
      })
  }

  private calculateTotalCost(route: RouteOption, request: RouteRequest): number {
    // Base freight rate
    const baseRate = this.getBaseRate(route.carrier, request.cargoType)

    // Add surcharges
    const baf = baseRate * 0.15 // Bunker adjustment
    const caf = baseRate * 0.05 // Currency adjustment
    const thc = 350 // Terminal handling

    // If transshipment, add extra costs
    const transshipmentFee = route.transshipmentPort ? 200 : 0

    return baseRate + baf + caf + thc + transshipmentFee
  }

  private getBaseRate(carrier: string, cargoType: string): number {
    const rates: Record<string, Record<string, number>> = {
      'MAERSK_LINE': { '20ft': 1800, '40ft': 2400, '40hc': 2600, 'lcl': 65 },
      'COSCO_SHIPPING': { '20ft': 1500, '40ft': 2100, '40hc': 2300, 'lcl': 55 },
      'MSC_GLOBAL': { '20ft': 1650, '40ft': 2250, '40hc': 2450, 'lcl': 60 }
    }
    return rates[carrier]?.[cargoType] || 2000
  }

  private calculateEmissions(route: RouteOption, request: RouteRequest): number {
    // CO2 per TEU based on route
    const teus = request.cargoType === 'lcl' ? 0.5 :
                 request.cargoType === '20ft' ? 1 : 2
    return route.emissions * teus
  }

  paretoScore(routes: RouteOption[]): RouteOption[] {
    return this.scorer.score(routes)
  }
}
```

**File**: `services/route-optimizer/src/services/paretoScoring.ts`
```typescript
interface ScoredRoute {
  carrier: string
  transitDays: number
  cost: number
  reliability: number
  score: number
  rank: 'recommended' | 'economy' | 'balanced'
}

export class ParetoScorer {
  private weights = {
    cost: 0.40,
    time: 0.30,
    reliability: 0.20,
    emissions: 0.10
  }

  score(routes: any[]): any[] {
    // Normalize all metrics to 0-100 scale
    const normalized = routes.map(route => ({
      ...route,
      normalizedCost: this.normalize(route.cost, routes.map(r => r.cost), true),
      normalizedTime: this.normalize(route.transitDays, routes.map(r => r.transitDays), true),
      normalizedReliability: route.reliability,
      normalizedEmissions: this.normalize(route.emissions, routes.map(r => r.emissions), true)
    }))

    // Calculate weighted score
    const scored = normalized.map(route => ({
      ...route,
      score:
        route.normalizedCost * this.weights.cost +
        route.normalizedTime * this.weights.time +
        route.normalizedReliability * this.weights.reliability +
        route.normalizedEmissions * this.weights.emissions
    }))

    // Sort by score descending
    scored.sort((a, b) => b.score - a.score)

    // Assign ranks
    return scored.map((route, index) => ({
      ...route,
      rank: index === 0 ? 'recommended' :
            route.cost === Math.min(...scored.map(r => r.cost)) ? 'economy' : 'balanced'
    }))
  }

  private normalize(value: number, allValues: number[], inverse: boolean = false): number {
    const min = Math.min(...allValues)
    const max = Math.max(...allValues)
    if (max === min) return 100

    const normalized = ((value - min) / (max - min)) * 100
    return inverse ? 100 - normalized : normalized
  }
}
```

#### 3.3 Landed Cost Service in Main Backend

**File**: `backend/app/services/landed_cost_service.py`
```python
import json
from typing import Optional
from app.schemas.landed_cost import LandedCostRequest, LandedCostResponse, CostBreakdown

class LandedCostService:
    def __init__(self):
        self._load_data()

    def _load_data(self):
        with open("data/duty_rates.json") as f:
            self.duty_rates = json.load(f)

    async def calculate(self, request: LandedCostRequest) -> LandedCostResponse:
        # Get product value
        product_value = request.unit_price * request.quantity

        # Get duty rates for HS code
        hs_prefix = request.hs_code[:7]  # e.g., "8518.30"
        rates = self.duty_rates.get(hs_prefix, {"mfn": 0, "section_301": 0})

        # Calculate duties
        base_duty = product_value * (rates["mfn"] / 100)
        section_301 = 0
        if request.origin_country == "CN" and request.destination_country == "US":
            section_301 = product_value * (rates.get("section_301", 0) / 100)

        # Calculate fees
        mpf = self._calculate_mpf(product_value)
        hmf = self._calculate_hmf(product_value) if request.shipping_mode.startswith("ocean") else 0

        # Calculate freight
        freight = self._estimate_freight(
            request.shipping_mode,
            request.origin_country,
            request.destination_country
        )

        # Calculate insurance (0.5% of CIF value)
        cif_value = product_value + freight
        insurance = cif_value * 0.005

        # Total landed cost
        total = product_value + base_duty + section_301 + mpf + hmf + freight + insurance

        # Effective duty rate
        total_duties = base_duty + section_301
        effective_rate = (total_duties / product_value) * 100 if product_value > 0 else 0

        return LandedCostResponse(
            total_landed_cost=round(total, 2),
            per_unit_cost=round(total / request.quantity, 2),
            breakdown=CostBreakdown(
                product_value=round(product_value, 2),
                base_duty=round(base_duty, 2),
                section_301=round(section_301, 2),
                mpf=round(mpf, 2),
                hmf=round(hmf, 2),
                freight=round(freight, 2),
                insurance=round(insurance, 2)
            ),
            effective_duty_rate=round(effective_rate, 2),
            risk_advisory=self._get_risk_advisory(request)
        )

    def _calculate_mpf(self, value: float) -> float:
        """Merchandise Processing Fee: 0.3464%, min $27.75, max $538.40"""
        mpf = value * 0.003464
        return max(27.75, min(mpf, 538.40))

    def _calculate_hmf(self, value: float) -> float:
        """Harbor Maintenance Fee: 0.125%"""
        return value * 0.00125

    def _estimate_freight(
        self,
        mode: str,
        origin: str,
        destination: str
    ) -> float:
        """Estimate freight cost based on mode and lane"""
        rates = {
            "ocean_fcl": 1800,
            "ocean_lcl": 640,
            "air": 4200,
            "express": 6500
        }
        return rates.get(mode, 2000)

    def _get_risk_advisory(self, request: LandedCostRequest) -> Optional[str]:
        """Generate risk advisory based on current conditions"""
        advisories = []

        if request.shipping_mode == "ocean_lcl":
            advisories.append(
                "Current LCL congestion at destination port suggests +4 day delay."
            )

        if request.origin_country == "CN":
            advisories.append(
                "Section 301 tariffs apply. Consider alternative sourcing."
            )

        return " ".join(advisories) if advisories else None
```

### Git Commit (Phase 3)
```bash
git add .
git commit -m "feat(microservice): Route Optimizer + Landed Cost Engine

- Create Node.js Route Optimizer microservice on port 8002
- Implement Pareto-optimal scoring for routes
- Add WebSocket for live cargo tracking
- Build FTA eligibility analyzer
- Implement Landed Cost calculation service
- Add MPF, HMF, Section 301 calculations
- Create carrier data with rates and reliability scores

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
```

---

# PHASE 4: COMPLIANCE + AI ASSISTANT + INTEGRATION (Hours 9-12)

## Branch: `feature/backend-complete`

### Tasks

#### 4.1 Compliance Service

**File**: `backend/app/services/compliance_service.py`
```python
from typing import List, Dict, Optional
from app.schemas.compliance import (
    ComplianceCheckRequest,
    ComplianceCheckResponse,
    DocumentStatus,
    RestrictionCheck,
    AuditRiskScore
)

class ComplianceService:
    def __init__(self):
        self._load_restricted_items()
        self._load_screening_lists()

    def _load_restricted_items(self):
        self.restricted_items = {
            "8504.40.95": {"type": "ECCN", "description": "Static Converters (Dual Use)", "license": "REQUIRED"},
            "8471.50.01": {"type": "NSA", "description": "Encryption Processing Units", "license": "PENDING"},
            "2805.11.00": {"type": "Strategic", "description": "Lithium Concentrates", "license": "EXEMPT"}
        }

    def _load_screening_lists(self):
        # Simulated denied party list
        self.denied_parties = ["ACME SANCTIONS CO", "BLOCKED ENTITY LLC"]

    async def check_compliance(
        self,
        request: ComplianceCheckRequest
    ) -> ComplianceCheckResponse:
        # Pre-shipment screening
        screening_results = await self._screen_parties(request.parties)

        # Document validation
        document_status = self._validate_documents(request.documents)

        # Check restricted items
        restrictions = self._check_restrictions(request.hs_codes)

        # Calculate audit risk
        audit_risk = self._calculate_audit_risk(
            screening_results,
            document_status,
            restrictions
        )

        # PGA requirements
        pga_requirements = self._get_pga_requirements(request.hs_codes)

        return ComplianceCheckResponse(
            screening_clear=all(r["clear"] for r in screening_results),
            screening_results=screening_results,
            document_status=document_status,
            restrictions=restrictions,
            audit_risk=audit_risk,
            pga_requirements=pga_requirements,
            overall_status=self._calculate_overall_status(
                screening_results, document_status, restrictions
            )
        )

    async def _screen_parties(self, parties: List[str]) -> List[Dict]:
        results = []
        for party in parties:
            is_blocked = party.upper() in self.denied_parties
            results.append({
                "party": party,
                "clear": not is_blocked,
                "lists_checked": ["OFAC", "BIS", "UN", "EU"],
                "match": "BLOCKED" if is_blocked else None
            })
        return results

    def _validate_documents(self, documents: List[Dict]) -> List[DocumentStatus]:
        status_list = []
        required_docs = [
            "commercial_invoice",
            "packing_list",
            "certificate_of_origin",
            "bill_of_lading"
        ]

        provided = {d["type"]: d for d in documents}

        for doc_type in required_docs:
            if doc_type in provided:
                status = "VERIFIED" if provided[doc_type].get("verified") else "PENDING"
            else:
                status = "MISSING"

            status_list.append(DocumentStatus(
                document_type=doc_type,
                status=status,
                required=True
            ))

        return status_list

    def _check_restrictions(self, hs_codes: List[str]) -> List[RestrictionCheck]:
        restrictions = []
        for hs_code in hs_codes:
            if hs_code in self.restricted_items:
                item = self.restricted_items[hs_code]
                restrictions.append(RestrictionCheck(
                    hs_code=hs_code,
                    description=item["description"],
                    restriction_type=item["type"],
                    license_status=item["license"]
                ))
        return restrictions

    def _calculate_audit_risk(
        self,
        screening: List[Dict],
        documents: List[DocumentStatus],
        restrictions: List[RestrictionCheck]
    ) -> AuditRiskScore:
        # Base score starts at 100
        score = 100

        # Deduct for screening issues
        if not all(r["clear"] for r in screening):
            score -= 50

        # Deduct for missing documents
        missing_docs = sum(1 for d in documents if d.status == "MISSING")
        score -= missing_docs * 10

        # Deduct for pending documents
        pending_docs = sum(1 for d in documents if d.status == "PENDING")
        score -= pending_docs * 5

        # Deduct for restrictions requiring license
        required_licenses = sum(1 for r in restrictions if r.license_status == "REQUIRED")
        score -= required_licenses * 15

        return AuditRiskScore(
            score=max(0, score),
            level="LOW" if score >= 80 else "MEDIUM" if score >= 50 else "HIGH",
            factors=[
                f"Screening issues: {sum(1 for r in screening if not r['clear'])}",
                f"Missing documents: {missing_docs}",
                f"Pending licenses: {required_licenses}"
            ]
        )

    def _get_pga_requirements(self, hs_codes: List[str]) -> List[Dict]:
        # Partner Government Agency requirements
        pga_map = {
            "8504": {"agency": "FDA", "notification": "Required for lithium battery components"},
            "2805": {"agency": "EPA", "notification": "TSCA compliance required"},
        }

        requirements = []
        for hs_code in hs_codes:
            chapter = hs_code[:4]
            if chapter in pga_map:
                requirements.append({
                    "hs_code": hs_code,
                    **pga_map[chapter]
                })

        return requirements

    def _calculate_overall_status(
        self,
        screening: List[Dict],
        documents: List[DocumentStatus],
        restrictions: List[RestrictionCheck]
    ) -> str:
        missing = sum(1 for d in documents if d.status == "MISSING")
        total = len(documents)
        completion = ((total - missing) / total) * 100 if total > 0 else 100

        if not all(r["clear"] for r in screening):
            return "BLOCKED"
        elif completion < 50:
            return "NOT_READY"
        elif completion < 100:
            return f"{int(completion)}% READY"
        else:
            return "READY"
```

#### 4.2 AI Assistant Service

**File**: `backend/app/services/assistant_service.py`
```python
import anthropic
from typing import AsyncGenerator, Dict, List
from app.config import settings

class AssistantService:
    def __init__(self):
        self.client = anthropic.Anthropic(api_key=settings.CLAUDE_API_KEY)
        self.system_prompt = """You are an AI trade compliance assistant for TradeOptimize AI.
You help users with:
- HS code classification questions
- Duty and tariff inquiries
- FTA eligibility questions
- Import/export compliance guidance
- Landed cost calculations

You have access to:
- User's recent classifications
- Current product being analyzed
- Real-time tariff data

Always be helpful, accurate, and cite relevant regulations when applicable.
Format HS codes as XXXX.XX.XX and mention confidence levels when classifying."""

    async def chat(
        self,
        message: str,
        context: Dict
    ) -> Dict:
        # Build context-aware prompt
        context_str = self._build_context(context)

        response = self.client.messages.create(
            model="claude-sonnet-4-20250514",
            max_tokens=1000,
            system=self.system_prompt + "\n\n" + context_str,
            messages=[{"role": "user", "content": message}]
        )

        # Extract any HS code predictions from response
        hs_prediction = self._extract_hs_prediction(response.content[0].text)

        return {
            "response": response.content[0].text,
            "hs_prediction": hs_prediction,
            "context_used": list(context.keys())
        }

    async def stream_chat(
        self,
        message: str,
        context: Dict
    ) -> AsyncGenerator[str, None]:
        context_str = self._build_context(context)

        with self.client.messages.stream(
            model="claude-sonnet-4-20250514",
            max_tokens=1000,
            system=self.system_prompt + "\n\n" + context_str,
            messages=[{"role": "user", "content": message}]
        ) as stream:
            for text in stream.text_stream:
                yield text

    def _build_context(self, context: Dict) -> str:
        parts = ["CURRENT CONTEXT:"]

        if "recent_classifications" in context:
            parts.append("\nRecent Classifications:")
            for c in context["recent_classifications"][:5]:
                parts.append(f"- {c['description'][:50]}... → {c['hs_code']}")

        if "current_product" in context:
            parts.append(f"\nCurrent Product: {context['current_product']}")

        if "user_profile" in context:
            profile = context["user_profile"]
            parts.append(f"\nUser: {profile.get('company', 'Unknown Company')}")

        return "\n".join(parts)

    def _extract_hs_prediction(self, text: str) -> Dict:
        import re
        # Look for HS code patterns
        hs_match = re.search(r'(\d{4}\.\d{2}(?:\.\d{2})?)', text)
        if hs_match:
            return {
                "hs_code": hs_match.group(1),
                "found_in_response": True
            }
        return None
```

#### 4.3 API Routes Integration

**File**: `backend/app/api/v1/router.py`
```python
from fastapi import APIRouter
from app.api.v1 import landed_cost, compliance, assistant, analytics

api_router = APIRouter()

api_router.include_router(
    landed_cost.router,
    prefix="/landed-cost",
    tags=["Landed Cost"]
)

api_router.include_router(
    compliance.router,
    prefix="/compliance",
    tags=["Compliance"]
)

api_router.include_router(
    assistant.router,
    prefix="/assistant",
    tags=["AI Assistant"]
)

api_router.include_router(
    analytics.router,
    prefix="/analytics",
    tags=["Analytics"]
)
```

**File**: `backend/app/api/v1/assistant.py`
```python
from fastapi import APIRouter, HTTPException
from fastapi.responses import StreamingResponse
from app.services.assistant_service import AssistantService
from app.schemas.assistant import ChatRequest, ChatResponse

router = APIRouter()
assistant_service = AssistantService()

@router.post("/chat", response_model=ChatResponse)
async def chat(request: ChatRequest):
    try:
        result = await assistant_service.chat(
            message=request.message,
            context=request.context
        )
        return ChatResponse(**result)
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@router.get("/stream")
async def stream_chat(prompt: str):
    async def generate():
        async for chunk in assistant_service.stream_chat(
            message=prompt,
            context={}
        ):
            yield f"data: {chunk}\n\n"
        yield "data: [DONE]\n\n"

    return StreamingResponse(
        generate(),
        media_type="text/event-stream"
    )
```

### Git Commit (Phase 4)
```bash
git add .
git commit -m "feat(backend): Compliance engine, AI Assistant, full integration

- Implement Compliance Service with document validation
- Add denied party screening (OFAC, BIS, UN, EU)
- Create audit risk scoring algorithm
- Build AI Assistant with Claude integration
- Add streaming chat endpoint (SSE)
- Integrate all API routes
- Add PGA requirements checker
- Complete service layer integration

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
```

---

# API SPECIFICATION

## Main Backend (Port 8000)

| Endpoint | Method | Description |
|----------|--------|-------------|
| `GET /health` | GET | Health check |
| `POST /api/v1/landed-cost/calculate` | POST | Calculate landed cost |
| `POST /api/v1/compliance/check` | POST | Run compliance check |
| `POST /api/v1/assistant/chat` | POST | Chat with AI |
| `GET /api/v1/assistant/stream` | GET | Streaming chat |
| `GET /api/v1/analytics/summary` | GET | Get analytics |

## Classification Microservice (Port 8001)

| Endpoint | Method | Description |
|----------|--------|-------------|
| `GET /health` | GET | Health check |
| `POST /api/v1/classify` | POST | Classify product |
| `POST /api/v1/batch` | POST | Batch classify |
| `POST /api/v1/validate` | POST | Validate HS code |

## Route Optimizer Microservice (Port 8002)

| Endpoint | Method | Description |
|----------|--------|-------------|
| `GET /health` | GET | Health check |
| `POST /api/v1/optimize-route` | POST | Find optimal routes |
| `POST /api/v1/compare-routes` | POST | Compare routes |
| `POST /api/v1/fta-check` | POST | Check FTA eligibility |
| `WS /tracking/:id` | WS | Live tracking |

---

# DELIVERABLES CHECKLIST

## Phase 1 (Hours 0-3)
- [ ] Docker Compose with all services
- [ ] Main backend FastAPI structure
- [ ] PostgreSQL models defined
- [ ] MongoDB collections set up
- [ ] Redis configured
- [ ] Seed data files created

## Phase 2 (Hours 3-6)
- [ ] Classification microservice running
- [ ] Claude API integration working
- [ ] Confidence scoring implemented
- [ ] Batch classification endpoint
- [ ] Validation endpoint

## Phase 3 (Hours 6-9)
- [ ] Route optimizer microservice running
- [ ] Pareto scoring algorithm
- [ ] WebSocket tracking working
- [ ] FTA analyzer
- [ ] Landed cost service complete

## Phase 4 (Hours 9-12)
- [ ] Compliance service complete
- [ ] AI Assistant with streaming
- [ ] All routes integrated
- [ ] Health checks working
- [ ] Ready for frontend integration

---

*PRD Version: 1.0*
*Account: Claude Pro Account 2*
*Focus: Backend + Microservices*
