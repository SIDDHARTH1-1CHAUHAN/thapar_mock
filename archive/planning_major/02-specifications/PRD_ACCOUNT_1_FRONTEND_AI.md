# PRD - Claude Account 1: Frontend + AI Services
## TradeOptimize AI | Hackathon Development Track

---

# CRITICAL REFERENCES (READ FIRST!)

| Document | Purpose |
|----------|---------|
| `DESIGN_IMPLEMENTATION_GUIDE.md` | CSS variables, component patterns, layout structure |
| `QUICK_COPY_PASTE_COMPONENTS.md` | Ready-to-use React components - COPY THESE FIRST |
| `HACKATHON_WINNING_STRATEGY.md` | Demo script, Q&A prep, presentation tips |
| `design/*.md` | Original HTML designs for pixel-perfect implementation |

**START HERE**: Copy all components from `QUICK_COPY_PASTE_COMPONENTS.md` before beginning Phase 1.

---

# OVERVIEW

**Assigned To**: Claude Pro Account 1 (Developer: Siddharth)
**Focus Areas**: Frontend Application + AI Integration Services
**Total Phases**: 4 (3 hours each = 12 hours)
**Branch Prefix**: `feature/frontend-*` and `feature/ai-*`

---

# RESPONSIBILITIES SUMMARY

| Component | Description | Priority |
|-----------|-------------|----------|
| Next.js Frontend | Complete UI/UX implementation | P0 |
| AI Assistant | Claude-powered chat interface | P0 |
| HS Classification UI | Input forms + result display | P0 |
| Landed Cost UI | Calculator interface | P0 |
| Cargo Tracking UI | Map + notifications | P1 |
| Analytics Dashboard | Charts + metrics (UI only) | P1 |
| Onboarding Wizard | Setup flow (UI only) | P2 |

---

# PHASE 1: PROJECT FOUNDATION (Hours 0-3)

## Branch: `feature/frontend-foundation`

### Tasks

#### 1.1 Project Scaffolding
```bash
# Initialize Next.js project
npx create-next-app@14 frontend --typescript --tailwind --eslint --app --src-dir

# Install core dependencies
cd frontend
npm install zustand @tanstack/react-query axios lucide-react recharts
npm install -D @types/node
```

**File Structure to Create**:
```
frontend/
├── src/
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   ├── globals.css
│   │   ├── (dashboard)/
│   │   │   ├── layout.tsx
│   │   │   ├── classify/page.tsx
│   │   │   ├── landed-cost/page.tsx
│   │   │   ├── route-optimizer/page.tsx
│   │   │   ├── compliance/page.tsx
│   │   │   ├── tracking/page.tsx
│   │   │   ├── analytics/page.tsx
│   │   │   └── assistant/page.tsx
│   │   └── onboarding/
│   │       └── page.tsx
│   ├── components/
│   │   ├── ui/
│   │   │   ├── Button.tsx
│   │   │   ├── Card.tsx
│   │   │   ├── Input.tsx
│   │   │   ├── Select.tsx
│   │   │   ├── Badge.tsx
│   │   │   ├── Progress.tsx
│   │   │   └── Skeleton.tsx
│   │   ├── layout/
│   │   │   ├── Sidebar.tsx
│   │   │   ├── Header.tsx
│   │   │   └── StatusBar.tsx
│   │   └── features/
│   │       └── (created in later phases)
│   ├── lib/
│   │   ├── api.ts
│   │   ├── utils.ts
│   │   └── constants.ts
│   ├── store/
│   │   └── useAppStore.ts
│   └── types/
│       └── index.ts
├── public/
│   └── (assets)
├── tailwind.config.ts
├── next.config.js
└── package.json
```

#### 1.2 Design System Implementation

**Tailwind Config** (Match screenshots - dark theme with red accents):
```typescript
// tailwind.config.ts
const config = {
  theme: {
    extend: {
      colors: {
        background: '#0a0a0a',
        foreground: '#fafafa',
        card: '#141414',
        'card-hover': '#1a1a1a',
        border: '#262626',
        primary: '#ef4444',
        'primary-hover': '#dc2626',
        muted: '#737373',
        success: '#22c55e',
        warning: '#eab308',
        error: '#ef4444',
      },
      fontFamily: {
        mono: ['JetBrains Mono', 'monospace'],
        sans: ['Inter', 'sans-serif'],
      },
    },
  },
}
```

**Base UI Components**:

```typescript
// src/components/ui/Button.tsx
interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'ghost' | 'outline'
  size?: 'sm' | 'md' | 'lg'
  isLoading?: boolean
  children: React.ReactNode
}

// src/components/ui/Card.tsx
interface CardProps {
  title?: string
  subtitle?: string
  status?: 'active' | 'pending' | 'error'
  children: React.ReactNode
}

// src/components/ui/Input.tsx
interface InputProps {
  label?: string
  placeholder?: string
  error?: string
  prefix?: React.ReactNode
}
```

#### 1.3 Layout Components

**Sidebar Navigation** (Match screenshot exactly):
```typescript
// src/components/layout/Sidebar.tsx
const navItems = [
  { label: 'HS CLASSIFIER', href: '/classify', icon: 'Tag' },
  { label: 'AI ASSISTANT', href: '/assistant', icon: 'Bot' },
  { label: 'LANDED COST', href: '/landed-cost', icon: 'Calculator' },
  { label: 'ROUTE OPTIMIZER', href: '/route-optimizer', icon: 'Map' },
  { label: 'LIVE TRACKING', href: '/tracking', icon: 'Navigation' },
  { label: 'COMPLIANCE', href: '/compliance', icon: 'Shield' },
  { label: 'HISTORICAL ANALYTICS', href: '/analytics', icon: 'BarChart' },
]
```

**Design Reference** (from screenshots):
- Logo: Circle with slash icon
- System name: "TRADE OPTIMIZER"
- Modules section with expandable items
- User info at bottom: "USER: DIMA VTULKIN / GLOBAL LOGISTICS"
- Version: "V.2.4.0"

#### 1.4 Global State Setup

```typescript
// src/store/useAppStore.ts
import { create } from 'zustand'

interface AppState {
  // User
  user: { name: string; company: string } | null

  // Current classification
  currentClassification: Classification | null
  setClassification: (c: Classification) => void

  // Landed cost results
  landedCostResult: LandedCostResult | null
  setLandedCostResult: (r: LandedCostResult) => void

  // Active shipment tracking
  activeShipment: Shipment | null
  setActiveShipment: (s: Shipment) => void

  // UI state
  sidebarCollapsed: boolean
  toggleSidebar: () => void
}
```

### Git Commit (Phase 1)
```bash
git checkout -b feature/frontend-foundation
git add .
git commit -m "feat(frontend): project scaffolding and design system

- Initialize Next.js 14 with TypeScript and Tailwind
- Implement dark theme design system matching UI specs
- Create base UI components (Button, Card, Input, etc.)
- Set up Sidebar and Header layout components
- Configure Zustand store for global state
- Define TypeScript interfaces for all entities

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
```

---

# PHASE 2: CORE FEATURE UIs (Hours 3-6)

## Branch: `feature/frontend-core-features`

### Tasks

#### 2.1 HS Classification Page

**File**: `src/app/(dashboard)/classify/page.tsx`

**UI Components**:
```
┌─────────────────────────────────────────────────────────────────┐
│ ACTIVE TASK                                        AI ANALYSIS  │
│ CLASSIFY PRODUCT                     DATE: 24 OCT 2023          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ ┌─────────────────────────────────┐  PREDICTED HS CODE          │
│ │ PRODUCT DESCRIPTION             │  ┌───────────────────────┐  │
│ │                                 │  │ 8509.80              │  │
│ │ e.g. Electric toothbrush with   │  └───────────────────────┘  │
│ │ lithium-ion battery and travel  │                             │
│ │ case...                         │  Electro-mechanical domestic │
│ │                                 │  appliances, with self-     │
│ └─────────────────────────────────┘  contained electric motor.  │
│                                                                 │
│ ORIGIN COUNTRY         DESTINATION                CONFIDENCE    │
│ ┌──────────────┐       ┌──────────────┐           ┌───────────┐│
│ │ China (CN)   │       │ United States│           │ 98.4%     ││
│ └──────────────┘       └──────────────┘           └───────────┘│
│                                                                 │
│ SUPPORTING DOCUMENTATION                DUTY: 4.2%  FTA ELIGIBLE│
│ ┌─────────────────────────────────┐                             │
│ │ DRAG_DROP_IMAGE_OR_PDF          │    LANDED COST EST.         │
│ │ Technical Specs, Blueprints...  │    $2,450.00                │
│ └─────────────────────────────────┘                             │
│                                                                 │
│ RECENT CLASSIFICATIONS                                          │
│ ┌────────┬───────────────────────┬────────────┬────────────────┐│
│ │ REF ID │ ITEM                  │ HS CODE    │ STATUS         ││
│ ├────────┼───────────────────────┼────────────┼────────────────┤│
│ │ #TR-8821│ Cotton T-Shirt       │ 6109.10.00 │ [VERIFIED]     ││
│ │ #TR-8820│ Ceramic Coffee Mug   │ 6912.00.25 │ [VERIFIED]     ││
│ │ #TR-8819│ Wireless Headphones  │ 8518.30.20 │ [PENDING]      ││
│ └────────┴───────────────────────┴────────────┴────────────────┘│
│                                                                 │
│                                        [EXPORT DOCUMENTS ↓]     │
└─────────────────────────────────────────────────────────────────┘
```

**Component Structure**:
```typescript
// src/components/features/classify/
├── ClassifyForm.tsx        // Main input form
├── HSResultCard.tsx        // Classification result display
├── ConfidenceIndicator.tsx // Circular confidence meter
├── SimilarRulings.tsx      // Related rulings list
├── RecentClassifications.tsx // History table
└── DocumentUpload.tsx      // Drag-drop zone
```

**API Integration**:
```typescript
// src/lib/api.ts
export const classifyProduct = async (data: ClassifyRequest) => {
  const response = await axios.post('http://localhost:8001/api/v1/classify', data)
  return response.data as ClassificationResult
}
```

#### 2.2 Landed Cost Calculator Page

**File**: `src/app/(dashboard)/landed-cost/page.tsx`

**UI Components**:
```
┌─────────────────────────────────────────────────────────────────┐
│ ACTIVE MODULE                                   COST ANALYSIS   │
│ LANDED CALCULATOR                              CURRENCY: USD    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ CARGO PARAMETERS                                EST. TOTAL      │
│ ┌────────────────────────────────────────┐      LANDED          │
│ │ UNIT PRICE (FOB)   QUANTITY   HS CODE  │      ┌─────────────┐ │
│ │ ┌────────────┐   ┌────────┐ ┌────────┐ │      │ $22,987.00  │ │
│ │ │    42.50   │   │   500  │ │8517.62 │ │      │ PER UNIT:   │ │
│ │ └────────────┘   └────────┘ └────────┘ │      │ $45.97      │ │
│ └────────────────────────────────────────┘      └─────────────┘ │
│                                                                 │
│ METHOD COMPARISON MATRIX                        FEE BREAKDOWN   │
│ ┌─────────────────────────────────────────────┐ ┌─────────────┐ │
│ │        SHIPPING  FREIGHT  INSURANCE TOTAL   │ │FOB Value    │ │
│ │        METHOD                       COST    │ │$21,250.00   │ │
│ │ ┌────────────────────────────────────────┐  │ │             │ │
│ │ │ OCEAN_FCL  $1,850  $125.00   $942.50   │  │ │Freight      │ │
│ │ │ 20ft Std   $24,167.50                  │  │ │$640.00      │ │
│ │ └────────────────────────────────────────┘  │ │             │ │
│ │ ┌────────────────────────────────────────┐  │ │Duty (3.2%)  │ │
│ │ │ OCEAN_LCL  $640.00 $85.00   $1,012.50  │  │ │$680.00      │ │
│ │ │ LCL Load   $22,987.00                  │  │ │             │ │
│ │ └────────────────────────────────────────┘  │ │MPF/HMF      │ │
│ │ ┌────────────────────────────────────────┐  │ │$112.00      │ │
│ │ │ AIR_EXPR   $4,200  $210.00  $890.00    │  │ │             │ │
│ │ │ Priority   $26,550.00                  │  │ │Inland       │ │
│ │ └────────────────────────────────────────┘  │ │$220.00      │ │
│ └─────────────────────────────────────────────┘ └─────────────┘ │
│                                                                 │
│ RISK ADVISORY:                                                  │
│ Current LCL congestion at Destination Port suggests +4 day     │
│ delay. Ocean FCL remains the most stable timeline for Q4.       │
│                                                                 │
│                                        [GENERATE QUOTE →]       │
└─────────────────────────────────────────────────────────────────┘
```

**Component Structure**:
```typescript
// src/components/features/landed-cost/
├── LandedCostForm.tsx      // Input form
├── CostBreakdown.tsx       // Fee breakdown card
├── MethodComparison.tsx    // Shipping method matrix
├── RiskAdvisory.tsx        // Risk warning box
└── QuoteGenerator.tsx      // Export functionality
```

#### 2.3 Route Optimizer Page

**File**: `src/app/(dashboard)/route-optimizer/page.tsx`

**UI Components**:
```
┌─────────────────────────────────────────────────────────────────┐
│ ACTIVE TASK                                    ROUTE ANALYTICS  │
│ ROUTE OPTIMIZER                           PATHWAY: SH → LB      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ ┌───────────────────────────────┐   RECOMMENDED / FASTEST       │
│ │                               │   ┌─────────────────────────┐ │
│ │   [MAP VISUALIZATION]         │   │ MAERSK_LINE             │ │
│ │                               │   │ TRANSIT: 14D  COST:$4,210│
│ │   Shanghai ──●──────●── LA    │   │ [DIRECT] RELIABILITY:94%│ │
│ │              Pacific          │   └─────────────────────────┘ │
│ │                               │                               │
│ │   [RECENTER] [LAYERS:TRAFFIC] │   ECONOMY                     │
│ │              [TIME_SLIDER]    │   ┌─────────────────────────┐ │
│ │                               │   │ COSCO_SHIPPING          │ │
│ └───────────────────────────────┘   │ TRANSIT: 19D  COST:$3,150│
│                                     │ 1TRANSSHIPMENT REL:88%  │ │
│                                     └─────────────────────────┘ │
│ CONSIGNMENT DETAILS                                             │
│ ┌─────────────────────────────────┐  BALANCED                   │
│ │ CARGO: 40FT HC CONTAINER (x12) │  ┌─────────────────────────┐ │
│ │ WEIGHT: 242,000 KG             │  │ MSC_GLOBAL              │ │
│ │ EST. DEPARTURE: 28 OCT 2023    │  │ TRANSIT: 16D  COST:$3,840│
│ └─────────────────────────────────┘  │ [DIRECT] RELIABILITY:91%│
│                                     └─────────────────────────┘ │
│                                                                 │
│ EMISSIONS IMPACT: 14.2 TONS CO2E                                │
│ ████████████████████████████░░░░░░░░░░                         │
│                                                                 │
│                                        [BOOK SHIPMENT →]        │
└─────────────────────────────────────────────────────────────────┘
```

**Component Structure**:
```typescript
// src/components/features/route-optimizer/
├── RouteMap.tsx            // Map visualization (use react-simple-maps)
├── RouteCard.tsx           // Individual route option
├── ConsignmentDetails.tsx  // Cargo info panel
├── EmissionsIndicator.tsx  // CO2 progress bar
└── RouteFilters.tsx        // Filter controls
```

### Git Commit (Phase 2)
```bash
git add .
git commit -m "feat(frontend): core feature UIs - classify, landed cost, route optimizer

- Implement HS Classification page with form, results, history
- Create Landed Cost Calculator with method comparison matrix
- Build Route Optimizer with map visualization and route cards
- Add responsive layouts matching design specifications
- Integrate mock data for development testing

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
```

---

# PHASE 3: AI ASSISTANT + TRACKING (Hours 6-9)

## Branch: `feature/ai-assistant-tracking`

### Tasks

#### 3.1 AI Trade Assistant Page

**File**: `src/app/(dashboard)/assistant/page.tsx`

**UI Components**:
```
┌─────────────────────────────────────────────────────────────────┐
│ ACTIVE MODULE                                   LIVE_CONTEXT    │
│ AI ASSISTANT                          SESSION ID: CHAT_8824_XQ  │
├─────────────────────────────────────────────────────────────────┤
│                                         ■ THINKING              │
│ ┌───────────────────────────────────┐                          │
│ │ SYSTEM_CORE:                      │  PROBABLE HS CODE        │
│ │ Hello Dima. I've analyzed your    │  ┌──────────────────────┐│
│ │ recent imports. Would you like to │  │ 8504.40.95           ││
│ │ review the classification for the │  │ Static converters;   ││
│ │ "Wireless Headphones" pending in  │  │ power supplies for   ││
│ │ your queue?                       │  │ ADP machines.        ││
│ │                                   │  └──────────────────────┘│
│ │ [REVIEW_PENDING] [NEW_CLASS]      │                          │
│ │ [IMPORT_POLICY_QUERY]             │  RULING REFERENCES       │
│ └───────────────────────────────────┘  • CROSS Ruling N302451  │
│                                        • NY N293245 (Similar)  │
│ ┌───────────────────────────────────┐                          │
│ │ USER:                             │  TRADE BARRIERS          │
│ │ I need the HS code for a "Solar-  │  [SECTION 301: 25%]     │
│ │ powered portable charger with     │  [LITHIUM BATTERY REGS] │
│ │ integrated LED flashlight"...     │  [REQUIRES UN38.3]      │
│ └───────────────────────────────────┘                          │
│                                                                 │
│ ┌───────────────────────────────────┐  KNOWLEDGE BASE          │
│ │ SYSTEM_CORE:                      │  WCO 2022 Nomenclature   │
│ │ Processing technical specs for    │  Index v1.4              │
│ │ solar-integrated power banks.     │  [REQUIRES UN38.3 CERT]  │
│ │ Based on GIR 3(b), composite      │                          │
│ │ goods are classified by...        │                          │
│ │ [● ● ●]                           │                          │
│ └───────────────────────────────────┘                          │
│                                                                 │
│ ┌─────────────────────────────────────────────────────────────┐│
│ │ Query trade database or ask for classification help... [→] ││
│ └─────────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────┘
```

**Component Structure**:
```typescript
// src/components/features/assistant/
├── ChatContainer.tsx       // Main chat area
├── MessageBubble.tsx       // Individual message
├── QuickActions.tsx        // Suggested action buttons
├── ContextPanel.tsx        // Right sidebar with live context
├── HSCodePreview.tsx       // HS code result in context
├── RulingReferences.tsx    // Related rulings list
├── TradeBarriers.tsx       // Tag list of barriers
└── ChatInput.tsx           // Input with send button
```

**AI Integration (FREE - Groq API)**:
```typescript
// src/lib/assistant.ts
// Using Groq API (FREE tier: 30 requests/min, Llama 3 70B)
export const sendMessage = async (message: string, context: AssistantContext) => {
  const response = await axios.post('http://localhost:8000/api/v1/assistant/chat', {
    message,
    context: {
      recentClassifications: context.recent,
      currentProduct: context.product,
      userProfile: context.user,
    }
  })
  return response.data
}

// Fallback to local Ollama if Groq rate limited
export const sendMessageLocal = async (message: string) => {
  const response = await axios.post('http://localhost:11434/api/generate', {
    model: 'mistral',
    prompt: message,
    stream: false
  })
  return response.data
}
```

**Streaming Implementation**:
```typescript
// Use Server-Sent Events for streaming responses
const streamResponse = async (prompt: string) => {
  const eventSource = new EventSource(
    `http://localhost:8000/api/v1/assistant/stream?prompt=${encodeURIComponent(prompt)}`
  )

  eventSource.onmessage = (event) => {
    const data = JSON.parse(event.data)
    appendToMessage(data.token)
  }
}
```

#### 3.2 Live Cargo Tracking Page

**File**: `src/app/(dashboard)/tracking/page.tsx`

**UI Components**:
```
┌─────────────────────────────────────────────────────────────────┐
│ ACTIVE SHIPMENT                                LIVE NOTIFICATIONS│
│ CARGO LOCATOR                    TRACKING ID: #MSC-992-XQ       │
├─────────────────────────────────────────────────────────────────┤
│                                              ● STREAMING        │
│ ┌───────────────────────────────────┐                          │
│ │                                   │  02 MINS AGO              │
│ │    SHANGHAI (CNSHA)               │  WEATHER ADVISORY         │
│ │         ●                         │  Strong headwinds detected│
│ │          \                        │  in Sector 4. Fuel        │
│ │           \   MSC OSCAR           │  optimization reroute     │
│ │            \    ◉                 │  engaged.                 │
│ │             \                     │                           │
│ │              ────────●            │  19 MINS AGO              │
│ │         LOS ANGELES (USLAX)       │  AIS UPDATE               │
│ │                                   │  MSC OSCAR speed at       │
│ │                                   │  18.4 knots. Signal OK.   │
│ └───────────────────────────────────┘                          │
│                                        1 HOUR AGO               │
│ DELIVERY PIPELINE                      DOCUMENTATION            │
│ ┌─────────────────────────────────────┐ Digital B/L verified   │
│ │ [■]DEPARTED → [□]TRANSIT →         │ by US Customs portal.   │
│ │ [□]EST.ARR → [□]CUSTOMS            │                          │
│ │                                     │ 2 HOURS AGO             │
│ │ Shanghai    Pacific   LA Gateway   │ SYSTEM                   │
│ │ 21 OCT      CURRENT   29 OCT       │ Cargo temp sensors       │
│ │                       PENDING      │ calibrated. 42 reefers   │
│ └─────────────────────────────────────┘ at -18°C.               │
│                                                                 │
│ ESTIMATED REMAINING: 05D 14H 22M                                │
│ RELIABILITY INDEX: ████████████████░░░░ 82%                     │
│                                                                 │
│                                   [GENERATE STATUS REPORT]      │
└─────────────────────────────────────────────────────────────────┘
```

**Component Structure**:
```typescript
// src/components/features/tracking/
├── TrackingMap.tsx         // Map with vessel position
├── NotificationFeed.tsx    // Right-side notification list
├── NotificationCard.tsx    // Individual notification
├── DeliveryPipeline.tsx    // Progress steps
├── CountdownTimer.tsx      // ETA countdown
├── ReliabilityIndex.tsx    // Progress bar
└── StatusReport.tsx        // Report generator
```

**WebSocket Integration**:
```typescript
// src/hooks/useTrackingWebSocket.ts
export const useTrackingWebSocket = (trackingId: string) => {
  const [notifications, setNotifications] = useState<Notification[]>([])
  const [position, setPosition] = useState<VesselPosition>()

  useEffect(() => {
    const ws = new WebSocket(`ws://localhost:8002/tracking/${trackingId}`)

    ws.onmessage = (event) => {
      const data = JSON.parse(event.data)
      if (data.type === 'position') setPosition(data.payload)
      if (data.type === 'notification') {
        setNotifications(prev => [data.payload, ...prev])
      }
    }

    return () => ws.close()
  }, [trackingId])

  return { notifications, position }
}
```

### Git Commit (Phase 3)
```bash
git add .
git commit -m "feat(frontend): AI assistant and live cargo tracking

- Implement AI Trade Assistant with Claude integration
- Create chat interface with message streaming
- Add context panel with live HS code predictions
- Build cargo tracking page with map visualization
- Implement WebSocket for real-time notifications
- Add delivery pipeline progress component

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
```

---

# PHASE 4: COMPLIANCE + ANALYTICS + POLISH (Hours 9-12)

## Branch: `feature/frontend-complete`

### Tasks

#### 4.1 Compliance Page

**File**: `src/app/(dashboard)/compliance/page.tsx`

**UI Components**:
```
┌─────────────────────────────────────────────────────────────────┐
│ ACTIVE MODULE                            COMPLIANCE CHECKLIST   │
│ TRADE COMPLIANCE                        REGION: US-APAC         │
├─────────────────────────────────────────────────────────────────┤
│                                              65% READY          │
│ REGULATORY REQUIREMENTS                                         │
│ ┌────────────────────────┐  ┌────────────────────────┐         │
│ │ [ACTIVE]               │  │ [WARNING]              │         │
│ │ PARTNER GOV AGENCIES   │  │ AD/CVD SCRUTINY       │         │
│ │ (PGA)                  │  │                        │         │
│ │ Requires FDA notif.    │  │ Anti-dumping duties   │         │
│ │ for lithium battery    │  │ applicable to certain │         │
│ │ housing components.    │  │ aluminum extrusions   │         │
│ │ 48h lead time rec.     │  │ from CN region.       │         │
│ │                        │  │                        │         │
│ │ REF: 21 CFR 801        │  │ REF: ITA-2023-01      │         │
│ └────────────────────────┘  └────────────────────────┘         │
│                                                                 │
│ DOCUMENT TRACKER                                                │
│ □ Commercial Invoice      [VERIFIED]                           │
│ □ Packing List           [VERIFIED]                           │
│ □ Certificate of Origin  [MISSING]                            │
│ □ TSCA Toxic Substance   [REQUIRED]                           │
│                                                                 │
│ RESTRICTED ITEMS LIST (BY HS SUBHEAD)                          │
│ ┌──────────────┬──────────────────────┬────────────┬─────────┐ │
│ │ HS CODE      │ DESCRIPTION          │ RESTRICTION│ LICENSE │ │
│ ├──────────────┼──────────────────────┼────────────┼─────────┤ │
│ │ 8504.40.95   │ Static Converters    │ ECCN Ctrl  │[REQUIRED]│ │
│ │ 8471.50.01   │ Encryption Units     │ NSA Review │[PENDING] │ │
│ │ 2805.11.00   │ Lithium Concentrates │ Strategic  │[EXEMPT]  │ │
│ └──────────────┴──────────────────────┴────────────┴─────────┘ │
│                                                                 │
│ AUDIT RISK LEVEL: LOW                                          │
│ Current entity rating 98/100. Previous 12 months: 0 infractions│
│                                                                 │
│                                          [RUN FULL AUDIT →]    │
└─────────────────────────────────────────────────────────────────┘
```

#### 4.2 Historical Analytics Page (UI + Export)

**File**: `src/app/(dashboard)/analytics/page.tsx`

**Install Export Libraries**:
```bash
npm install jspdf jspdf-autotable xlsx file-saver html2canvas
npm install -D @types/file-saver
```

```
┌─────────────────────────────────────────────────────────────────┐
│ REPORTS                                      KEY PERFORMANCE    │
│ HISTORICAL ANALYTICS                   PERIOD: Q3 2023 - PRESENT│
├─────────────────────────────────────────────────────────────────┤
│                                              [LIVE] ♥           │
│ TRADE VOLUMES BY CATEGORY    CLASSIFICATION ACCURACY TREND     │
│ ┌────────────────────────┐  ┌────────────────────────┐         │
│ │ █████ █████████ ████   │  │  ─────────────────────  │         │
│ │ █████ █████████ ████   │  │         ──────         │         │
│ │ █████ █████████ ████   │  │  ────────              │         │
│ │ ───────────────────────│  │                        │         │
│ │ CAT_01  CAT_07         │  │  Steady 12% improvement│         │
│ └────────────────────────┘  │  in ML model precision │         │
│                             │  over 6 months.        │         │
│                             └────────────────────────┘         │
│                                                                 │
│ DUTY SAVINGS REALIZED (FTA OPTIMIZATION)                       │
│ ┌─────────────────────────────────────────────────────────────┐│
│ │                                                             ││
│ │  [POTENTIAL]  [REALIZED]     $412,800                       ││
│ │      ████        ████         TOTAL SAVINGS YTD             ││
│ │      ████        ████                                        ││
│ │      ████        ████        82% efficiency in FTA          ││
│ │      ████        ████        utilization for CN-US routes   ││
│ │                                                             ││
│ └─────────────────────────────────────────────────────────────┘│
│                                                                 │
│ KEY METRICS:                                                    │
│ AVG CLASSIFICATION TIME: 1.4S (↓0.8s vs prev qtr)              │
│ AUDIT RISK INDEX: 0.04 (threshold: 0.15)                       │
│ TOP HS CHAPTER: 8517.12 (Telephones)                           │
│                                                                 │
│ ┌─────────────────────────────────────────────────────────────┐│
│ │ EXPORT OPTIONS:                                             ││
│ │ [📄 Download PDF] [📊 Download Excel] [📋 Download CSV]     ││
│ └─────────────────────────────────────────────────────────────┘│
│                                                                 │
│                                    [GENERATE Q3 REPORT →]       │
└─────────────────────────────────────────────────────────────────┘
```

**Export Component**:
```typescript
// src/components/features/analytics/ExportButtons.tsx
import jsPDF from 'jspdf'
import autoTable from 'jspdf-autotable'
import * as XLSX from 'xlsx'
import { saveAs } from 'file-saver'
import html2canvas from 'html2canvas'

export const ExportButtons = ({ data, chartRef }: ExportProps) => {

  const exportPDF = async () => {
    const doc = new jsPDF()

    // Add header
    doc.setFontSize(20)
    doc.text('TradeOptimize AI - Analytics Report', 20, 20)
    doc.setFontSize(12)
    doc.text(`Generated: ${new Date().toLocaleDateString()}`, 20, 30)

    // Add summary stats
    doc.setFontSize(14)
    doc.text('Executive Summary', 20, 45)
    doc.setFontSize(10)
    doc.text(`Total Classifications: ${data.totalClassifications}`, 25, 55)
    doc.text(`Average Accuracy: ${data.avgAccuracy}%`, 25, 62)
    doc.text(`Duty Savings: $${data.dutySavings.toLocaleString()}`, 25, 69)

    // Add chart as image
    if (chartRef.current) {
      const canvas = await html2canvas(chartRef.current)
      const imgData = canvas.toDataURL('image/png')
      doc.addImage(imgData, 'PNG', 20, 80, 170, 100)
    }

    // Add data table
    autoTable(doc, {
      head: [['HS Code', 'Classifications', 'Accuracy', 'Savings']],
      body: data.tableData.map(row => [
        row.hsCode,
        row.count,
        `${row.accuracy}%`,
        `$${row.savings.toLocaleString()}`
      ]),
      startY: 190
    })

    doc.save('tradeoptimize-report.pdf')
  }

  const exportExcel = () => {
    const wb = XLSX.utils.book_new()

    // Summary sheet
    const summaryData = [
      ['TradeOptimize AI - Analytics Report'],
      ['Generated:', new Date().toLocaleDateString()],
      [],
      ['Metric', 'Value'],
      ['Total Classifications', data.totalClassifications],
      ['Average Accuracy', `${data.avgAccuracy}%`],
      ['Duty Savings', `$${data.dutySavings.toLocaleString()}`],
    ]
    const summarySheet = XLSX.utils.aoa_to_sheet(summaryData)
    XLSX.utils.book_append_sheet(wb, summarySheet, 'Summary')

    // Classifications sheet
    const classSheet = XLSX.utils.json_to_sheet(data.classifications)
    XLSX.utils.book_append_sheet(wb, classSheet, 'Classifications')

    // Savings sheet
    const savingsSheet = XLSX.utils.json_to_sheet(data.savings)
    XLSX.utils.book_append_sheet(wb, savingsSheet, 'Duty Savings')

    // Save file
    const excelBuffer = XLSX.write(wb, { bookType: 'xlsx', type: 'array' })
    const blob = new Blob([excelBuffer], { type: 'application/octet-stream' })
    saveAs(blob, 'tradeoptimize-report.xlsx')
  }

  const exportCSV = () => {
    const csvContent = data.tableData
      .map(row => `${row.hsCode},${row.count},${row.accuracy},${row.savings}`)
      .join('\n')
    const header = 'HS Code,Classifications,Accuracy,Savings\n'
    const blob = new Blob([header + csvContent], { type: 'text/csv' })
    saveAs(blob, 'tradeoptimize-data.csv')
  }

  return (
    <div className="flex gap-2">
      <Button onClick={exportPDF} variant="outline">
        📄 Download PDF
      </Button>
      <Button onClick={exportExcel} variant="outline">
        📊 Download Excel
      </Button>
      <Button onClick={exportCSV} variant="ghost">
        📋 Download CSV
      </Button>
    </div>
  )
}
```

#### 4.3 Onboarding Wizard (UI Only)

**File**: `src/app/onboarding/page.tsx`

```
┌─────────────────────────────────────────────────────────────────┐
│ INITIALIZATION                              PROVISIONING STATUS │
│ SYSTEM ONBOARDING                          STEP: 02 / 03        │
├─────────────────────────────────────────────────────────────────┤
│                                              ■ SYNCING          │
│ ━━━━━━━━━━━━━━━━━━━━ ━━━━━━━━━━━━ ━━━━━━━                      │
│                                                                 │
│ MODULE 02                                   IMPORT PROGRESS     │
│ PRODUCT CATALOG IMPORT                      ┌─────────────────┐ │
│                                             │     65.2%       │ │
│ Map your existing inventory data to our     │  4,238 / 6,500  │ │
│ AI engine to begin automated HS             │     ITEMS       │ │
│ classification and landed cost estimation.  │   EST: 45s      │ │
│                                             └─────────────────┘ │
│ SOURCE DATA PROVIDER                                            │
│ ┌─────────────────────────────────────────┐                    │
│ │ ERP System Export (CSV/XLSX)         ▼  │  INTEGRATION STACK │
│ └─────────────────────────────────────────┘  ┌───────────────┐ │
│                                              │Global Customs │ │
│ DEFAULT CURRENCY        DATA REFRESH RATE   │API [LINKED]   │ │
│ ┌────────────────────┐ ┌─────────────────┐  ├───────────────┤ │
│ │ USD - US Dollar  ▼ │ │ Real-time (API)│  │ERP Connector  │ │
│ └────────────────────┘ └─────────────────┘  │[PENDING]      │ │
│                                              ├───────────────┤ │
│ ┌─────────────────────────────────────────┐  │Carrier Network│ │
│ │         UPLOAD_CATALOG_FILE             │  │[PENDING]      │ │
│ │   Drag and drop your .csv, .xlsx or     │  └───────────────┘ │
│ │              .json file                  │                    │
│ └─────────────────────────────────────────┘                    │
│                                                                 │
│ ┌─────────────────────────────────────────┐                    │
│ │          PROCEED TO INTEGRATIONS →      │                    │
│ └─────────────────────────────────────────┘                    │
│                                                                 │
│ SYSTEM NOTICE: All data transfers encrypted via AES-256.       │
└─────────────────────────────────────────────────────────────────┘
```

#### 4.4 Final Polish

**Tasks**:
1. Add loading states and skeletons for all pages
2. Implement error boundaries
3. Add keyboard shortcuts
4. Optimize images and assets
5. Test responsive layouts
6. Add page transitions

### Git Commit (Phase 4)
```bash
git add .
git commit -m "feat(frontend): compliance, analytics, onboarding + polish

- Implement Trade Compliance page with document tracker
- Create Historical Analytics dashboard with charts
- Build System Onboarding wizard (3-step flow)
- Add loading states and skeleton screens
- Implement error boundaries
- Optimize for production build

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
```

---

# API CONTRACTS (For Integration with Account 2)

## Expected Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/v1/classify` | POST | HS Code classification |
| `/api/v1/landed-cost` | POST | Landed cost calculation |
| `/api/v1/routes/optimize` | POST | Route optimization |
| `/api/v1/compliance/check` | POST | Compliance verification |
| `/api/v1/assistant/chat` | POST | AI assistant message |
| `/api/v1/assistant/stream` | GET (SSE) | Streaming response |
| `/ws/tracking/:id` | WS | Real-time tracking |

## Request/Response Types

```typescript
// Classification
interface ClassifyRequest {
  description: string
  imageUrl?: string
  originCountry: string
  destinationCountry: string
}

interface ClassifyResponse {
  hsCode: string
  confidence: number
  description: string
  dutyRate: number
  section301Rate?: number
  reasoning: string
  similarRulings: { id: string; similarity: number }[]
}

// Landed Cost
interface LandedCostRequest {
  hsCode: string
  unitPrice: number
  quantity: number
  originCountry: string
  destinationCountry: string
  shippingMode: 'ocean_fcl' | 'ocean_lcl' | 'air' | 'express'
  incoterm: 'FOB' | 'CIF' | 'DDP'
}

interface LandedCostResponse {
  totalLandedCost: number
  perUnitCost: number
  breakdown: {
    productValue: number
    baseDuty: number
    section301: number
    mpf: number
    hmf: number
    freight: number
    insurance: number
  }
  effectiveDutyRate: number
  riskAdvisory?: string
}
```

---

# DELIVERABLES CHECKLIST

## Phase 1 (Hours 0-3)
- [ ] Project scaffolded with Next.js 14
- [ ] Design system configured (dark theme)
- [ ] Base UI components created
- [ ] Layout components (Sidebar, Header)
- [ ] Zustand store configured
- [ ] TypeScript interfaces defined

## Phase 2 (Hours 3-6)
- [ ] HS Classification page complete
- [ ] Landed Cost Calculator page complete
- [ ] Route Optimizer page complete
- [ ] API integration functions ready
- [ ] Mock data for development

## Phase 3 (Hours 6-9)
- [ ] AI Assistant page complete
- [ ] Claude integration working
- [ ] Streaming responses implemented
- [ ] Cargo Tracking page complete
- [ ] WebSocket integration ready

## Phase 4 (Hours 9-12)
- [ ] Compliance page complete
- [ ] Analytics dashboard complete
- [ ] Onboarding wizard complete
- [ ] Loading states added
- [ ] Production optimizations

---

*PRD Version: 1.0*
*Account: Claude Pro Account 1*
*Focus: Frontend + AI Services*
