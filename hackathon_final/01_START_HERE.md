# TradeOptimize AI - Hackathon File Index
## START HERE - Read Files in This Order

---

# BEFORE HACKATHON (Planning Phase)

Read these to understand the project:

| Order | File | Purpose | Time |
|-------|------|---------|------|
| 1 | `problem_statement.md` | Original hackathon challenge | 5 min |
| 2 | `HACKATHON_MASTER_PLAN.md` | Features, USP, business model | 15 min |
| 3 | `GIT_PHASE_WORKFLOW.md` | Branch strategy, PR process | 10 min |
| 4 | `HACKATHON_WINNING_STRATEGY.md` | Demo script, Q&A prep | 10 min |
| 5 | **`GUARANTEED_WIN_ADDITIONS.md`** | WOW factors, judge bait, demo script | 15 min |

---

# DURING HACKATHON (Development Phase)

## For Account 1 - Frontend (Siddharth)

| Phase | Time | Feed to Claude | What You Build |
|-------|------|----------------|----------------|
| Setup | Hour 0 | `claude-feeds/shared/01_PROJECT_CONTEXT.md` | (Context only) |
| | | `claude-feeds/shared/02_DESIGN_SYSTEM.md` | (Components) |
| Phase 1 | Hour 0-3 | `claude-feeds/account-1-frontend/FEED_PHASE_1.md` | Next.js, Layout, Theme |
| Phase 2 | Hour 3-6 | `claude-feeds/account-1-frontend/FEED_PHASE_2.md` | Classify, AI Chat |
| Phase 3 | Hour 6-9 | `claude-feeds/account-1-frontend/FEED_PHASE_3.md` | Landed Cost, Compliance |
| Phase 4 | Hour 9-12 | `claude-feeds/account-1-frontend/FEED_PHASE_4.md` | Routes, Export, Polish |

## For Account 2 - Backend (Saksham x2)

| Phase | Time | Feed to Claude | What You Build |
|-------|------|----------------|----------------|
| Setup | Hour 0 | `claude-feeds/shared/01_PROJECT_CONTEXT.md` | (Context only) |
| Phase 1 | Hour 0-3 | `claude-feeds/account-2-backend/FEED_PHASE_1.md` | FastAPI, DBs |
| Phase 2 | Hour 3-6 | `claude-feeds/account-2-backend/FEED_PHASE_2.md` | HS Classifier MS |
| Phase 3 | Hour 6-9 | `claude-feeds/account-2-backend/FEED_PHASE_3.md` | Route Optimizer MS |
| Phase 4 | Hour 9-12 | `claude-feeds/account-2-backend/FEED_PHASE_4.md` | Integration APIs |
| Bonus | Any | `claude-feeds/account-2-backend/LIVE_DATA_APIS.md` | Real API integrations |

---

# REFERENCE FILES (Use When Needed)

| File | When to Use |
|------|-------------|
| `DESIGN_IMPLEMENTATION_GUIDE.md` | CSS variables, layout patterns |
| `QUICK_COPY_PASTE_COMPONENTS.md` | Ready React components |
| `DEMO_MOCK_DATA.md` | If APIs fail during demo |
| `design/*.md` | Original HTML designs (6 files) |
| **`ML_AI_COMPLETE_GUIDE.md`** | **All AI/ML setup and implementation** |

---

# ML/AI OVERVIEW (Read ML_AI_COMPLETE_GUIDE.md for details)

## AI Components Used

| Component | What It Does | When Built | Phase |
|-----------|--------------|------------|-------|
| **Groq API** | LLM for classification + chat | Hour 3-6 | BE Phase 2 |
| **Tesseract OCR** | Extract text from images | Hour 4-5 | BE Phase 2 |
| **Ollama** | Local LLM fallback | Pre-installed | Backup |
| **Redis Cache** | Cache AI responses | Hour 5-6 | BE Phase 2 |

## AI Features by Module

| Feature | AI Used? | What AI Does |
|---------|----------|--------------|
| HS Classification | **YES** | Groq classifies products, OCR reads images |
| AI Assistant | **YES** | Groq powers conversational chat |
| Landed Cost | No | Uses live USITC API (not AI) |
| Route Optimizer | No | Algorithms, not AI |
| Compliance | No | Rule-based checks |
| Cargo Tracking | No | Real-time position simulation + WebSocket |

## Backend Features Summary

| Feature | Backend Phase | API Endpoints |
|---------|---------------|---------------|
| HS Classification | BE Phase 2 | POST /api/classify, POST /api/classify/image |
| Landed Cost | BE Phase 4 | POST /api/v1/landed-cost/calculate |
| Compliance | BE Phase 4 | POST /api/v1/compliance/check |
| AI Assistant | BE Phase 4 | POST /api/v1/assistant/chat |
| Route Optimizer | BE Phase 3 | POST /api/routes/compare |
| Cargo Tracking | BE Phase 4 | GET /cargo/track/{id}, WS /cargo/ws/track/{id} |
| Analytics | BE Phase 4 | GET /analytics/dashboard, GET /analytics/cost-savings |

## Maps Feature (Cargo + Route)

| Feature | Library | What It Shows |
|---------|---------|---------------|
| **Cargo Tracking Map** | Leaflet (FREE) | Vessel position, route line, ports |
| **Route Optimizer Map** | Leaflet (FREE) | Multiple routes, port congestion |

Maps use **OpenStreetMap tiles** - 100% free, no API key needed!

## AI Timeline

```
BEFORE HACKATHON
├── Get Groq API key: https://console.groq.com (2 min)
├── Install Tesseract OCR (5 min)
└── (Optional) Install Ollama (10 min)

HOUR 3-6 (Backend Phase 2) ← ALL AI WORK HERE
├── Hour 3:30 - Build llm_service.py (Groq integration)
├── Hour 4:00 - Write classification prompts
├── Hour 4:30 - Build ocr_service.py (Tesseract)
├── Hour 5:00 - Build cache_service.py (Redis)
└── Hour 5:30 - Build classify API endpoints

HOUR 9-12 (Backend Phase 4)
└── Hour 10-11 - Build AI Assistant (reuses Groq)
```

---

# COMPLETE FILE LIST

```
D:\thapar-hackathon\
│
├── START_HERE.md                    ← YOU ARE HERE
│
├── ─── PLANNING DOCS ───
├── problem_statement.md             ← Original challenge
├── TARIFF_TRADE_AI_PRD.md          ← Original PRD
├── HACKATHON_MASTER_PLAN.md        ← Our strategy
├── GIT_PHASE_WORKFLOW.md           ← Branch/PR workflow
├── HACKATHON_WINNING_STRATEGY.md   ← Demo script
│
├── ─── PRD DOCS (Detailed) ───
├── PRD_ACCOUNT_1_FRONTEND_AI.md    ← Full frontend spec
├── PRD_ACCOUNT_2_BACKEND_MICROSERVICES.md  ← Full backend spec
├── PRD_INTEGRATION_FINAL.md        ← Final merge/test
│
├── ─── DESIGN DOCS ───
├── DESIGN_IMPLEMENTATION_GUIDE.md  ← CSS tokens, patterns
├── QUICK_COPY_PASTE_COMPONENTS.md  ← React components
├── HACKATHON_FEATURES_FLOWCHARTS.md ← Feature flowcharts
│
├── ─── DEMO/FALLBACK ───
├── DEMO_MOCK_DATA.md               ← Backup data
│
├── ─── CLAUDE FEEDS (COPY-PASTE) ───
├── claude-feeds/
│   ├── README.md                   ← How to use feeds
│   ├── shared/
│   │   ├── 01_PROJECT_CONTEXT.md  ← Feed FIRST
│   │   └── 02_DESIGN_SYSTEM.md    ← Components
│   ├── account-1-frontend/
│   │   ├── FEED_PHASE_1.md
│   │   ├── FEED_PHASE_2.md
│   │   ├── FEED_PHASE_3.md
│   │   └── FEED_PHASE_4.md
│   └── account-2-backend/
│       ├── FEED_PHASE_1.md
│       ├── FEED_PHASE_2.md
│       ├── FEED_PHASE_3.md
│       ├── FEED_PHASE_4.md
│       └── LIVE_DATA_APIS.md      ← Real API code
│
└── ─── ORIGINAL DESIGNS ───
    └── design/
        ├── cp.md                   ← Classify Product
        ├── lc.md                   ← Landed Cost
        ├── ro.md                   ← Route Optimizer
        ├── tc.md                   ← Trade Compliance
        ├── ai_assistant.md         ← AI Chat
        └── cargo.locator.md        ← Cargo Tracking
```

---

# QUICK START CHECKLIST

## Night Before Hackathon
- [ ] Read `HACKATHON_MASTER_PLAN.md`
- [ ] Read `GIT_PHASE_WORKFLOW.md`
- [ ] Read `HACKATHON_WINNING_STRATEGY.md`
- [ ] Get Groq API key: https://console.groq.com (FREE)
- [ ] Install Docker Desktop
- [ ] Install Node.js 18+
- [ ] Install Python 3.11+
- [ ] Install Tesseract OCR (for document scanning - see below)

---

# CRITICAL SETUP INSTRUCTIONS

## 1. Groq API Key (DO THIS FIRST - FREE)
```
1. Go to https://console.groq.com
2. Sign up with Google (instant)
3. Click "API Keys" → "Create API Key"
4. Copy the key starting with "gsk_"
5. Save it - you'll add to backend/.env

FREE LIMITS: 30 requests/min, 6000 tokens/min
This is MORE than enough for hackathon!
```

## 2. Docker Desktop (REQUIRED)
```
⚠️ CRITICAL: Start Docker Desktop BEFORE running docker-compose!
- Windows: Download from https://docker.com/products/docker-desktop
- Make sure it's RUNNING (whale icon in taskbar)
- Test: Open terminal, run `docker --version`
```

## 3. Tesseract OCR (For Document/Image Classification)
```
Windows Installation:
1. Download: https://github.com/UB-Mannheim/tesseract/wiki
2. Run installer (use default path: C:\Program Files\Tesseract-OCR)
3. Add to PATH:
   - Search "Environment Variables" in Windows
   - Edit PATH → Add "C:\Program Files\Tesseract-OCR"
4. Test: Open new terminal, run `tesseract --version`

⚠️ If Tesseract fails: Skip OCR features, text classification still works!
```

## 4. Python Virtual Environment
```bash
cd backend
python -m venv venv
venv\Scripts\activate  # Windows
pip install -r requirements.txt
```

---

## Hour 0 (Hackathon Starts)
- [ ] Create GitHub repo
- [ ] Create `develop` branch
- [ ] Account 1: Start `feature/fe-phase-1-foundation`
- [ ] Account 2: Start `feature/be-phase-1-foundation`
- [ ] Open Claude, paste shared context + Phase 1 feed
- [ ] START CODING!

## Every 3 Hours
- [ ] Commit and push current work
- [ ] Create PR to develop
- [ ] Merge PR
- [ ] Start new branch for next phase
- [ ] Open NEW Claude conversation
- [ ] Paste shared context + next phase feed

---

# TOTAL FEATURES: 21

### Major Features (7)
| # | Feature | Type |
|---|---------|------|
| 1 | HS Classification (AI + OCR + Image) | Microservice |
| 2 | Landed Cost Calculator | Core API |
| 3 | Route Optimizer | Microservice |
| 4 | Trade Compliance + OFAC | Core API |
| 5 | AI Assistant Chat | Core API |
| 6 | Cargo Tracking | UI |
| 7 | Analytics Dashboard + Export | UI |

### Polish Features (14)
Light/Dark Mode, Keyboard Shortcuts, Toast Notifications, Loading Skeletons, PDF Export, Excel Export, Error Boundaries, Empty States, Rate Limiting, Response Caching, WebSocket Real-time, Background Jobs, Health Monitoring, Image Upload

---

# KEY FACTS TO REMEMBER

| Question | Answer |
|----------|--------|
| Total Features | **21** (7 major + 14 polish) |
| Microservices | **2** (HS Classifier + Route Optimizer) |
| Do we need to train models? | **NO** - Groq's Llama 3.1 70B is pre-trained |
| Is data real? | **YES** - USITC API, Exchange rates are LIVE |
| How fast is classification? | **2-3 seconds** (Groq), <50ms (cached) |
| What if API fails? | Use `DEMO_MOCK_DATA.md` fallback |
| Cost of APIs? | **$0** - All free tiers |

---

# GOOD LUCK! 🚀

**Team Scripting_Ninjas - PEC Chandigarh**

You have everything you need. Now go win that hackathon!
