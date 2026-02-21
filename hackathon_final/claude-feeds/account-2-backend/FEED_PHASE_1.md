# Backend Phase 1: Foundation (Hours 0-3)
## Branch: `feature/be-phase-1-foundation`

---

## AI/ML IN THIS PHASE

```
┌─────────────────────────────────────────────────────────┐
│  NO AI IN THIS PHASE - INFRASTRUCTURE ONLY             │
│  ──────────────────────────────────────────            │
│                                                         │
│  This phase sets up:                                    │
│  • FastAPI project structure                           │
│  • Docker Compose for databases                        │
│  • PostgreSQL, MongoDB, Redis connections              │
│  • Health check endpoints                              │
│  • Logging and configuration                           │
│                                                         │
│  AI work starts in Phase 2!                            │
│  Just get the foundation running.                      │
└─────────────────────────────────────────────────────────┘
```

---

## Your Mission

Set up FastAPI project, databases (PostgreSQL, MongoDB, Redis), and core infrastructure.

---

## Deliverables

### 1. Project Structure

```
backend/
├── app/
│   ├── __init__.py
│   ├── main.py                 # FastAPI app
│   ├── core/
│   │   ├── __init__.py
│   │   ├── config.py           # Settings
│   │   ├── database.py         # DB connections
│   │   └── logging.py          # Logging setup
│   ├── models/
│   │   ├── __init__.py
│   │   ├── classification.py
│   │   ├── landed_cost.py
│   │   └── user.py
│   ├── schemas/
│   │   ├── __init__.py
│   │   └── common.py
│   └── api/
│       ├── __init__.py
│       └── v1/
│           ├── __init__.py
│           ├── router.py
│           └── health.py
├── docker-compose.yml
├── requirements.txt
├── .env.example
└── README.md
```

### 2. Requirements.txt

```
fastapi==0.109.0
uvicorn[standard]==0.27.0
pydantic==2.5.3
pydantic-settings==2.1.0
sqlalchemy==2.0.25
asyncpg==0.29.0
motor==3.3.2
redis==5.0.1
httpx==0.26.0
python-multipart==0.0.6
python-jose[cryptography]==3.3.0
passlib[bcrypt]==1.7.4
psutil==5.9.8
```

### 3. Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_USER: tradeopt
      POSTGRES_PASSWORD: tradeopt123
      POSTGRES_DB: tradeoptimize
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  mongodb:
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

### 4. Config

```python
# app/core/config.py
from pydantic_settings import BaseSettings
from functools import lru_cache

class Settings(BaseSettings):
    # App
    APP_NAME: str = "TradeOptimize AI"
    DEBUG: bool = True
    API_V1_PREFIX: str = "/api/v1"

    # Database
    POSTGRES_URL: str = "postgresql+asyncpg://tradeopt:tradeopt123@localhost:5432/tradeoptimize"
    MONGO_URL: str = "mongodb://localhost:27017"
    REDIS_URL: str = "redis://localhost:6379"

    # AI (FREE)
    GROQ_API_KEY: str = ""
    OLLAMA_URL: str = "http://localhost:11434"

    # External APIs
    USITC_API_URL: str = "https://hts.usitc.gov/api"
    EXCHANGE_RATE_API_URL: str = "https://api.exchangerate-api.com/v4/latest/USD"

    # CORS
    CORS_ORIGINS: list[str] = ["http://localhost:3000"]

    class Config:
        env_file = ".env"

@lru_cache()
def get_settings() -> Settings:
    return Settings()
```

### 5. Database Connections

```python
# app/core/database.py
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession
from sqlalchemy.orm import sessionmaker, declarative_base
from motor.motor_asyncio import AsyncIOMotorClient
from redis.asyncio import Redis
from .config import get_settings

settings = get_settings()

# PostgreSQL
engine = create_async_engine(settings.POSTGRES_URL, echo=settings.DEBUG)
AsyncSessionLocal = sessionmaker(engine, class_=AsyncSession, expire_on_commit=False)
Base = declarative_base()

async def get_db():
    async with AsyncSessionLocal() as session:
        yield session

# MongoDB
mongo_client = AsyncIOMotorClient(settings.MONGO_URL)
mongo_db = mongo_client.tradeoptimize

def get_mongo():
    return mongo_db

# Redis
redis_client = Redis.from_url(settings.REDIS_URL, decode_responses=True)

async def get_redis():
    return redis_client
```

### 6. Logging Setup

```python
# app/core/logging.py
import logging
import sys
from datetime import datetime
from pathlib import Path

def setup_logging():
    # Create logs directory
    Path("logs").mkdir(exist_ok=True)

    # Configure logging
    logging.basicConfig(
        level=logging.INFO,
        format='%(asctime)s | %(levelname)s | %(name)s | %(message)s',
        handlers=[
            logging.StreamHandler(sys.stdout),
            logging.FileHandler(f'logs/app_{datetime.now().strftime("%Y%m%d")}.log')
        ]
    )

    # Suppress noisy loggers
    logging.getLogger("httpx").setLevel(logging.WARNING)
    logging.getLogger("uvicorn.access").setLevel(logging.WARNING)

    return logging.getLogger("tradeoptimize")
```

### 7. Main App

```python
# app/main.py
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from contextlib import asynccontextmanager

from .core.config import get_settings
from .core.logging import setup_logging
from .core.database import engine, Base
from .api.v1.router import api_router

settings = get_settings()
logger = setup_logging()

@asynccontextmanager
async def lifespan(app: FastAPI):
    # Startup
    logger.info("Starting TradeOptimize AI Backend...")
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)
    logger.info("Database tables created")
    yield
    # Shutdown
    logger.info("Shutting down...")

app = FastAPI(
    title=settings.APP_NAME,
    version="1.0.0",
    lifespan=lifespan
)

# CORS
app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.CORS_ORIGINS,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Routes
app.include_router(api_router, prefix=settings.API_V1_PREFIX)

@app.get("/")
async def root():
    return {"message": "TradeOptimize AI Backend", "status": "operational"}
```

### 8. Health Check Endpoint

```python
# app/api/v1/health.py
from fastapi import APIRouter, Depends
from ...core.database import get_db, get_redis, get_mongo

router = APIRouter()

@router.get("/health")
async def health_check():
    return {"status": "healthy", "service": "main-backend"}

@router.get("/health/detailed")
async def detailed_health():
    checks = {
        "postgres": await check_postgres(),
        "mongodb": await check_mongo(),
        "redis": await check_redis(),
    }
    all_healthy = all(checks.values())
    return {
        "status": "healthy" if all_healthy else "degraded",
        "checks": checks
    }

async def check_postgres():
    try:
        # Simple query to test connection
        return True
    except:
        return False

async def check_mongo():
    try:
        db = get_mongo()
        await db.command("ping")
        return True
    except:
        return False

async def check_redis():
    try:
        redis = await get_redis()
        await redis.ping()
        return True
    except:
        return False
```

### 9. API Router

```python
# app/api/v1/router.py
from fastapi import APIRouter
from .health import router as health_router

api_router = APIRouter()

api_router.include_router(health_router, tags=["Health"])

# Placeholder routes (implemented in later phases)
@api_router.post("/classify")
async def classify_placeholder():
    return {"message": "Classification service coming in Phase 2"}

@api_router.post("/landed-cost")
async def landed_cost_placeholder():
    return {"message": "Landed cost service coming in Phase 4"}
```

### 10. .env.example

```
# App
DEBUG=true

# Database
POSTGRES_URL=postgresql+asyncpg://tradeopt:tradeopt123@localhost:5432/tradeoptimize
MONGO_URL=mongodb://localhost:27017
REDIS_URL=redis://localhost:6379

# AI (FREE - Get from https://console.groq.com)
GROQ_API_KEY=gsk_xxxxxxxxxxxx

# External APIs
USITC_API_URL=https://hts.usitc.gov/api
```

---

## Commands to Run

```bash
# Start databases
docker-compose up -d

# Install dependencies
pip install -r requirements.txt

# Run server
uvicorn app.main:app --reload --port 8000
```

---

## Commit Schedule

```bash
# Hour 0:30
git commit -m "feat(be1): project structure and requirements"

# Hour 1:00
git commit -m "feat(be1): docker-compose for databases"

# Hour 1:30
git commit -m "feat(be1): config and database connections"

# Hour 2:00
git commit -m "feat(be1): FastAPI main app with CORS"

# Hour 2:30
git commit -m "feat(be1): health check endpoints"

# Hour 3:00
git commit -m "feat(be1): logging setup and .env.example"
git push -u origin feature/be-phase-1-foundation
```

---

## Testing Checklist

- [ ] `docker-compose up -d` starts all databases
- [ ] `uvicorn app.main:app --reload` starts without errors
- [ ] `GET /` returns status message
- [ ] `GET /api/v1/health` returns healthy
- [ ] `GET /api/v1/health/detailed` shows all services

---

## PR Template

```markdown
## Phase BE-1: Backend Foundation

### Changes
- FastAPI project structure
- Docker Compose for PostgreSQL, MongoDB, Redis
- Database connection modules
- Health check endpoints
- Logging configuration
- Environment variables setup

### Testing
```bash
docker-compose up -d
uvicorn app.main:app --reload
curl http://localhost:8000/api/v1/health
```

### Dependencies
None (first backend phase)
```
