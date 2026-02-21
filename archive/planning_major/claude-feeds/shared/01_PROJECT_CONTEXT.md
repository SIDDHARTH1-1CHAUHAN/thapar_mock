# Project Context - TradeOptimize AI
## Feed this FIRST to any Claude conversation

---

## What We're Building

**TradeOptimize AI** - An AI-Powered Tariff & Trade Optimization Platform

**Team**: Scripting_Ninjas (Siddharth, Saksham G, Saksham G) - PEC Chandigarh
**Hackathon**: Makeathon 8 (48 hours)

---

## Core Features (6 Total)

1. **HS Classification** (Microservice) - AI-powered product classification with image/OCR
2. **Landed Cost Calculator** - Duty/fee calculation with live tariff data
3. **Route Optimizer** (Microservice) - Compare shipping routes with Pareto scoring
4. **Trade Compliance** - OFAC screening, document validation
5. **AI Assistant** - Chat interface for trade queries
6. **Cargo Tracking** - Real-time shipment tracking

---

## Tech Stack (ALL FREE)

### Frontend
- Next.js 14 + TypeScript
- Tailwind CSS
- Zustand (state management)
- Recharts (charts)

### Backend
- FastAPI (Python) - Main backend + Microservices
- PostgreSQL + MongoDB + Redis
- Docker Compose

### AI (100% FREE - NO FINE-TUNING NEEDED)
- **Groq API** (FREE): Llama 3.1 70B, 30 req/min, ~800 tokens/sec
- **Ollama** (Local): Mistral 7B fallback
- **Tesseract** (FREE): OCR for document text extraction
- **HuggingFace CLIP** (FREE): Image understanding

**WHY NO MODEL TRAINING?**
- Llama 3.1 70B already knows HS codes, trade regulations, GIR rules
- We use PROMPT ENGINEERING as our "fine-tuning"
- Detailed system prompts with classification rules
- This achieves 90%+ accuracy without any training

**SPEED: 2-3 SECONDS**
- Groq processes at 800 tokens/second
- First request: 2-3 seconds (API call)
- Cached requests: <50ms (Redis)

---

## Groq API Setup (MANDATORY - Do Before Hackathon)

```
STEP 1: Get API Key (2 minutes)
1. Go to https://console.groq.com
2. Sign up with Google (instant approval)
3. Click "API Keys" in sidebar
4. Click "Create API Key"
5. Copy the key (starts with "gsk_")

STEP 2: Test It Works
curl -X POST "https://api.groq.com/openai/v1/chat/completions" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model": "llama-3.1-70b-versatile", "messages": [{"role": "user", "content": "Hello"}]}'

STEP 3: Add to .env
GROQ_API_KEY=gsk_xxxxxxxxxxxx

FREE LIMITS (More than enough!):
- 30 requests per minute
- 6,000 tokens per minute
- 14,400 requests per day
```

## Tesseract OCR Setup (For Image/Document Classification)

```
Windows:
1. Download: https://github.com/UB-Mannheim/tesseract/wiki
2. Install to: C:\Program Files\Tesseract-OCR
3. Add to PATH environment variable
4. Restart terminal

Test: tesseract --version

⚠️ If Tesseract fails during hackathon:
- Text classification still works perfectly
- Image classification will use description instead
- NOT a blocker!
```

### Live Data APIs (FREE & REAL)
- **USITC HTS API**: `https://hts.usitc.gov/api` - Official US tariff rates
- **ExchangeRate-API**: `https://api.exchangerate-api.com` - Real-time FX (1500 free/month)
- **OFAC SDN**: `treasury.gov/ofac` - Sanctions screening (simplified)
- **Section 301 Lists**: Hardcoded from USTR official data

**See `LIVE_DATA_APIS.md` for actual implementation code!**

---

## Architecture Overview

```
Frontend (Next.js :3000)
    │
    ├── HS Classification Microservice (FastAPI :8001)
    ├── Route Optimizer Microservice (FastAPI :8002)
    └── Main Backend (FastAPI :8000)
            │
            ├── PostgreSQL :5432 (users, logs)
            ├── MongoDB :27017 (tariff data, HS codes)
            └── Redis :6379 (cache, rate limiting)
```

---

## Design System (CRITICAL)

### Fonts
- **Space Grotesk**: Body text, UI elements
- **Silkscreen**: Pixel font for HS codes, buttons, headings

### Colors
```css
--bg-canvas: #C8C8C8;    /* Light gray background */
--bg-dark: #080808;       /* Near-black for panels */
--text-main: #000000;     /* Black text */
--text-inv: #E8E8E8;      /* Light text on dark */
--warning: #FF4141;       /* Red for alerts */
```

### Layout
- 3-column grid: `260px sidebar | 1fr workspace | 400px right panel`
- Zero border-radius (brutalist design)
- Uppercase labels with letter-spacing

### Font Loading (Add to layout.tsx)
```html
<link href="https://fonts.googleapis.com/css2?family=Silkscreen&family=Space+Grotesk:wght@400;600;700&display=swap" rel="stylesheet">
```

---

## Coding Standards

### TypeScript/React
- Use functional components with hooks
- Zustand for global state
- React Query for API calls
- All files use `.tsx` extension

### Python/FastAPI
- Async functions with `async def`
- Pydantic models for validation
- Type hints everywhere
- Use `httpx` for async HTTP

### Git Commits
```
feat(phase): description
fix(phase): description
docs(phase): description
```

---

## Environment Variables

### Frontend (.env.local)
```
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_HS_SERVICE_URL=http://localhost:8001
NEXT_PUBLIC_ROUTE_SERVICE_URL=http://localhost:8002
NEXT_PUBLIC_USE_MOCK=false
```

### Backend (.env)
```
GROQ_API_KEY=gsk_xxxx
POSTGRES_URL=postgresql://user:pass@localhost:5432/tradeopt
MONGO_URL=mongodb://localhost:27017
REDIS_URL=redis://localhost:6379
USITC_API_URL=https://hts.usitc.gov/api
```

---

## Key Endpoints

### Main Backend (:8000)
- `POST /api/v1/landed-cost` - Calculate landed cost
- `POST /api/v1/compliance/check` - Run compliance check
- `POST /api/v1/assistant/chat` - AI assistant
- `GET /api/v1/analytics/summary` - Dashboard data

### HS Classification MS (:8001)
- `POST /api/classify` - Classify product
- `POST /api/classify/image` - Classify from image
- `POST /api/classify/ocr` - Extract text from document

### Route Optimizer MS (:8002)
- `POST /api/routes/compare` - Compare routes
- `GET /api/ports/congestion` - Port status
- `WS /ws/route-updates` - Real-time updates

---

## Phase Structure

Each phase is 3 hours and independently mergeable:

| Phase | Frontend | Backend |
|-------|----------|---------|
| 1 | Foundation + Theme | Foundation + DB |
| 2 | Classify + Chat | Classification MS |
| 3 | Cost + Compliance | Route Optimizer MS |
| 4 | Polish + Export | Integration APIs |

---

## Important Notes

1. **No paid APIs** - We only use FREE tiers (Groq, HuggingFace)
2. **Mock data fallback** - If APIs fail, use `DEMO_MOCK_DATA.md`
3. **Commit every 30 mins** - Don't lose work
4. **PR at end of each phase** - Merge to develop branch
5. **Design pixel-perfect** - Follow the design system exactly

---

## Now proceed with your specific phase instructions!
