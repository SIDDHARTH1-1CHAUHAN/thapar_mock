# PRD - Integration & Final Phase
## TradeOptimize AI | Hackathon Final Sprint

---

# OVERVIEW

**Phase**: Final Integration (Hours 21-24 of simulated 24-hour window)
**Objective**: Merge all features, run tests, fix bugs, add polish
**Runs After**: Both Account 1 and Account 2 complete their phases
**Branch**: `develop` → `main`

---

# PRE-INTEGRATION CHECKLIST

## Account 1 (Frontend) Must Have:
- [ ] All 7 pages implemented and styled
- [ ] API integration functions ready
- [ ] WebSocket hooks implemented
- [ ] Loading states and error boundaries
- [ ] Responsive layouts tested

## Account 2 (Backend) Must Have:
- [ ] All 3 services running (main + 2 microservices)
- [ ] Database schemas migrated
- [ ] All endpoints responding
- [ ] Docker Compose working
- [ ] Seed data loaded

---

# PHASE BREAKDOWN

## Step 1: Merge & Environment Setup (30 minutes)

### 1.1 Branch Merging
```bash
# Merge all feature branches into develop
git checkout develop
git merge feature/frontend-foundation --no-ff -m "Merge frontend foundation"
git merge feature/frontend-core-features --no-ff -m "Merge frontend core features"
git merge feature/ai-assistant-tracking --no-ff -m "Merge AI assistant and tracking"
git merge feature/frontend-complete --no-ff -m "Merge frontend complete"
git merge feature/backend-foundation --no-ff -m "Merge backend foundation"
git merge feature/microservice-classification --no-ff -m "Merge classification microservice"
git merge feature/microservice-route-optimizer --no-ff -m "Merge route optimizer"
git merge feature/backend-complete --no-ff -m "Merge backend complete"
```

### 1.2 Environment Configuration

**File**: `.env` (root)
```env
# API Keys
CLAUDE_API_KEY=your_claude_api_key_here
PINECONE_API_KEY=your_pinecone_api_key_here
OPENEXCHANGE_API_KEY=your_fx_api_key_here

# Database URLs
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/tradeoptimize
MONGODB_URL=mongodb://localhost:27017/tradeoptimize
REDIS_URL=redis://localhost:6379

# Service URLs (for frontend)
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_CLASSIFICATION_URL=http://localhost:8001
NEXT_PUBLIC_ROUTE_OPTIMIZER_URL=http://localhost:8002
NEXT_PUBLIC_WS_URL=ws://localhost:8002

# Feature Flags
ENABLE_IMAGE_CLASSIFICATION=true
ENABLE_STREAMING_CHAT=true
ENABLE_LIVE_TRACKING=true
```

### 1.3 Full Stack Startup
```bash
# Start all services
docker-compose up -d

# Wait for databases to be ready
sleep 10

# Run database migrations
cd backend && alembic upgrade head && cd ..

# Seed initial data
cd backend && python -m app.db.seed && cd ..

# Start frontend in development
cd frontend && npm run dev
```

---

## Step 2: Integration Testing (1 hour)

### 2.1 API Integration Tests

**File**: `tests/integration/test_api_integration.py`
```python
import pytest
import httpx
import asyncio

BASE_URL = "http://localhost:8000"
CLASSIFICATION_URL = "http://localhost:8001"
ROUTE_URL = "http://localhost:8002"

class TestHealthChecks:
    """Verify all services are running"""

    @pytest.mark.asyncio
    async def test_main_backend_health(self):
        async with httpx.AsyncClient() as client:
            response = await client.get(f"{BASE_URL}/health")
            assert response.status_code == 200
            assert response.json()["status"] == "healthy"

    @pytest.mark.asyncio
    async def test_classification_service_health(self):
        async with httpx.AsyncClient() as client:
            response = await client.get(f"{CLASSIFICATION_URL}/health")
            assert response.status_code == 200

    @pytest.mark.asyncio
    async def test_route_optimizer_health(self):
        async with httpx.AsyncClient() as client:
            response = await client.get(f"{ROUTE_URL}/health")
            assert response.status_code == 200


class TestClassificationFlow:
    """Test end-to-end classification flow"""

    @pytest.mark.asyncio
    async def test_classify_product(self):
        async with httpx.AsyncClient() as client:
            response = await client.post(
                f"{CLASSIFICATION_URL}/api/v1/classify",
                json={
                    "description": "Wireless Bluetooth headphones with active noise cancellation",
                    "origin_country": "CN",
                    "destination_country": "US"
                }
            )
            assert response.status_code == 200
            data = response.json()
            assert "hs_code" in data
            assert "confidence" in data
            assert data["confidence"] >= 70

    @pytest.mark.asyncio
    async def test_classify_with_image(self):
        async with httpx.AsyncClient() as client:
            response = await client.post(
                f"{CLASSIFICATION_URL}/api/v1/classify",
                json={
                    "description": "Electronic device",
                    "image_url": "https://example.com/product.jpg",
                    "origin_country": "CN",
                    "destination_country": "US"
                }
            )
            assert response.status_code == 200


class TestLandedCostFlow:
    """Test landed cost calculation"""

    @pytest.mark.asyncio
    async def test_calculate_landed_cost(self):
        async with httpx.AsyncClient() as client:
            response = await client.post(
                f"{BASE_URL}/api/v1/landed-cost/calculate",
                json={
                    "hs_code": "8518.30.20",
                    "unit_price": 45.00,
                    "quantity": 1000,
                    "origin_country": "CN",
                    "destination_country": "US",
                    "shipping_mode": "ocean_fcl",
                    "incoterm": "FOB"
                }
            )
            assert response.status_code == 200
            data = response.json()
            assert "total_landed_cost" in data
            assert "breakdown" in data
            assert data["breakdown"]["section_301"] > 0  # CN origin should have 301


class TestRouteOptimization:
    """Test route optimization"""

    @pytest.mark.asyncio
    async def test_optimize_route(self):
        async with httpx.AsyncClient() as client:
            response = await client.post(
                f"{ROUTE_URL}/api/v1/optimize-route",
                json={
                    "originPort": "CNSHA",
                    "destinationPort": "USLAX",
                    "hsCode": "8518.30.20",
                    "productValue": 45000,
                    "quantity": 1000,
                    "cargoType": "20ft"
                }
            )
            assert response.status_code == 200
            data = response.json()
            assert "baseline" in data
            assert "alternatives" in data
            assert "recommendation" in data


class TestComplianceFlow:
    """Test compliance checking"""

    @pytest.mark.asyncio
    async def test_compliance_check(self):
        async with httpx.AsyncClient() as client:
            response = await client.post(
                f"{BASE_URL}/api/v1/compliance/check",
                json={
                    "parties": ["ACME Corp", "Global Logistics Inc"],
                    "hs_codes": ["8518.30.20"],
                    "documents": [
                        {"type": "commercial_invoice", "verified": True},
                        {"type": "packing_list", "verified": True}
                    ]
                }
            )
            assert response.status_code == 200
            data = response.json()
            assert "screening_clear" in data
            assert "audit_risk" in data


class TestAIAssistant:
    """Test AI assistant"""

    @pytest.mark.asyncio
    async def test_chat(self):
        async with httpx.AsyncClient() as client:
            response = await client.post(
                f"{BASE_URL}/api/v1/assistant/chat",
                json={
                    "message": "What is the HS code for wireless headphones?",
                    "context": {}
                }
            )
            assert response.status_code == 200
            data = response.json()
            assert "response" in data
```

### 2.2 Frontend Integration Tests

**File**: `frontend/tests/integration.spec.ts`
```typescript
import { test, expect } from '@playwright/test'

test.describe('TradeOptimize AI Integration', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('http://localhost:3000')
  })

  test('should load dashboard', async ({ page }) => {
    await expect(page.locator('text=TRADE OPTIMIZER')).toBeVisible()
  })

  test('should classify product', async ({ page }) => {
    await page.click('text=HS CLASSIFIER')
    await page.fill('textarea[name="description"]', 'Wireless Bluetooth headphones')
    await page.selectOption('select[name="origin"]', 'CN')
    await page.selectOption('select[name="destination"]', 'US')
    await page.click('button:has-text("Classify")')

    // Wait for result
    await expect(page.locator('[data-testid="hs-code-result"]')).toBeVisible()
    await expect(page.locator('[data-testid="confidence-score"]')).toBeVisible()
  })

  test('should calculate landed cost', async ({ page }) => {
    await page.click('text=LANDED COST')
    await page.fill('input[name="unitPrice"]', '45')
    await page.fill('input[name="quantity"]', '1000')
    await page.fill('input[name="hsCode"]', '8518.30.20')
    await page.click('button:has-text("Calculate")')

    await expect(page.locator('[data-testid="total-landed-cost"]')).toBeVisible()
  })

  test('should show route options', async ({ page }) => {
    await page.click('text=ROUTE OPTIMIZER')
    await page.selectOption('select[name="origin"]', 'CNSHA')
    await page.selectOption('select[name="destination"]', 'USLAX')
    await page.click('button:has-text("Find Routes")')

    await expect(page.locator('[data-testid="route-card"]').first()).toBeVisible()
  })

  test('should chat with AI assistant', async ({ page }) => {
    await page.click('text=AI ASSISTANT')
    await page.fill('input[name="message"]', 'What HS code for laptops?')
    await page.click('button:has-text("Send")')

    await expect(page.locator('[data-testid="assistant-response"]')).toBeVisible()
  })
})
```

### 2.3 Run All Tests
```bash
# Backend tests
cd backend && pytest tests/ -v

# Frontend tests
cd frontend && npm run test:e2e

# Integration tests
pytest tests/integration/ -v
```

---

## Step 3: Bug Fixes & Edge Cases (1 hour)

### 3.1 Common Issues to Check

| Issue | Check | Fix |
|-------|-------|-----|
| CORS errors | Browser console | Update FastAPI CORS middleware |
| API timeout | Network tab | Increase timeout, add loading states |
| WebSocket disconnect | Console errors | Add reconnection logic |
| Missing data | Empty states | Add fallback UI |
| Type errors | TypeScript build | Fix interface mismatches |

### 3.2 Error Handling Improvements

**Frontend Error Boundary**:
```typescript
// frontend/src/components/ErrorBoundary.tsx
'use client'
import { Component, ErrorInfo, ReactNode } from 'react'

interface Props {
  children: ReactNode
  fallback?: ReactNode
}

interface State {
  hasError: boolean
  error?: Error
}

export class ErrorBoundary extends Component<Props, State> {
  state: State = { hasError: false }

  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error }
  }

  componentDidCatch(error: Error, errorInfo: ErrorInfo) {
    console.error('Error caught:', error, errorInfo)
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || (
        <div className="p-4 bg-red-900/20 border border-red-500 rounded">
          <h2 className="text-red-500 font-mono">SYSTEM_ERROR</h2>
          <p className="text-muted mt-2">Something went wrong. Please refresh.</p>
        </div>
      )
    }
    return this.props.children
  }
}
```

**API Error Handler**:
```typescript
// frontend/src/lib/api.ts
import axios, { AxiosError } from 'axios'

export const api = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_URL,
  timeout: 30000,
})

api.interceptors.response.use(
  response => response,
  (error: AxiosError) => {
    if (error.response?.status === 429) {
      // Rate limited
      console.error('Rate limited. Please wait.')
    } else if (error.response?.status === 503) {
      // Service unavailable
      console.error('Service temporarily unavailable.')
    }
    return Promise.reject(error)
  }
)
```

### 3.3 Performance Optimizations

```typescript
// Add React Query for caching
// frontend/src/lib/queryClient.ts
import { QueryClient } from '@tanstack/react-query'

export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 5 * 60 * 1000, // 5 minutes
      cacheTime: 10 * 60 * 1000, // 10 minutes
      retry: 2,
      refetchOnWindowFocus: false,
    },
  },
})
```

---

## Step 4: Unit Tests (30 minutes)

### 4.1 Backend Unit Tests

**File**: `backend/tests/unit/test_landed_cost.py`
```python
import pytest
from app.services.landed_cost_service import LandedCostService
from app.schemas.landed_cost import LandedCostRequest

@pytest.fixture
def service():
    return LandedCostService()

class TestLandedCostCalculations:

    def test_mpf_minimum(self, service):
        """MPF should not be less than $27.75"""
        result = service._calculate_mpf(1000)  # Very low value
        assert result >= 27.75

    def test_mpf_maximum(self, service):
        """MPF should not exceed $538.40"""
        result = service._calculate_mpf(1000000)  # Very high value
        assert result <= 538.40

    def test_section_301_for_china(self, service):
        """Section 301 should apply for CN origin"""
        request = LandedCostRequest(
            hs_code="8518.30.20",
            unit_price=45.00,
            quantity=1000,
            origin_country="CN",
            destination_country="US",
            shipping_mode="ocean_fcl",
            incoterm="FOB"
        )
        result = service.calculate(request)
        assert result.breakdown.section_301 > 0

    def test_no_section_301_for_vietnam(self, service):
        """Section 301 should NOT apply for VN origin"""
        request = LandedCostRequest(
            hs_code="8518.30.20",
            unit_price=45.00,
            quantity=1000,
            origin_country="VN",
            destination_country="US",
            shipping_mode="ocean_fcl",
            incoterm="FOB"
        )
        result = service.calculate(request)
        assert result.breakdown.section_301 == 0

    def test_hmf_only_for_ocean(self, service):
        """HMF should only apply for ocean shipping"""
        ocean_request = LandedCostRequest(
            hs_code="8518.30.20",
            unit_price=45.00,
            quantity=100,
            origin_country="CN",
            destination_country="US",
            shipping_mode="ocean_fcl",
            incoterm="FOB"
        )
        air_request = LandedCostRequest(
            hs_code="8518.30.20",
            unit_price=45.00,
            quantity=100,
            origin_country="CN",
            destination_country="US",
            shipping_mode="air",
            incoterm="FOB"
        )

        ocean_result = service.calculate(ocean_request)
        air_result = service.calculate(air_request)

        assert ocean_result.breakdown.hmf > 0
        assert air_result.breakdown.hmf == 0
```

**File**: `backend/tests/unit/test_compliance.py`
```python
import pytest
from app.services.compliance_service import ComplianceService

@pytest.fixture
def service():
    return ComplianceService()

class TestComplianceScreening:

    def test_clear_party(self, service):
        """Regular company should pass screening"""
        results = service._screen_parties(["ACME Corp"])
        assert all(r["clear"] for r in results)

    def test_blocked_party(self, service):
        """Blocked entity should fail screening"""
        results = service._screen_parties(["BLOCKED ENTITY LLC"])
        assert not all(r["clear"] for r in results)

    def test_audit_risk_calculation(self, service):
        """Audit risk should decrease with issues"""
        # Perfect case
        perfect = service._calculate_audit_risk(
            screening=[{"clear": True}],
            documents=[{"status": "VERIFIED"}],
            restrictions=[]
        )

        # With missing doc
        missing_doc = service._calculate_audit_risk(
            screening=[{"clear": True}],
            documents=[{"status": "MISSING"}],
            restrictions=[]
        )

        assert perfect.score > missing_doc.score
```

### 4.2 Frontend Unit Tests

**File**: `frontend/tests/unit/calculations.test.ts`
```typescript
import { describe, it, expect } from 'vitest'
import { formatCurrency, formatHSCode, calculateSavings } from '@/lib/utils'

describe('Utility Functions', () => {
  describe('formatCurrency', () => {
    it('should format USD correctly', () => {
      expect(formatCurrency(1234.56)).toBe('$1,234.56')
    })

    it('should handle zero', () => {
      expect(formatCurrency(0)).toBe('$0.00')
    })
  })

  describe('formatHSCode', () => {
    it('should format 10-digit code', () => {
      expect(formatHSCode('8518302000')).toBe('8518.30.20.00')
    })

    it('should format 6-digit code', () => {
      expect(formatHSCode('851830')).toBe('8518.30')
    })
  })

  describe('calculateSavings', () => {
    it('should calculate percentage savings', () => {
      const result = calculateSavings(100000, 85000)
      expect(result.amount).toBe(15000)
      expect(result.percentage).toBe(15)
    })
  })
})
```

---

## Step 5: Security & Vulnerability Check (30 minutes)

### 5.1 Security Checklist

| Check | Status | Notes |
|-------|--------|-------|
| API Key exposure | [ ] | Ensure all keys in .env, not in code |
| SQL Injection | [ ] | Using parameterized queries (SQLAlchemy) |
| XSS Prevention | [ ] | React escapes by default |
| CORS properly configured | [ ] | Only allow frontend origin |
| Rate limiting | [ ] | Redis-based rate limiting |
| Input validation | [ ] | Pydantic schemas for all inputs |
| HTTPS ready | [ ] | Configure for production |

### 5.2 Dependency Audit

```bash
# Backend
cd backend && pip-audit

# Frontend
cd frontend && npm audit

# Fix vulnerabilities
npm audit fix
```

### 5.3 Environment Variable Check

```bash
# Ensure no secrets in git
git secrets --scan

# Check for hardcoded keys
grep -r "sk-" --include="*.py" --include="*.ts" --include="*.tsx" .
grep -r "AKIA" --include="*.py" --include="*.ts" --include="*.tsx" .
```

---

## Step 6: Final Polish (30 minutes)

### 6.1 UI Enhancements

```typescript
// Add micro-interactions
// frontend/src/components/ui/Button.tsx
export const Button = ({ children, isLoading, ...props }) => (
  <button
    className={cn(
      "relative transition-all duration-200",
      "hover:scale-[1.02] active:scale-[0.98]",
      "disabled:opacity-50 disabled:cursor-not-allowed"
    )}
    {...props}
  >
    {isLoading && (
      <span className="absolute inset-0 flex items-center justify-center">
        <Spinner className="w-4 h-4" />
      </span>
    )}
    <span className={isLoading ? 'invisible' : ''}>{children}</span>
  </button>
)
```

### 6.2 Add Demo Data

```typescript
// frontend/src/data/demo.ts
export const demoClassifications = [
  {
    id: '#TR-8821',
    item: 'Cotton T-Shirt (Knitted)',
    hsCode: '6109.10.00',
    status: 'VERIFIED'
  },
  {
    id: '#TR-8820',
    item: 'Ceramic Coffee Mug',
    hsCode: '6912.00.25',
    status: 'VERIFIED'
  },
  {
    id: '#TR-8819',
    item: 'Wireless Headphones',
    hsCode: '8518.30.20',
    status: 'PENDING'
  }
]

export const demoShipment = {
  trackingId: '#MSC-992-XQ',
  vessel: 'MSC OSCAR',
  origin: { port: 'CNSHA', name: 'Shanghai', date: '21 OCT' },
  destination: { port: 'USLAX', name: 'Los Angeles', eta: '29 OCT' },
  status: 'IN_TRANSIT',
  position: { lat: 32.7, lng: -130.5 },
  reliabilityIndex: 82
}
```

### 6.3 Final Build Check

```bash
# Build frontend for production
cd frontend && npm run build

# Check for build errors
npm run lint
npm run type-check

# Test production build
npm run start
```

---

## Step 7: Git Commit Strategy (Final 3 Hours)

### Commit Schedule

| Time | Action | Branch |
|------|--------|--------|
| T+21:00 | Merge all features to develop | develop |
| T+21:30 | Run integration tests | develop |
| T+22:00 | Fix critical bugs | develop |
| T+22:30 | Run unit tests | develop |
| T+23:00 | Security check | develop |
| T+23:30 | Final polish | develop |
| T+24:00 | Merge to main, tag release | main |

### Final Commits

```bash
# Integration commit
git add .
git commit -m "chore: integrate frontend and backend services

- Merge all feature branches
- Configure environment variables
- Verify all services communicate correctly
- Add integration tests

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"

# Test commit
git add .
git commit -m "test: add unit and integration tests

- Add backend unit tests for landed cost
- Add backend unit tests for compliance
- Add frontend unit tests for utilities
- Add E2E tests with Playwright
- All tests passing

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"

# Security commit
git add .
git commit -m "security: vulnerability fixes and hardening

- Audit and fix npm vulnerabilities
- Add rate limiting to all endpoints
- Verify no secrets in codebase
- Configure CORS properly
- Add input validation

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"

# Final release
git checkout main
git merge develop --no-ff -m "release: v1.0.0 - Hackathon MVP

TradeOptimize AI - Complete MVP Release

Features:
- AI-Powered HS Classification (Microservice)
- Landed Cost Calculator
- Route Optimizer (Microservice)
- Trade Compliance Engine
- AI Trade Assistant
- Live Cargo Tracking
- Historical Analytics (UI)
- System Onboarding (UI)

Tech Stack:
- Frontend: Next.js 14 + TypeScript + Tailwind
- Backend: FastAPI + Node.js
- Databases: PostgreSQL + MongoDB + Redis
- AI: Claude API (Anthropic)

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"

git tag -a v1.0.0 -m "Hackathon MVP Release"
```

---

# DEMO PREPARATION

## Pre-Demo Checklist

- [ ] All services running (docker-compose up)
- [ ] Demo data loaded
- [ ] Browser cleared (no cached errors)
- [ ] Network stable
- [ ] Backup screenshots ready (if API fails)

## Demo Flow Script

1. **Open Dashboard** - Show clean UI
2. **Classify Product** - "Wireless Bluetooth headphones with ANC"
3. **Show Results** - HS 8518.30.20, 94% confidence
4. **Calculate Landed Cost** - Show $11,250 Section 301 duty
5. **Optimize Route** - Show Vietnam route saves $8,250
6. **Compliance Check** - Quick document validation
7. **AI Assistant** - Ask a trade question
8. **Cargo Tracking** - Show live map (if time)

## Backup Plan

If API fails during demo:
1. Use pre-recorded video
2. Show static screenshots
3. Walk through code architecture
4. Focus on technical decisions

---

# SUCCESS METRICS

| Metric | Target | Measure |
|--------|--------|---------|
| Classification time | < 5 seconds | Stopwatch |
| Confidence score | > 85% | API response |
| Route options | 3+ alternatives | UI count |
| Test coverage | > 70% | pytest-cov |
| Lighthouse score | > 80 | Chrome DevTools |
| Zero critical bugs | 0 | Manual testing |

---

# POST-HACKATHON ROADMAP

## Immediate (Week 1)
- Deploy to cloud (Vercel + Railway)
- Add more HS codes to database
- Improve classification prompts

## Short-term (Month 1)
- Real carrier API integrations
- User authentication (Auth0/Clerk)
- Billing integration (Stripe)

## Medium-term (Quarter 1)
- ERP connectors (SAP, NetSuite)
- Mobile app (React Native)
- Multi-language support

---

*PRD Version: 1.0*
*Phase: Integration & Final*
*Duration: 3 hours*
