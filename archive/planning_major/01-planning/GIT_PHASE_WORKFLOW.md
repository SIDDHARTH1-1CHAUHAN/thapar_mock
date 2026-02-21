# Git Phase Workflow - TradeOptimize AI
## Independent 3-Hour Phases with Branch & PR Strategy

---

# OVERVIEW

Each phase is a **completely independent** branch that can be:
- Worked on in isolation
- Committed at any time
- PR'd and merged without dependencies on other incomplete phases
- Demonstrated even if later phases fail

**Total Phases**: 8 phases (4 frontend + 4 backend) running in parallel
**Duration**: 3 hours each
**Branch Strategy**: Feature branches → PR → Merge to `develop` → Final merge to `main`

---

# BRANCH STRUCTURE

```
main (production-ready)
│
├── develop (integration branch)
│   │
│   ├── FRONTEND TRACK (Account 1)
│   │   ├── feature/fe-phase-1-foundation
│   │   ├── feature/fe-phase-2-classify-assistant
│   │   ├── feature/fe-phase-3-cost-compliance
│   │   └── feature/fe-phase-4-polish-extras
│   │
│   └── BACKEND TRACK (Account 2)
│       ├── feature/be-phase-1-foundation
│       ├── feature/be-phase-2-classification-ms
│       ├── feature/be-phase-3-route-optimizer-ms
│       └── feature/be-phase-4-integration-apis
│
└── hotfix/* (emergency fixes)
```

---

# GIT COMMANDS CHEATSHEET

```bash
# Start of hackathon - create develop branch
git checkout -b develop main
git push -u origin develop

# Starting a new phase
git checkout develop
git pull origin develop
git checkout -b feature/fe-phase-1-foundation

# Commit often (every 30-45 mins)
git add .
git commit -m "feat(phase1): add sidebar component"

# End of phase - push and create PR
git push -u origin feature/fe-phase-1-foundation
# Create PR via GitHub UI or CLI:
gh pr create --base develop --title "Phase 1: Frontend Foundation" --body "..."

# Merge PR (after review/approval)
gh pr merge --squash

# Start next phase
git checkout develop
git pull origin develop
git checkout -b feature/fe-phase-2-classify-assistant
```

---

# FRONTEND PHASES (Account 1 - Siddharth)

## PHASE FE-1: Foundation (Hours 0-3)
**Branch**: `feature/fe-phase-1-foundation`

### Deliverables
- [ ] Next.js 14 project setup with TypeScript
- [ ] Tailwind config with design tokens
- [ ] Font loading (Space Grotesk + Silkscreen)
- [ ] AppLayout component (3-column grid)
- [ ] Sidebar component with navigation
- [ ] WorkspaceHeader component
- [ ] Basic routing structure
- [ ] **Theme system (Light/Dark mode)**

### Small Features Included
```typescript
// Theme toggle implementation
// src/store/useThemeStore.ts
import { create } from 'zustand'
import { persist } from 'zustand/middleware'

interface ThemeStore {
  theme: 'light' | 'dark'
  toggleTheme: () => void
}

export const useThemeStore = create<ThemeStore>()(
  persist(
    (set) => ({
      theme: 'dark', // Default to dark (matches design)
      toggleTheme: () => set((state) => ({
        theme: state.theme === 'dark' ? 'light' : 'dark'
      })),
    }),
    { name: 'theme-storage' }
  )
)
```

### Files to Create
```
frontend/
├── src/app/globals.css          ← Theme CSS variables
├── src/app/layout.tsx           ← ThemeProvider wrapper
├── src/components/layout/
│   ├── AppLayout.tsx
│   ├── Sidebar.tsx
│   ├── WorkspaceHeader.tsx
│   └── ThemeToggle.tsx          ← NEW: Light/Dark toggle
├── src/store/
│   └── useThemeStore.ts         ← NEW: Theme state
└── tailwind.config.ts
```

### Commit Schedule
```
Hour 0:30 - "feat(fe1): project scaffolding and dependencies"
Hour 1:00 - "feat(fe1): tailwind config with design tokens"
Hour 1:30 - "feat(fe1): AppLayout and Sidebar components"
Hour 2:00 - "feat(fe1): WorkspaceHeader and routing"
Hour 2:30 - "feat(fe1): theme system with light/dark mode"
Hour 3:00 - "feat(fe1): polish and PR ready"
```

### PR Template
```markdown
## Phase FE-1: Frontend Foundation

### Changes
- Next.js 14 project with TypeScript + Tailwind
- Design system implementation (fonts, colors, tokens)
- Core layout components (AppLayout, Sidebar, Header)
- Theme system with light/dark mode toggle
- Basic routing for all 6 pages

### Screenshots
[Add screenshots of light and dark mode]

### Testing
- [ ] `npm run dev` starts without errors
- [ ] Navigation works between pages
- [ ] Theme toggle persists across refresh

### Merge Requirements
- None (first phase)
```

---

## PHASE FE-2: Classify + AI Assistant (Hours 3-6)
**Branch**: `feature/fe-phase-2-classify-assistant`
**Depends on**: FE-1 merged

### Deliverables
- [ ] Classify Product page (full UI from cp.md design)
- [ ] Image upload component with drag-drop
- [ ] AI Assistant chat interface (from ai_assistant.md)
- [ ] Chat message components
- [ ] Typing indicator animation
- [ ] Intelligence panel (right sidebar)
- [ ] **Keyboard shortcuts**

### Small Features Included
```typescript
// Keyboard shortcuts
// src/hooks/useKeyboardShortcuts.ts
import { useEffect } from 'react'

export function useKeyboardShortcuts() {
  useEffect(() => {
    const handleKeyDown = (e: KeyboardEvent) => {
      // Ctrl/Cmd + K = Open AI Assistant
      if ((e.ctrlKey || e.metaKey) && e.key === 'k') {
        e.preventDefault()
        window.location.href = '/assistant'
      }
      // Ctrl/Cmd + Shift + C = Quick classify
      if ((e.ctrlKey || e.metaKey) && e.shiftKey && e.key === 'C') {
        e.preventDefault()
        window.location.href = '/classify'
      }
      // Escape = Close modals
      if (e.key === 'Escape') {
        document.dispatchEvent(new CustomEvent('close-modal'))
      }
    }

    window.addEventListener('keydown', handleKeyDown)
    return () => window.removeEventListener('keydown', handleKeyDown)
  }, [])
}
```

### Files to Create
```
frontend/src/
├── app/(dashboard)/
│   ├── classify/page.tsx
│   └── assistant/page.tsx
├── components/features/
│   ├── classify/
│   │   ├── ProductDescriptionForm.tsx
│   │   ├── ImageUploadSection.tsx
│   │   ├── TechnicalSpecs.tsx
│   │   └── ClassificationResult.tsx
│   └── assistant/
│       ├── ChatContainer.tsx
│       ├── MessageList.tsx
│       ├── Message.tsx
│       ├── TypingIndicator.tsx
│       ├── SuggestionChips.tsx
│       └── ChatInput.tsx
├── components/panels/
│   └── IntelligencePanel.tsx
└── hooks/
    └── useKeyboardShortcuts.ts    ← NEW
```

### Commit Schedule
```
Hour 3:30 - "feat(fe2): classify page layout and forms"
Hour 4:00 - "feat(fe2): image upload with drag-drop"
Hour 4:30 - "feat(fe2): AI assistant chat container"
Hour 5:00 - "feat(fe2): message components and animations"
Hour 5:30 - "feat(fe2): intelligence panel and suggestions"
Hour 6:00 - "feat(fe2): keyboard shortcuts and polish"
```

### PR Template
```markdown
## Phase FE-2: Classify + AI Assistant

### Changes
- HS Classification page with product form and image upload
- AI Assistant chat interface with message animations
- Typing indicator and suggestion chips
- Intelligence panel for live AI context
- Keyboard shortcuts (Ctrl+K for assistant, Ctrl+Shift+C for classify)

### Dependencies
- Requires FE-1 merged for layout components

### Testing
- [ ] Image drag-drop works
- [ ] Chat messages animate on send
- [ ] Keyboard shortcuts function
```

---

## PHASE FE-3: Landed Cost + Compliance (Hours 6-9)
**Branch**: `feature/fe-phase-3-cost-compliance`
**Depends on**: FE-2 merged (or FE-1 minimum)

### Deliverables
- [ ] Landed Cost Calculator page (from lc.md design)
- [ ] Cost breakdown visualization
- [ ] Trade Compliance page (from tc.md design)
- [ ] Risk indicator bars
- [ ] Document checklist component
- [ ] **Toast notifications system**
- [ ] **Loading skeletons**

### Small Features Included
```typescript
// Toast notification system
// src/components/ui/Toast.tsx
'use client'

import { create } from 'zustand'

interface Toast {
  id: string
  type: 'success' | 'error' | 'warning' | 'info'
  message: string
}

interface ToastStore {
  toasts: Toast[]
  addToast: (toast: Omit<Toast, 'id'>) => void
  removeToast: (id: string) => void
}

export const useToastStore = create<ToastStore>((set) => ({
  toasts: [],
  addToast: (toast) => set((state) => ({
    toasts: [...state.toasts, { ...toast, id: crypto.randomUUID() }]
  })),
  removeToast: (id) => set((state) => ({
    toasts: state.toasts.filter((t) => t.id !== id)
  })),
}))

// Auto-dismiss after 5s
export function toast(type: Toast['type'], message: string) {
  const { addToast, removeToast } = useToastStore.getState()
  const id = crypto.randomUUID()
  addToast({ type, message })
  setTimeout(() => removeToast(id), 5000)
}
```

```typescript
// Loading skeleton
// src/components/ui/Skeleton.tsx
export function Skeleton({ className = '' }: { className?: string }) {
  return (
    <div className={`animate-pulse bg-[#333] ${className}`} />
  )
}

export function CardSkeleton() {
  return (
    <div className="border border-[#333] p-5 space-y-3">
      <Skeleton className="h-4 w-24" />
      <Skeleton className="h-8 w-full" />
      <Skeleton className="h-4 w-3/4" />
    </div>
  )
}
```

### Files to Create
```
frontend/src/
├── app/(dashboard)/
│   ├── landed-cost/page.tsx
│   └── compliance/page.tsx
├── components/features/
│   ├── landed-cost/
│   │   ├── CostCalculatorForm.tsx
│   │   ├── CostBreakdown.tsx
│   │   ├── DutyRateDisplay.tsx
│   │   └── CurrencySelector.tsx
│   └── compliance/
│       ├── ComplianceChecker.tsx
│       ├── RiskIndicator.tsx
│       ├── DocumentChecklist.tsx
│       └── SanctionsAlert.tsx
├── components/ui/
│   ├── Toast.tsx               ← NEW
│   ├── ToastContainer.tsx      ← NEW
│   └── Skeleton.tsx            ← NEW
└── store/
    └── useToastStore.ts        ← NEW
```

### Commit Schedule
```
Hour 6:30 - "feat(fe3): landed cost page and form"
Hour 7:00 - "feat(fe3): cost breakdown visualization"
Hour 7:30 - "feat(fe3): compliance checker page"
Hour 8:00 - "feat(fe3): risk indicators and checklist"
Hour 8:30 - "feat(fe3): toast notification system"
Hour 9:00 - "feat(fe3): loading skeletons and polish"
```

---

## PHASE FE-4: Polish + Extras (Hours 9-12)
**Branch**: `feature/fe-phase-4-polish-extras`
**Depends on**: FE-3 merged

### Deliverables
- [ ] Route Optimizer page (from ro.md design)
- [ ] Cargo Locator page (from cargo.locator.md design)
- [ ] Analytics Dashboard with charts
- [ ] **PDF/Excel export functionality**
- [ ] **Responsive design adjustments**
- [ ] **Error boundaries**
- [ ] **Empty states**

### Small Features Included
```typescript
// PDF Export
// src/lib/export.ts
import jsPDF from 'jspdf'
import autoTable from 'jspdf-autotable'
import * as XLSX from 'xlsx'
import { saveAs } from 'file-saver'
import html2canvas from 'html2canvas'

export async function exportToPDF(elementId: string, filename: string) {
  const element = document.getElementById(elementId)
  if (!element) return

  const canvas = await html2canvas(element, { scale: 2 })
  const imgData = canvas.toDataURL('image/png')

  const pdf = new jsPDF('p', 'mm', 'a4')
  const pdfWidth = pdf.internal.pageSize.getWidth()
  const pdfHeight = (canvas.height * pdfWidth) / canvas.width

  pdf.addImage(imgData, 'PNG', 0, 0, pdfWidth, pdfHeight)
  pdf.save(`${filename}.pdf`)
}

export function exportToExcel(data: any[], filename: string) {
  const worksheet = XLSX.utils.json_to_sheet(data)
  const workbook = XLSX.utils.book_new()
  XLSX.utils.book_append_sheet(workbook, worksheet, 'Data')
  const excelBuffer = XLSX.write(workbook, { bookType: 'xlsx', type: 'array' })
  const blob = new Blob([excelBuffer], { type: 'application/octet-stream' })
  saveAs(blob, `${filename}.xlsx`)
}
```

```typescript
// Error Boundary
// src/components/ErrorBoundary.tsx
'use client'

import { Component, ReactNode } from 'react'

interface Props {
  children: ReactNode
  fallback?: ReactNode
}

interface State {
  hasError: boolean
}

export class ErrorBoundary extends Component<Props, State> {
  state = { hasError: false }

  static getDerivedStateFromError() {
    return { hasError: true }
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || (
        <div className="p-8 text-center">
          <div className="font-pixel text-xl mb-4">SYSTEM_ERROR</div>
          <p className="text-sm opacity-70">Something went wrong. Please refresh.</p>
          <button
            onClick={() => window.location.reload()}
            className="mt-4 bg-dark text-canvas px-4 py-2 font-pixel"
          >
            RELOAD_&gt;
          </button>
        </div>
      )
    }
    return this.props.children
  }
}
```

```typescript
// Empty State
// src/components/ui/EmptyState.tsx
export function EmptyState({
  title,
  description,
  action
}: {
  title: string
  description: string
  action?: { label: string; onClick: () => void }
}) {
  return (
    <div className="flex flex-col items-center justify-center p-12 text-center">
      <div className="w-16 h-16 border-2 border-dashed border-[#555] flex items-center justify-center mb-4">
        <span className="text-2xl">∅</span>
      </div>
      <div className="font-pixel text-lg mb-2">{title}</div>
      <p className="text-sm opacity-60 max-w-md">{description}</p>
      {action && (
        <button
          onClick={action.onClick}
          className="mt-4 bg-dark text-canvas px-4 py-2 font-pixel text-sm"
        >
          {action.label}
        </button>
      )}
    </div>
  )
}
```

### Files to Create
```
frontend/src/
├── app/(dashboard)/
│   ├── route/page.tsx
│   ├── cargo/page.tsx
│   └── analytics/page.tsx
├── components/features/
│   ├── route/
│   │   ├── RouteComparison.tsx
│   │   ├── RouteCard.tsx
│   │   └── MapVisualization.tsx
│   ├── cargo/
│   │   ├── ShipmentTracker.tsx
│   │   ├── TrackingTimeline.tsx
│   │   └── VesselPosition.tsx
│   └── analytics/
│       ├── AnalyticsDashboard.tsx
│       ├── SavingsChart.tsx
│       ├── ClassificationStats.tsx
│       └── ExportButtons.tsx
├── components/
│   ├── ErrorBoundary.tsx       ← NEW
│   └── ui/EmptyState.tsx       ← NEW
└── lib/
    └── export.ts               ← NEW
```

### Commit Schedule
```
Hour 9:30  - "feat(fe4): route optimizer page"
Hour 10:00 - "feat(fe4): cargo locator with timeline"
Hour 10:30 - "feat(fe4): analytics dashboard with charts"
Hour 11:00 - "feat(fe4): PDF/Excel export functionality"
Hour 11:30 - "feat(fe4): error boundaries and empty states"
Hour 12:00 - "feat(fe4): responsive adjustments and final polish"
```

---

# BACKEND PHASES (Account 2 - Saksham G x2)

## PHASE BE-1: Foundation (Hours 0-3)
**Branch**: `feature/be-phase-1-foundation`

### Deliverables
- [ ] FastAPI project structure
- [ ] PostgreSQL + MongoDB + Redis setup (Docker Compose)
- [ ] Database models and migrations
- [ ] Basic health check endpoints
- [ ] CORS configuration
- [ ] **Environment configuration**
- [ ] **Logging setup**

### Small Features Included
```python
# Environment config
# backend/app/core/config.py
from pydantic_settings import BaseSettings
from functools import lru_cache

class Settings(BaseSettings):
    # App
    APP_NAME: str = "TradeOptimize AI"
    DEBUG: bool = False
    API_V1_PREFIX: str = "/api/v1"

    # Database
    POSTGRES_URL: str = "postgresql://user:pass@localhost:5432/tradeopt"
    MONGO_URL: str = "mongodb://localhost:27017"
    REDIS_URL: str = "redis://localhost:6379"

    # AI Services (FREE)
    GROQ_API_KEY: str = ""
    OLLAMA_URL: str = "http://localhost:11434"

    # External APIs
    USITC_API_URL: str = "https://hts.usitc.gov/api"
    EXCHANGE_RATE_API_KEY: str = ""

    class Config:
        env_file = ".env"

@lru_cache()
def get_settings():
    return Settings()
```

```python
# Logging setup
# backend/app/core/logging.py
import logging
import sys
from datetime import datetime

def setup_logging():
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
```

### Files to Create
```
backend/
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── core/
│   │   ├── config.py           ← NEW
│   │   ├── logging.py          ← NEW
│   │   └── database.py
│   ├── models/
│   │   ├── classification.py
│   │   ├── landed_cost.py
│   │   └── user.py
│   └── api/
│       └── v1/
│           ├── health.py
│           └── router.py
├── docker-compose.yml
├── requirements.txt
└── .env.example
```

### Commit Schedule
```
Hour 0:30 - "feat(be1): FastAPI project structure"
Hour 1:00 - "feat(be1): Docker Compose for databases"
Hour 1:30 - "feat(be1): database models and connections"
Hour 2:00 - "feat(be1): health check endpoints"
Hour 2:30 - "feat(be1): environment config and logging"
Hour 3:00 - "feat(be1): CORS and final setup"
```

---

## PHASE BE-2: HS Classification Microservice (Hours 3-6)
**Branch**: `feature/be-phase-2-classification-ms`
**Depends on**: BE-1 merged

### Deliverables
- [ ] Dedicated microservice on Port 8001
- [ ] Groq API integration (FREE LLM)
- [ ] Ollama fallback for local inference
- [ ] Image classification with CLIP
- [ ] OCR with Tesseract
- [ ] **Rate limiting**
- [ ] **Caching layer**

### Small Features Included
```python
# Rate limiting
# microservices/hs_classifier/app/middleware/rate_limit.py
from fastapi import Request, HTTPException
from redis import Redis
import time

redis_client = Redis.from_url("redis://localhost:6379")

async def rate_limit_middleware(request: Request, call_next):
    client_ip = request.client.host
    key = f"rate_limit:{client_ip}"

    # 30 requests per minute (matches Groq free tier)
    current = redis_client.get(key)
    if current and int(current) >= 30:
        raise HTTPException(429, "Rate limit exceeded. Try again in 1 minute.")

    pipe = redis_client.pipeline()
    pipe.incr(key)
    pipe.expire(key, 60)
    pipe.execute()

    return await call_next(request)
```

```python
# Caching layer
# microservices/hs_classifier/app/services/cache.py
from redis import Redis
import json
import hashlib

redis = Redis.from_url("redis://localhost:6379")
CACHE_TTL = 3600 * 24  # 24 hours

def get_cache_key(description: str) -> str:
    return f"hs_cache:{hashlib.md5(description.lower().encode()).hexdigest()}"

def get_cached_classification(description: str) -> dict | None:
    key = get_cache_key(description)
    cached = redis.get(key)
    if cached:
        return json.loads(cached)
    return None

def cache_classification(description: str, result: dict):
    key = get_cache_key(description)
    redis.setex(key, CACHE_TTL, json.dumps(result))
```

### Files to Create
```
microservices/hs_classifier/
├── app/
│   ├── main.py
│   ├── api/
│   │   └── classify.py
│   ├── services/
│   │   ├── llm_service.py      # Groq + Ollama
│   │   ├── image_service.py    # CLIP
│   │   ├── ocr_service.py      # Tesseract
│   │   └── cache.py            ← NEW
│   ├── middleware/
│   │   └── rate_limit.py       ← NEW
│   └── prompts/
│       └── classification.py
├── Dockerfile
└── requirements.txt
```

---

## PHASE BE-3: Route Optimizer Microservice (Hours 6-9)
**Branch**: `feature/be-phase-3-route-optimizer-ms`
**Depends on**: BE-1 merged

### Deliverables
- [ ] Dedicated microservice on Port 8002
- [ ] Route calculation algorithm
- [ ] Port congestion data integration
- [ ] Cost/time/emissions comparison
- [ ] **WebSocket for real-time updates**
- [ ] **Background job processing**

### Small Features Included
```python
# WebSocket for real-time route updates
# microservices/route_optimizer/app/api/websocket.py
from fastapi import WebSocket, WebSocketDisconnect
from typing import List

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
            await connection.send_json(message)

manager = ConnectionManager()

@router.websocket("/ws/route-updates")
async def websocket_endpoint(websocket: WebSocket):
    await manager.connect(websocket)
    try:
        while True:
            data = await websocket.receive_json()
            # Process route request and send updates
            await manager.broadcast({
                "type": "route_update",
                "data": data
            })
    except WebSocketDisconnect:
        manager.disconnect(websocket)
```

```python
# Background jobs with Redis Queue
# microservices/route_optimizer/app/jobs/route_calculator.py
from rq import Queue
from redis import Redis

redis_conn = Redis.from_url("redis://localhost:6379")
queue = Queue(connection=redis_conn)

def enqueue_route_calculation(params: dict):
    job = queue.enqueue(
        'app.services.route_service.calculate_routes',
        params,
        job_timeout='5m'
    )
    return job.id

def get_job_status(job_id: str):
    from rq.job import Job
    job = Job.fetch(job_id, connection=redis_conn)
    return {
        "status": job.get_status(),
        "result": job.result if job.is_finished else None
    }
```

### Files to Create
```
microservices/route_optimizer/
├── app/
│   ├── main.py
│   ├── api/
│   │   ├── routes.py
│   │   └── websocket.py        ← NEW
│   ├── services/
│   │   ├── route_service.py
│   │   ├── port_data.py
│   │   └── emissions.py
│   ├── jobs/
│   │   └── route_calculator.py ← NEW
│   └── algorithms/
│       └── pareto.py
├── Dockerfile
└── requirements.txt
```

---

## PHASE BE-4: Integration APIs (Hours 9-12)
**Branch**: `feature/be-phase-4-integration-apis`
**Depends on**: BE-2 and BE-3 merged

### Deliverables
- [ ] Main backend API gateway
- [ ] Landed cost calculation engine
- [ ] Compliance checker with OFAC screening
- [ ] AI Assistant backend
- [ ] Live data service (USITC, exchange rates)
- [ ] **API documentation (Swagger)**
- [ ] **Health monitoring dashboard data**

### Small Features Included
```python
# Health monitoring
# backend/app/api/v1/monitoring.py
from fastapi import APIRouter
import psutil
import asyncio
import httpx

router = APIRouter()

@router.get("/health/detailed")
async def detailed_health():
    # Check all services
    services = {
        "main_backend": True,
        "hs_classifier": await check_service("http://localhost:8001/health"),
        "route_optimizer": await check_service("http://localhost:8002/health"),
        "postgres": await check_postgres(),
        "redis": await check_redis(),
        "groq_api": await check_groq(),
    }

    # System metrics
    metrics = {
        "cpu_percent": psutil.cpu_percent(),
        "memory_percent": psutil.virtual_memory().percent,
        "disk_percent": psutil.disk_usage('/').percent,
    }

    return {
        "status": "healthy" if all(services.values()) else "degraded",
        "services": services,
        "metrics": metrics,
        "timestamp": datetime.utcnow().isoformat()
    }

async def check_service(url: str) -> bool:
    try:
        async with httpx.AsyncClient() as client:
            r = await client.get(url, timeout=2.0)
            return r.status_code == 200
    except:
        return False
```

### Files to Create
```
backend/app/
├── api/v1/
│   ├── landed_cost.py
│   ├── compliance.py
│   ├── assistant.py
│   ├── analytics.py
│   └── monitoring.py          ← NEW
├── services/
│   ├── landed_cost_service.py
│   ├── compliance_service.py
│   ├── assistant_service.py
│   └── live_data_service.py
└── utils/
    └── swagger_docs.py        ← Enhanced docs
```

---

# SMALL FEATURES SUMMARY

| Feature | Phase | Description |
|---------|-------|-------------|
| **Light/Dark Mode** | FE-1 | Theme toggle with persistence |
| **Keyboard Shortcuts** | FE-2 | Ctrl+K (assistant), Ctrl+Shift+C (classify) |
| **Toast Notifications** | FE-3 | Success/error/warning toasts |
| **Loading Skeletons** | FE-3 | Animated loading placeholders |
| **PDF/Excel Export** | FE-4 | Download reports in multiple formats |
| **Error Boundaries** | FE-4 | Graceful error handling |
| **Empty States** | FE-4 | Beautiful empty state components |
| **Env Configuration** | BE-1 | Centralized settings management |
| **Logging System** | BE-1 | Structured logging to file |
| **Rate Limiting** | BE-2 | Protect API from abuse |
| **Caching Layer** | BE-2 | Redis cache for classifications |
| **WebSocket Updates** | BE-3 | Real-time route notifications |
| **Background Jobs** | BE-3 | Async route calculations |
| **Health Monitoring** | BE-4 | Service status dashboard |

---

# PR CHECKLIST FOR EACH PHASE

Before creating PR:
- [ ] All commits are atomic and well-described
- [ ] Code compiles without errors
- [ ] Basic functionality tested manually
- [ ] No console.log/print statements left
- [ ] Environment variables documented
- [ ] README updated if needed

PR Description includes:
- [ ] Summary of changes
- [ ] Screenshots (for frontend)
- [ ] Testing steps
- [ ] Dependencies on other phases
- [ ] Breaking changes (if any)

---

# MERGE SCHEDULE

```
Timeline (24-hour simulation):

Hour 3:  PR for FE-1 + BE-1 (merge immediately - no dependencies)
Hour 6:  PR for FE-2 + BE-2 (merge after FE-1/BE-1)
Hour 9:  PR for FE-3 + BE-3 (merge after previous)
Hour 12: PR for FE-4 + BE-4 (merge after previous)

Hour 12-15: Integration testing
Hour 15-18: Bug fixes and polish
Hour 18-21: Demo preparation
Hour 21-24: Final merge to main + deployment
```

---

# EMERGENCY PROCEDURES

### If a Phase Fails
```bash
# Revert to last working state
git checkout develop
git reset --hard origin/develop

# Start fresh branch for fixes
git checkout -b hotfix/phase-x-fix
```

### If Merge Conflict
```bash
# Resolve on develop
git checkout develop
git pull origin develop
git merge feature/branch-name
# Resolve conflicts manually
git add .
git commit -m "fix: resolve merge conflicts"
git push origin develop
```

### If API Key Expires During Demo
```bash
# Switch to mock mode
export NEXT_PUBLIC_USE_MOCK=true
# Restart frontend
npm run dev
```

---

**Each phase is designed to be independently valuable. Even if later phases fail, earlier phases provide a working product for demo.**
