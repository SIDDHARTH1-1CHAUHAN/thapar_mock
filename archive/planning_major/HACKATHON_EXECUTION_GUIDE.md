# HACKATHON EXECUTION GUIDE
## Exact Order of What to Feed Claude Tomorrow

---

# TEAM SETUP

| Person | Account | Role | Works On |
|--------|---------|------|----------|
| **Siddharth** | Account 1 | Frontend | Next.js, UI, Pages |
| **Saksham G** | Account 2 | Backend | FastAPI, Microservices, APIs |
| **Saksham G** | Account 2 | Backend | Same as above (pair coding) |

**Both Sakshams work on Account 2 together** (one codes, one reviews)

---

# BEFORE HACKATHON STARTS

## Night Before Checklist
```
□ Get Groq API key: https://console.groq.com
□ Install Docker Desktop (and START it)
□ Install Tesseract OCR
□ Install Node.js 18+
□ Install Python 3.11+
□ Clone/create empty GitHub repo
□ Read HACKATHON_MASTER_PLAN.md (15 min)
□ Read this file completely
```

---

# HOUR 0: HACKATHON STARTS

## First 10 Minutes - Setup

### Both Accounts - Initial Setup
```bash
# Create GitHub repo (one person does this)
# Clone to both machines

# Create branch structure
git checkout -b develop
git push -u origin develop
```

---

# ACCOUNT 1 (SIDDHARTH) - FRONTEND

---

## PHASE FE-1: Foundation (Hours 0-3)

### Step 1: Open NEW Claude conversation

### Step 2: Copy-paste this FIRST (Context):
```
Copy entire contents of:
D:\thapar-hackathon\planning_major\claude-feeds\shared\01_PROJECT_CONTEXT.md
```

### Step 3: Then copy-paste this (Design System):
```
Copy entire contents of:
D:\thapar-hackathon\planning_major\claude-feeds\shared\02_DESIGN_SYSTEM.md
```

### Step 4: Then copy-paste this (Phase 1 Instructions):
```
Copy entire contents of:
D:\thapar-hackathon\planning_major\claude-feeds\account-1-frontend\FEED_PHASE_1.md
```

### Step 5: Say to Claude:
```
"Create the complete frontend foundation as specified. Start with project setup."
```

### Step 6: Follow Claude's instructions, commit every 30 min

### End of Phase FE-1:
```bash
git add .
git commit -m "feat(fe1): frontend foundation complete"
git push -u origin feature/fe-phase-1-foundation
# Create PR to develop, merge it
```

---

## PHASE FE-2: Classify + AI Assistant (Hours 3-6)

### Step 1: Open NEW Claude conversation (fresh context)

### Step 2: Copy-paste Context FIRST:
```
Copy entire contents of:
D:\thapar-hackathon\planning_major\claude-feeds\shared\01_PROJECT_CONTEXT.md
```

### Step 3: Copy-paste Phase 2 Instructions:
```
Copy entire contents of:
D:\thapar-hackathon\planning_major\claude-feeds\account-1-frontend\FEED_PHASE_2.md
```

### Step 4: Say to Claude:
```
"Build the HS Classification page and AI Assistant chat interface as specified."
```

### End of Phase FE-2:
```bash
git add .
git commit -m "feat(fe2): classify and assistant pages complete"
git push -u origin feature/fe-phase-2-classify-assistant
# Create PR to develop, merge it
```

---

## PHASE FE-3: Landed Cost + Compliance (Hours 6-9)

### Step 1: Open NEW Claude conversation

### Step 2: Copy-paste Context:
```
D:\thapar-hackathon\planning_major\claude-feeds\shared\01_PROJECT_CONTEXT.md
```

### Step 3: Copy-paste Phase 3:
```
D:\thapar-hackathon\planning_major\claude-feeds\account-1-frontend\FEED_PHASE_3.md
```

### Step 4: Say:
```
"Build the Landed Cost Calculator and Trade Compliance pages as specified."
```

### End of Phase FE-3:
```bash
git commit -m "feat(fe3): landed cost and compliance pages complete"
git push && create PR && merge
```

---

## PHASE FE-4: Polish + Extras (Hours 9-12)

### Step 1: Open NEW Claude conversation

### Step 2: Copy-paste Context:
```
D:\thapar-hackathon\planning_major\claude-feeds\shared\01_PROJECT_CONTEXT.md
```

### Step 3: Copy-paste Phase 4:
```
D:\thapar-hackathon\planning_major\claude-feeds\account-1-frontend\FEED_PHASE_4.md
```

### Step 4: Say:
```
"Complete the Route Optimizer, Cargo Tracking, and Analytics pages. Add PDF/Excel export."
```

### End of Phase FE-4:
```bash
git commit -m "feat(fe4): all pages complete with export"
git push && create PR && merge
```

---

# ACCOUNT 2 (SAKSHAM x2) - BACKEND

---

## PHASE BE-1: Foundation (Hours 0-3)

### Step 1: Open NEW Claude conversation

### Step 2: Copy-paste Context FIRST:
```
Copy entire contents of:
D:\thapar-hackathon\planning_major\claude-feeds\shared\01_PROJECT_CONTEXT.md
```

### Step 3: Copy-paste Phase 1 Instructions:
```
Copy entire contents of:
D:\thapar-hackathon\planning_major\claude-feeds\account-2-backend\FEED_PHASE_1.md
```

### Step 4: Say to Claude:
```
"Set up the complete backend foundation with FastAPI, Docker, and databases as specified."
```

### Step 5: Important - Start Docker Desktop FIRST!
```bash
# Make sure Docker Desktop is running before docker-compose
docker-compose up -d
```

### End of Phase BE-1:
```bash
git commit -m "feat(be1): backend foundation with databases"
git push -u origin feature/be-phase-1-foundation
# Create PR to develop, merge it
```

---

## PHASE BE-2: HS Classification Microservice (Hours 3-6)

### Step 1: Open NEW Claude conversation

### Step 2: Copy-paste Context:
```
D:\thapar-hackathon\planning_major\claude-feeds\shared\01_PROJECT_CONTEXT.md
```

### Step 3: Copy-paste Phase 2:
```
D:\thapar-hackathon\planning_major\claude-feeds\account-2-backend\FEED_PHASE_2.md
```

### Step 4: Say:
```
"Build the HS Classification microservice with Groq LLM and Tesseract OCR as specified."
```

### Step 5: Set up Groq API key
```bash
# Add to backend/.env
GROQ_API_KEY=gsk_your_key_here
```

### End of Phase BE-2:
```bash
git commit -m "feat(be2): HS classification microservice with AI"
git push && create PR && merge
```

---

## PHASE BE-3: Route Optimizer Microservice (Hours 6-9)

### Step 1: Open NEW Claude conversation

### Step 2: Copy-paste Context:
```
D:\thapar-hackathon\planning_major\claude-feeds\shared\01_PROJECT_CONTEXT.md
```

### Step 3: Copy-paste Phase 3:
```
D:\thapar-hackathon\planning_major\claude-feeds\account-2-backend\FEED_PHASE_3.md
```

### Step 4: Say:
```
"Build the Route Optimizer microservice with Pareto algorithm as specified."
```

### End of Phase BE-3:
```bash
git commit -m "feat(be3): route optimizer microservice"
git push && create PR && merge
```

---

## PHASE BE-4: Integration APIs (Hours 9-12)

### Step 1: Open NEW Claude conversation

### Step 2: Copy-paste Context:
```
D:\thapar-hackathon\planning_major\claude-feeds\shared\01_PROJECT_CONTEXT.md
```

### Step 3: Copy-paste Phase 4:
```
D:\thapar-hackathon\planning_major\claude-feeds\account-2-backend\FEED_PHASE_4.md
```

### Step 4: Say:
```
"Complete all integration APIs: Landed Cost, Compliance, AI Assistant, Cargo Tracking, and Analytics as specified."
```

### End of Phase BE-4:
```bash
git commit -m "feat(be4): all integration APIs complete"
git push && create PR && merge
```

---

# QUICK REFERENCE: FILES TO FEED

## Account 1 (Frontend) - Feed Order

| Phase | Hour | Files to Copy-Paste |
|-------|------|---------------------|
| FE-1 | 0-3 | `01_PROJECT_CONTEXT.md` + `02_DESIGN_SYSTEM.md` + `FEED_PHASE_1.md` |
| FE-2 | 3-6 | `01_PROJECT_CONTEXT.md` + `FEED_PHASE_2.md` |
| FE-3 | 6-9 | `01_PROJECT_CONTEXT.md` + `FEED_PHASE_3.md` |
| FE-4 | 9-12 | `01_PROJECT_CONTEXT.md` + `FEED_PHASE_4.md` |

## Account 2 (Backend) - Feed Order

| Phase | Hour | Files to Copy-Paste |
|-------|------|---------------------|
| BE-1 | 0-3 | `01_PROJECT_CONTEXT.md` + `FEED_PHASE_1.md` |
| BE-2 | 3-6 | `01_PROJECT_CONTEXT.md` + `FEED_PHASE_2.md` |
| BE-3 | 6-9 | `01_PROJECT_CONTEXT.md` + `FEED_PHASE_3.md` |
| BE-4 | 9-12 | `01_PROJECT_CONTEXT.md` + `FEED_PHASE_4.md` |

---

# PARALLEL EXECUTION TIMELINE

```
HOUR    ACCOUNT 1 (Frontend)              ACCOUNT 2 (Backend)
────    ────────────────────              ───────────────────
 0      Start FE-1: Setup Next.js         Start BE-1: Setup FastAPI + Docker
 1      FE-1: Layout components           BE-1: Database connections
 2      FE-1: Theme, sidebar              BE-1: Health endpoints
 3      ──── PR & Merge FE-1 ────         ──── PR & Merge BE-1 ────

 3      Start FE-2: Classify page         Start BE-2: HS Classifier MS
 4      FE-2: Image upload                BE-2: Groq LLM integration
 5      FE-2: AI Assistant chat           BE-2: OCR + caching
 6      ──── PR & Merge FE-2 ────         ──── PR & Merge BE-2 ────

 6      Start FE-3: Landed Cost           Start BE-3: Route Optimizer MS
 7      FE-3: Calculator form             BE-3: Pareto algorithm
 8      FE-3: Compliance page             BE-3: WebSocket updates
 9      ──── PR & Merge FE-3 ────         ──── PR & Merge BE-3 ────

 9      Start FE-4: Route page            Start BE-4: Integration APIs
10      FE-4: Cargo tracking              BE-4: Landed Cost + Compliance
11      FE-4: Analytics + Export          BE-4: Cargo + Analytics APIs
12      ──── PR & Merge FE-4 ────         ──── PR & Merge BE-4 ────

12-14   Integration Testing               Integration Testing
14-16   Bug Fixes + Polish                Bug Fixes + Polish
16-18   Demo Preparation                  Demo Preparation
```

---

# INTEGRATION PHASE (Hours 12-14)

## After Both Teams Complete Phase 4

### Step 1: Pull all changes
```bash
git checkout develop
git pull
```

### Step 2: Update Frontend .env.local
```
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_HS_SERVICE_URL=http://localhost:8001
NEXT_PUBLIC_ROUTE_SERVICE_URL=http://localhost:8002
NEXT_PUBLIC_USE_MOCK=false
```

### Step 3: Start all services
```bash
# Terminal 1: Databases
cd backend && docker-compose up -d

# Terminal 2: Main backend
cd backend && uvicorn app.main:app --reload --port 8000

# Terminal 3: HS Classifier
cd microservices/hs_classifier && uvicorn app.main:app --reload --port 8001

# Terminal 4: Route Optimizer
cd microservices/route_optimizer && uvicorn app.main:app --reload --port 8002

# Terminal 5: Frontend
cd frontend && npm run dev
```

### Step 4: Test all endpoints
```bash
# Health checks
curl http://localhost:8000/health
curl http://localhost:8001/health
curl http://localhost:8002/health

# Test classification
curl -X POST http://localhost:8001/api/classify \
  -H "Content-Type: application/json" \
  -d '{"description": "wireless bluetooth headphones"}'

# Open frontend
open http://localhost:3000
```

---

# IF SOMETHING GOES WRONG

## Groq API Rate Limited
```bash
# Switch to mock mode in frontend
NEXT_PUBLIC_USE_MOCK=true
```

## Docker Not Working
```bash
# Run services directly without Docker
# PostgreSQL: Use SQLite instead
# Redis: Skip caching (code handles this)
# MongoDB: Use JSON files
```

## Frontend Build Fails
```bash
# Clear cache and reinstall
rm -rf node_modules .next
npm install
npm run dev
```

## Backend Import Errors
```bash
# Reinstall dependencies
pip install -r requirements.txt
```

## Out of Time
```
Priority order:
1. HS Classification (MUST HAVE)
2. Landed Cost Calculator
3. AI Assistant
4. The rest
```

---

# DEMO PREPARATION (Hours 16-18)

## Demo Script
```
1. Open app, show dashboard
2. Classify a product (show 2-3 sec speed)
3. Calculate landed cost (show Section 301)
4. Check compliance (show OFAC)
5. Compare routes (show recommendation)
6. Chat with AI assistant
7. Track a shipment (show real-time)
8. Show analytics (show charts)
9. Export report
```

## Demo Container IDs
```
TCLU1234567 - China to USA
MSCU9876543 - Hong Kong to Rotterdam
EGLV5551234 - Taiwan to USA
```

## Demo Products
```
"Solar-powered portable charger with LED flashlight" → 8504.40.95
"Wireless Bluetooth earbuds with noise cancellation" → 8518.30.20
"Stainless steel water bottle" → 7323.93.00
```

---

# EMERGENCY FALLBACK

If everything fails, use mock data:

### Frontend .env.local
```
NEXT_PUBLIC_USE_MOCK=true
```

### This will use:
```
D:\thapar-hackathon\planning_major\02-specifications\DEMO_MOCK_DATA.md
```

All responses will be pre-computed, demo will work perfectly.

---

# SUMMARY: WHAT TO FEED WHEN

## Account 1 (Frontend) - Siddharth

| Time | Action | Files |
|------|--------|-------|
| Hour 0 | New Claude chat | Context + Design + Phase 1 |
| Hour 3 | New Claude chat | Context + Phase 2 |
| Hour 6 | New Claude chat | Context + Phase 3 |
| Hour 9 | New Claude chat | Context + Phase 4 |

## Account 2 (Backend) - Saksham x2

| Time | Action | Files |
|------|--------|-------|
| Hour 0 | New Claude chat | Context + Phase 1 |
| Hour 3 | New Claude chat | Context + Phase 2 |
| Hour 6 | New Claude chat | Context + Phase 3 |
| Hour 9 | New Claude chat | Context + Phase 4 |

---

# FILE LOCATIONS QUICK REFERENCE

```
D:\thapar-hackathon\planning_major\
├── claude-feeds\
│   ├── shared\
│   │   ├── 01_PROJECT_CONTEXT.md    ← ALWAYS FEED FIRST
│   │   └── 02_DESIGN_SYSTEM.md      ← Feed with FE Phase 1 only
│   ├── account-1-frontend\
│   │   ├── FEED_PHASE_1.md
│   │   ├── FEED_PHASE_2.md
│   │   ├── FEED_PHASE_3.md
│   │   └── FEED_PHASE_4.md
│   └── account-2-backend\
│       ├── FEED_PHASE_1.md
│       ├── FEED_PHASE_2.md
│       ├── FEED_PHASE_3.md
│       ├── FEED_PHASE_4.md
│       └── LIVE_DATA_APIS.md        ← Optional bonus
```

---

**GOOD LUCK TOMORROW! YOU'VE GOT THIS!**
