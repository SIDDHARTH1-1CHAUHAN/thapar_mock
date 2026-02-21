# HACKATHON DAY EXECUTION GUIDE
## Step-by-Step Instructions for February 22, 2026

---

# TABLE OF CONTENTS

1. [Pre-Hackathon Setup (Night Before)](#pre-hackathon-setup)
2. [Hour 0: Hackathon Starts](#hour-0-hackathon-starts)
3. [Account 1: Frontend Phases](#account-1-frontend)
4. [Account 2: Backend Phases](#account-2-backend)
5. [Integration Phase](#integration-phase)
6. [Demo Preparation](#demo-preparation)
7. [Emergency Procedures](#emergency-procedures)

---

# PRE-HACKATHON SETUP
## Complete These the Night Before

### Step 1: Get Groq API Key (2 minutes)
```
1. Go to https://console.groq.com
2. Sign up with Google (instant)
3. Click "API Keys" → "Create API Key"
4. Copy the key (starts with "gsk_")
5. Save it somewhere safe - you'll need it tomorrow
```

### Step 2: Install Required Software

**Docker Desktop:**
```
Download: https://docker.com/products/docker-desktop
Install and START IT (whale icon should appear in taskbar)
Test: Open terminal → docker --version
```

**Tesseract OCR (Windows):**
```
Download: https://github.com/UB-Mannheim/tesseract/wiki
Install to: C:\Program Files\Tesseract-OCR
Add to PATH: System Environment Variables → PATH → Add "C:\Program Files\Tesseract-OCR"
Test: Open NEW terminal → tesseract --version
```

**Node.js 18+:**
```
Download: https://nodejs.org (LTS version)
Test: node --version (should be 18+)
```

**Python 3.11+:**
```
Download: https://python.org
Test: python --version (should be 3.11+)
```

### Step 3: Create GitHub Repository
```bash
# Create new repo on GitHub called "tradeoptimize-ai"
# Clone it locally
git clone https://github.com/YOUR_USERNAME/tradeoptimize-ai.git
cd tradeoptimize-ai

# Create develop branch
git checkout -b develop
git push -u origin develop
```

### Step 4: Verify Docker is Running
```
⚠️ CRITICAL: Docker Desktop must be RUNNING before hackathon starts
- Look for whale icon in taskbar
- If not running, start Docker Desktop app
```

---

# HOUR 0: HACKATHON STARTS

## Both Accounts: Initial Setup (First 15 minutes)

### Step 1: Open Project Folder
```
Location: D:\thapar-hackathon
```

### Step 2: Create Project Structure
```bash
# In the repo folder, create:
mkdir frontend
mkdir backend
mkdir backend/microservices
mkdir backend/microservices/hs_classifier
mkdir backend/microservices/route_optimizer
```

### Step 3: Start Docker Databases
```bash
# Terminal 1 (Keep open entire hackathon)
cd backend
# Docker Compose will be created in Phase BE-1
```

### Step 4: Split into Two Accounts
```
Account 1 (Siddharth): Frontend development
Account 2 (Saksham x2): Backend development

Each account opens their own Claude conversation
```

---

# ACCOUNT 1: FRONTEND

## PHASE FE-1: Foundation (Hours 0-3)

### Step 1: Create New Branch
```bash
git checkout develop
git pull origin develop
git checkout -b feature/fe-phase-1-foundation
```

### Step 2: Open New Claude Conversation

### Step 3: Feed Files to Claude (IN THIS ORDER)

**File 1:** `D:\thapar-hackathon\planning_major\claude-feeds\shared\01_PROJECT_CONTEXT.md`

**Prompt to use:**
```
I'm building a trade optimization platform called TradeOptimize AI for a hackathon.
Here's the project context. Read it carefully, then wait for my next message with specific instructions.
```

**File 2:** `D:\thapar-hackathon\planning_major\claude-feeds\shared\02_DESIGN_SYSTEM.md`

**Prompt to use:**
```
Here's our design system with all the CSS variables, Tailwind config, and component specifications.
Read it carefully and remember these exact styles - they must be used throughout the project.
```

**File 3:** `D:\thapar-hackathon\planning_major\claude-feeds\account-1-frontend\FEED_PHASE_1.md`

**Prompt to use:**
```
Now here are the specific instructions for Phase 1. Please implement everything in this document step by step.
Start by creating the Next.js project, then implement each component and page as described.
After each major step, show me the code and wait for confirmation before proceeding.
```

### Step 4: Monitor Progress
- Claude will create Next.js project
- Implement design system (globals.css, tailwind.config.ts)
- Create layout components (AppLayout, Sidebar, WorkspaceHeader)
- Create UI components (Button, Input, ResultCard, etc.)
- Set up theme system with Zustand
- Create placeholder pages for all 7 routes

### Step 5: Test Before Committing
```bash
cd frontend
npm run dev
# Open http://localhost:3000
# Test: All 7 routes work, theme toggle works, fonts load correctly
```

### Step 6: Commit and Push
```bash
git add .
git commit -m "feat(fe1): frontend foundation with Next.js 14, design system, and routing"
git push -u origin feature/fe-phase-1-foundation
```

### Step 7: Create PR and Merge
```bash
# On GitHub: Create Pull Request to develop branch
# Review the changes
# Merge the PR
```

---

## PHASE FE-2: Classify + AI Assistant (Hours 3-6)

### Step 1: Create New Branch
```bash
git checkout develop
git pull origin develop
git checkout -b feature/fe-phase-2-classify-assistant
```

### Step 2: Open NEW Claude Conversation (Fresh context)

### Step 3: Feed Files to Claude (IN THIS ORDER)

**File 1:** `D:\thapar-hackathon\planning_major\claude-feeds\shared\01_PROJECT_CONTEXT.md`

**Prompt to use:**
```
I'm continuing development on TradeOptimize AI. Here's the project context.
Phase 1 (foundation) is complete. We now have Next.js with the design system running.
Read this context, then wait for Phase 2 instructions.
```

**File 2:** `D:\thapar-hackathon\planning_major\claude-feeds\account-1-frontend\FEED_PHASE_2.md`

**Prompt to use:**
```
Here are the Phase 2 instructions. We need to build:
1. HS Classification page with product form and image upload
2. AI Assistant chat interface
3. Keyboard shortcuts
4. API client with mock/real toggle

Please implement each component step by step. The project already exists at ./frontend
Start with the Classify page components.
```

### Step 4: Monitor Progress
- Claude will create ProductDescriptionForm, ImageUploadSection
- Create AI Assistant chat components
- Implement keyboard shortcuts hook
- Set up API client with React Query
- Connect pages to API (mock mode initially)

### Step 5: Test Before Committing
```bash
cd frontend
npm run dev
# Test: /classify page works, image drag-drop works
# Test: /assistant page shows chat interface
# Test: Ctrl+K shortcut opens assistant
```

### Step 6: Commit, Push, and Merge
```bash
git add .
git commit -m "feat(fe2): HS classification page, AI assistant chat, and API client"
git push -u origin feature/fe-phase-2-classify-assistant
# Create PR on GitHub and merge to develop
```

---

## PHASE FE-3: Landed Cost + Compliance (Hours 6-9)

### Step 1: Create New Branch
```bash
git checkout develop
git pull origin develop
git checkout -b feature/fe-phase-3-cost-compliance
```

### Step 2: Open NEW Claude Conversation

### Step 3: Feed Files to Claude (IN THIS ORDER)

**File 1:** `D:\thapar-hackathon\planning_major\claude-feeds\shared\01_PROJECT_CONTEXT.md`

**Prompt to use:**
```
Continuing TradeOptimize AI development. Phase 1-2 complete (foundation, classify, assistant).
Read this context for reference.
```

**File 2:** `D:\thapar-hackathon\planning_major\claude-feeds\account-1-frontend\FEED_PHASE_3.md`

**Prompt to use:**
```
Here are Phase 3 instructions. We need to build:
1. Landed Cost Calculator page
2. Trade Compliance page
3. Toast notification system
4. Loading skeletons

The project is at ./frontend. Please implement step by step.
```

### Step 4: Test Before Committing
```bash
cd frontend
npm run dev
# Test: /landed-cost page calculates and shows breakdown
# Test: /compliance page shows risk indicators
# Test: Toast notifications appear
```

### Step 5: Commit, Push, and Merge
```bash
git add .
git commit -m "feat(fe3): landed cost calculator, compliance checker, toast system"
git push -u origin feature/fe-phase-3-cost-compliance
# Create PR and merge
```

---

## PHASE FE-4: Route + Cargo + Analytics (Hours 9-12)

### Step 1: Create New Branch
```bash
git checkout develop
git pull origin develop
git checkout -b feature/fe-phase-4-polish-extras
```

### Step 2: Open NEW Claude Conversation

### Step 3: Feed Files to Claude (IN THIS ORDER)

**File 1:** `D:\thapar-hackathon\planning_major\claude-feeds\shared\01_PROJECT_CONTEXT.md`

**Prompt to use:**
```
Continuing TradeOptimize AI. Phases 1-3 complete. Final frontend phase.
Read this context for reference.
```

**File 2:** `D:\thapar-hackathon\planning_major\claude-feeds\account-1-frontend\FEED_PHASE_4.md`

**Prompt to use:**
```
Here are Phase 4 instructions - the final frontend phase. We need:
1. Route Optimizer page with interactive map (Leaflet)
2. Cargo Tracking page with real-time map
3. Analytics Dashboard with charts (Recharts)
4. PDF/Excel/CSV export functionality
5. Error boundaries and empty states

IMPORTANT: First run "npm install leaflet react-leaflet @types/leaflet" for map support.
The project is at ./frontend. Please implement step by step.
```

### Step 4: Test Before Committing
```bash
cd frontend
npm run dev
# Test: /route page shows map with routes
# Test: /cargo page shows tracking map with vessel position
# Test: /analytics page shows charts
# Test: Export buttons generate PDF/Excel files
```

### Step 5: Commit, Push, and Merge
```bash
git add .
git commit -m "feat(fe4): route optimizer, cargo tracking, analytics with maps and export"
git push -u origin feature/fe-phase-4-polish-extras
# Create PR and merge
```

---

# ACCOUNT 2: BACKEND

## PHASE BE-1: Foundation (Hours 0-3)

### Step 1: Create New Branch
```bash
git checkout develop
git pull origin develop
git checkout -b feature/be-phase-1-foundation
```

### Step 2: Open New Claude Conversation

### Step 3: Feed Files to Claude (IN THIS ORDER)

**File 1:** `D:\thapar-hackathon\planning_major\claude-feeds\shared\01_PROJECT_CONTEXT.md`

**Prompt to use:**
```
I'm building TradeOptimize AI backend for a hackathon using FastAPI and Python.
Here's the project context. Read it carefully, then wait for specific instructions.
```

**File 2:** `D:\thapar-hackathon\planning_major\claude-feeds\account-2-backend\FEED_PHASE_1.md`

**Prompt to use:**
```
Here are Phase 1 instructions for backend foundation. Please implement:
1. FastAPI project structure
2. Docker Compose for PostgreSQL, MongoDB, Redis
3. Database connection modules
4. Health check endpoints
5. Configuration and logging

Create everything in the ./backend folder. Start with docker-compose.yml.
```

### Step 4: Start Databases and Test
```bash
cd backend
docker-compose up -d

# Wait 30 seconds for databases to start
# Then start the server:
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000

# Test:
curl http://localhost:8000/api/v1/health
# Should return {"status": "healthy"}
```

### Step 5: Commit, Push, and Merge
```bash
git add .
git commit -m "feat(be1): FastAPI foundation with Docker databases and health checks"
git push -u origin feature/be-phase-1-foundation
# Create PR and merge
```

---

## PHASE BE-2: HS Classification Microservice (Hours 3-6)

### Step 1: Create New Branch
```bash
git checkout develop
git pull origin develop
git checkout -b feature/be-phase-2-classification-ms
```

### Step 2: Open NEW Claude Conversation

### Step 3: Feed Files to Claude (IN THIS ORDER)

**File 1:** `D:\thapar-hackathon\planning_major\claude-feeds\shared\01_PROJECT_CONTEXT.md`

**Prompt to use:**
```
Continuing TradeOptimize AI backend. Phase 1 (foundation) is complete.
Databases are running. Read this context for reference.
```

**File 2:** `D:\thapar-hackathon\planning_major\claude-feeds\account-2-backend\FEED_PHASE_2.md`

**Prompt to use:**
```
Here are Phase 2 instructions for the HS Classification Microservice. This is the CORE AI feature.

We need to build:
1. Microservice structure in ./backend/microservices/hs_classifier/
2. LLM Service using Groq API (FREE - Llama 3.1 70B)
3. OCR Service using Tesseract
4. Redis caching for responses
5. Rate limiting middleware
6. Classification API endpoints

IMPORTANT: The prompts in this document are OPTIMIZED for 90-95% accuracy.
Use them EXACTLY as written - they include GIR rules and few-shot examples.

My Groq API key is: [PASTE YOUR KEY HERE]

Please implement step by step, starting with the microservice structure.
```

### Step 4: Create .env File
```bash
# Create backend/.env with:
GROQ_API_KEY=gsk_YOUR_KEY_HERE
POSTGRES_URL=postgresql+asyncpg://tradeopt:tradeopt123@localhost:5432/tradeoptimize
MONGO_URL=mongodb://localhost:27017
REDIS_URL=redis://localhost:6379
```

### Step 5: Test the Microservice
```bash
cd backend/microservices/hs_classifier
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8001

# Test classification:
curl -X POST http://localhost:8001/api/classify \
  -H "Content-Type: application/json" \
  -d '{"description": "Wireless Bluetooth headphones with noise cancellation"}'

# Should return HS code 8518.30.20 with ~95% confidence
```

### Step 6: Commit, Push, and Merge
```bash
git add .
git commit -m "feat(be2): HS classification microservice with Groq AI and optimized prompts"
git push -u origin feature/be-phase-2-classification-ms
# Create PR and merge
```

---

## PHASE BE-3: Route Optimizer Microservice (Hours 6-9)

### Step 1: Create New Branch
```bash
git checkout develop
git pull origin develop
git checkout -b feature/be-phase-3-route-optimizer-ms
```

### Step 2: Open NEW Claude Conversation

### Step 3: Feed Files to Claude (IN THIS ORDER)

**File 1:** `D:\thapar-hackathon\planning_major\claude-feeds\shared\01_PROJECT_CONTEXT.md`

**Prompt to use:**
```
Continuing TradeOptimize AI backend. Phases 1-2 complete (foundation, HS classifier).
Read this context for reference.
```

**File 2:** `D:\thapar-hackathon\planning_major\claude-feeds\account-2-backend\FEED_PHASE_3.md`

**Prompt to use:**
```
Here are Phase 3 instructions for Route Optimizer Microservice.

This uses ALGORITHMS (not AI):
- Pareto optimization for multi-objective scoring
- Port congestion data
- Emissions calculations
- WebSocket for real-time updates

Create in ./backend/microservices/route_optimizer/
Please implement step by step.
```

### Step 4: Test the Microservice
```bash
cd backend/microservices/route_optimizer
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8002

# Test route comparison:
curl -X POST http://localhost:8002/api/routes/compare \
  -H "Content-Type: application/json" \
  -d '{"origin_port": "CNSZX", "destination_port": "USLAX", "cargo_weight_kg": 1000, "cargo_value_usd": 50000}'
```

### Step 5: Commit, Push, and Merge
```bash
git add .
git commit -m "feat(be3): route optimizer microservice with Pareto algorithm"
git push -u origin feature/be-phase-3-route-optimizer-ms
# Create PR and merge
```

---

## PHASE BE-4: Integration APIs (Hours 9-12)

### Step 1: Create New Branch
```bash
git checkout develop
git pull origin develop
git checkout -b feature/be-phase-4-integration-apis
```

### Step 2: Open NEW Claude Conversation

### Step 3: Feed Files to Claude (IN THIS ORDER)

**File 1:** `D:\thapar-hackathon\planning_major\claude-feeds\shared\01_PROJECT_CONTEXT.md`

**Prompt to use:**
```
Final backend phase for TradeOptimize AI. Phases 1-3 complete.
Read this context for reference.
```

**File 2:** `D:\thapar-hackathon\planning_major\claude-feeds\account-2-backend\FEED_PHASE_4.md`

**Prompt to use:**
```
Here are Phase 4 instructions - all remaining backend APIs:

1. Landed Cost Service (uses live USITC tariff data)
2. Compliance Service (OFAC screening)
3. AI Assistant Service (uses same Groq API)
4. Cargo Tracking Service (with demo shipments)
5. Analytics Service (dashboard data)
6. Health Monitoring

Add all these to the main backend at ./backend/app/
Please implement step by step.
```

**File 3 (Reference):** `D:\thapar-hackathon\planning_major\claude-feeds\account-2-backend\LIVE_DATA_APIS.md`

**Prompt to use:**
```
Here's additional reference for live data integrations.
Use this to enhance the landed cost service with real USITC API calls.
```

### Step 4: Test All Endpoints
```bash
# Main backend should be running on port 8000

# Test landed cost:
curl -X POST http://localhost:8000/api/v1/landed-cost/calculate \
  -H "Content-Type: application/json" \
  -d '{"hs_code": "8504.40.95", "product_value": 50000, "quantity": 1000, "origin_country": "CN", "destination_country": "US"}'

# Test compliance:
curl -X POST http://localhost:8000/api/v1/compliance/check \
  -H "Content-Type: application/json" \
  -d '{"hs_code": "8504.40.95", "origin_country": "CN", "destination_country": "US"}'

# Test AI assistant:
curl -X POST http://localhost:8000/api/v1/assistant/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "What documents do I need for importing electronics from China?"}'

# Test cargo tracking:
curl http://localhost:8000/api/v1/cargo/track/TCLU1234567

# Test analytics:
curl http://localhost:8000/api/v1/analytics/dashboard
```

### Step 5: Commit, Push, and Merge
```bash
git add .
git commit -m "feat(be4): landed cost, compliance, AI assistant, cargo, analytics APIs"
git push -u origin feature/be-phase-4-integration-apis
# Create PR and merge
```

---

# INTEGRATION PHASE
## Hours 10-12 (Both Accounts Together)

### Step 1: Pull Latest Code (Both Accounts)
```bash
git checkout develop
git pull origin develop
```

### Step 2: Start All Services

**Terminal 1 - Databases:**
```bash
cd backend
docker-compose up -d
```

**Terminal 2 - Main Backend (Port 8000):**
```bash
cd backend
uvicorn app.main:app --reload --port 8000
```

**Terminal 3 - HS Classifier (Port 8001):**
```bash
cd backend/microservices/hs_classifier
uvicorn app.main:app --reload --port 8001
```

**Terminal 4 - Route Optimizer (Port 8002):**
```bash
cd backend/microservices/route_optimizer
uvicorn app.main:app --reload --port 8002
```

**Terminal 5 - Frontend (Port 3000):**
```bash
cd frontend
npm run dev
```

### Step 3: Configure Frontend for Real API

Edit `frontend/.env.local`:
```
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_HS_SERVICE_URL=http://localhost:8001
NEXT_PUBLIC_ROUTE_SERVICE_URL=http://localhost:8002
NEXT_PUBLIC_USE_MOCK=false
```

Restart frontend:
```bash
# Ctrl+C to stop
npm run dev
```

### Step 4: Test Full Flow
```
1. Open http://localhost:3000
2. Go to /classify → Enter "Solar powered portable charger with LED flashlight"
3. Click CLASSIFY → Should return HS code 8504.40.95
4. Go to /landed-cost → Enter the HS code, value $62,500, quantity 5000, China→US
5. Click CALCULATE → Should show total ~$81,634
6. Go to /compliance → Run check → Should show Section 301 warning
7. Go to /cargo → Track TCLU1234567 → Should show map with vessel
8. Go to /analytics → Should show charts and data
9. Test PDF export → Should download file
```

### Step 5: Fix Any Issues
Common issues:
- **CORS error:** Check backend CORS settings allow localhost:3000
- **Connection refused:** Verify all services are running
- **404 errors:** Check API endpoint paths match

---

# DEMO PREPARATION
## Last 30 Minutes

### Step 1: Final Git Merge
```bash
git checkout main
git merge develop
git push origin main
```

### Step 2: Prepare Demo Script
Open `D:\thapar-hackathon\planning_major\DEMO_BACKUP_RESPONSES.md` in a browser tab

### Step 3: Demo Flow (Practice This)
```
1. INTRO (30 sec)
   "This is TradeOptimize AI - an AI-powered trade optimization platform"

2. HS CLASSIFICATION (90 sec)
   - Show product description input
   - Demonstrate AI classification with Groq
   - Show confidence score and GIR reasoning

3. LANDED COST (60 sec)
   - Show cost breakdown
   - Highlight Section 301 tariff
   - Show per-unit cost

4. CARGO TRACKING (60 sec)
   - Show live map with vessel position
   - Show shipment timeline
   - Highlight real-time updates

5. ROUTE OPTIMIZER (60 sec)
   - Show route comparison
   - Show port congestion on map
   - Highlight recommended route

6. AI ASSISTANT (60 sec)
   - Ask trade question
   - Show AI response
   - Show suggestion chips

7. ANALYTICS (30 sec)
   - Show dashboard
   - Demo PDF export

8. TECH STACK (30 sec)
   - "Built with Next.js 14, FastAPI, Groq AI"
   - "21 features in 12 hours"
   - "100% free APIs"
```

### Step 4: Backup Plan
If APIs fail during demo:
```bash
# Edit frontend/.env.local:
NEXT_PUBLIC_USE_MOCK=true

# Restart frontend
npm run dev
```

---

# EMERGENCY PROCEDURES

## If Docker Won't Start
```bash
# Restart Docker Desktop
# Wait 2 minutes
# Try again: docker-compose up -d
```

## If Groq API Fails
```
The LLM service has Ollama fallback built-in.
If Ollama isn't installed, it returns mock classification.
Demo will still work, just with default responses.
```

## If Database Connection Fails
```bash
# Check if containers are running:
docker ps

# If not, restart:
docker-compose down
docker-compose up -d
```

## If Frontend Won't Build
```bash
rm -rf node_modules
rm package-lock.json
npm install
npm run dev
```

## If Everything Fails
```
1. Keep calm
2. Open DEMO_BACKUP_RESPONSES.md
3. Walk through UI manually
4. Read responses from the backup file
5. Explain what WOULD happen

Judges understand technical issues.
Confidence matters more than a working demo.
```

---

# QUICK REFERENCE

## File Locations
```
Planning docs:     D:\thapar-hackathon\planning_major\
Claude feeds:      D:\thapar-hackathon\planning_major\claude-feeds\
Shared context:    claude-feeds\shared\01_PROJECT_CONTEXT.md
Design system:     claude-feeds\shared\02_DESIGN_SYSTEM.md
Frontend phases:   claude-feeds\account-1-frontend\FEED_PHASE_*.md
Backend phases:    claude-feeds\account-2-backend\FEED_PHASE_*.md
Demo backup:       planning_major\DEMO_BACKUP_RESPONSES.md
Prompt guide:      planning_major\PROMPT_ENGINEERING_GUIDE.md
```

## Port Numbers
```
Frontend:          http://localhost:3000
Main Backend:      http://localhost:8000
HS Classifier MS:  http://localhost:8001
Route Optimizer:   http://localhost:8002
PostgreSQL:        localhost:5432
MongoDB:           localhost:27017
Redis:             localhost:6379
```

## Demo Container IDs (Memorize)
```
TCLU1234567 - Shenzhen → Long Beach (65% complete)
MSCU9876543 - Hong Kong → Rotterdam (40% complete)
EGLV5551234 - Kaohsiung → Los Angeles (80% complete)
```

## Demo Products (Memorize)
```
"Solar powered portable charger with LED flashlight" → 8504.40.95 (94%)
"Wireless Bluetooth earbuds with charging case" → 8518.30.20 (97%)
"Stainless steel insulated water bottle" → 7323.93.00 (99%)
```

---

# TIMELINE SUMMARY

| Time | Account 1 (Frontend) | Account 2 (Backend) |
|------|---------------------|---------------------|
| Hour 0-3 | Phase 1: Foundation | Phase 1: Foundation |
| Hour 3-6 | Phase 2: Classify + Chat | Phase 2: HS Classifier MS |
| Hour 6-9 | Phase 3: Cost + Compliance | Phase 3: Route Optimizer MS |
| Hour 9-11 | Phase 4: Route + Cargo + Analytics | Phase 4: Integration APIs |
| Hour 11-12 | Integration + Testing | Integration + Testing |
| Final 30min | Demo Prep | Demo Prep |

---

**GOOD LUCK! You've got this!**

Team Scripting_Ninjas - PEC Chandigarh
