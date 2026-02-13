# TradeOptimize AI - Hackathon Implementation Guide
## Main Features & Flowcharts

---

## HACKATHON MVP SCOPE (48-72 Hours)

### Priority Features for Demo

| Priority | Feature | Demo Impact | Complexity |
|----------|---------|-------------|------------|
| P0 | AI HS Classification | HIGH | Medium |
| P0 | Landed Cost Calculator | HIGH | Medium |
| P1 | Route Comparison (2-3 routes) | HIGH | Low |
| P1 | Savings Dashboard | HIGH | Low |
| P2 | FTA Eligibility Check | Medium | Medium |
| P2 | Compliance Screening | Medium | Low |

---

## FEATURE 1: AI-POWERED HS CLASSIFICATION

### What to Build
- Text input for product description
- Image upload (optional, bonus points)
- AI classification with confidence score
- Explanation of why that HS code

### Flowchart

```
┌─────────────────────────────────────────────────────────────┐
│                    HS CLASSIFICATION FLOW                    │
└─────────────────────────────────────────────────────────────┘

    ┌─────────────────┐
    │  USER INPUT     │
    │  ─────────────  │
    │  "Wireless BT   │
    │  headphones     │
    │  with ANC"      │
    └────────┬────────┘
             │
             ▼
    ┌─────────────────┐
    │  PRE-PROCESSING │
    │  ─────────────  │
    │  • Clean text   │
    │  • Extract      │
    │    keywords     │
    │  • Normalize    │
    └────────┬────────┘
             │
             ▼
    ┌─────────────────┐
    │  LLM CLASSIFIER │
    │  (GPT-4/Claude) │
    │  ─────────────  │
    │  Prompt:        │
    │  "Classify this │
    │  product into   │
    │  HS code..."    │
    └────────┬────────┘
             │
             ▼
    ┌─────────────────┐
    │  OUTPUT         │
    │  ─────────────  │
    │  HS: 8518.30.20 │
    │  Conf: 94%      │
    │  Reason: "..."  │
    └─────────────────┘
```

### API Endpoint
```
POST /api/classify
{
  "description": "Wireless Bluetooth headphones with noise cancellation",
  "image_url": null  // optional
}

Response:
{
  "hs_code": "8518.30.20",
  "confidence": 94,
  "description": "Headphones and earphones",
  "chapter": "85 - Electrical machinery",
  "reasoning": "Classified as headphones under Chapter 85..."
}
```

### Tech Stack for This Feature
- Frontend: React form with text input
- Backend: FastAPI endpoint
- AI: OpenAI GPT-4 or Claude API
- Prompt engineering for HS classification

---

## FEATURE 2: LANDED COST CALCULATOR

### What to Build
- Input: Product value, quantity, origin, destination
- Calculate: Duties, taxes, fees, freight
- Output: Total landed cost breakdown

### Flowchart

```
┌─────────────────────────────────────────────────────────────┐
│                  LANDED COST CALCULATION FLOW                │
└─────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────┐
│                        USER INPUTS                            │
├──────────────┬──────────────┬──────────────┬────────────────┤
│ Product      │ Origin       │ Destination  │ Shipping       │
│ ──────────── │ ──────────── │ ──────────── │ ──────────────│
│ HS Code      │ Country: CN  │ Country: US  │ Mode: Ocean    │
│ Value: $45   │ Port: CNSHA  │ Port: USLAX  │ Incoterm: FOB  │
│ Qty: 1000    │              │              │                │
└──────┬───────┴──────┬───────┴──────┬───────┴───────┬────────┘
       │              │              │               │
       └──────────────┴──────┬───────┴───────────────┘
                             │
                             ▼
       ┌─────────────────────────────────────────────┐
       │           CALCULATION ENGINE                 │
       │  ─────────────────────────────────────────  │
       │                                             │
       │  1. Lookup duty rate for HS code            │
       │  2. Check for Section 301/AD-CVD            │
       │  3. Calculate MPF (0.3464%)                 │
       │  4. Calculate HMF (0.125%)                  │
       │  5. Estimate freight cost                   │
       │  6. Add insurance (0.5% of CIF)             │
       │                                             │
       └─────────────────────┬───────────────────────┘
                             │
                             ▼
       ┌─────────────────────────────────────────────┐
       │              COST BREAKDOWN                  │
       │  ─────────────────────────────────────────  │
       │                                             │
       │  Product Value:      $45,000.00             │
       │  ─────────────────────────────────          │
       │  Base Duty (0%):     $     0.00             │
       │  Section 301 (25%):  $11,250.00             │
       │  MPF:                $   155.88             │
       │  HMF:                $    56.25             │
       │  ─────────────────────────────────          │
       │  Freight:            $ 1,800.00             │
       │  Insurance:          $   234.00             │
       │  ═════════════════════════════════          │
       │  TOTAL LANDED:       $58,496.13             │
       │  Cost Per Unit:      $    58.50             │
       │                                             │
       └─────────────────────────────────────────────┘
```

### API Endpoint
```
POST /api/landed-cost
{
  "hs_code": "8518.30.20",
  "product_value": 45000,
  "quantity": 1000,
  "origin_country": "CN",
  "destination_country": "US",
  "shipping_mode": "ocean",
  "incoterm": "FOB"
}

Response:
{
  "total_landed_cost": 58496.13,
  "cost_per_unit": 58.50,
  "breakdown": {
    "product_value": 45000,
    "base_duty": 0,
    "section_301": 11250,
    "mpf": 155.88,
    "hmf": 56.25,
    "freight": 1800,
    "insurance": 234
  },
  "effective_duty_rate": 25.0
}
```

### Duty Rate Data (Hardcode for Hackathon)
```javascript
const DUTY_RATES = {
  "8518.30": { mfn: 0, section_301: 25 },  // Headphones
  "6110.20": { mfn: 16.5, section_301: 7.5 },  // Sweaters
  "9503.00": { mfn: 0, section_301: 25 },  // Toys
  // Add 10-20 common HS codes
};
```

---

## FEATURE 3: ROUTE COMPARISON & SAVINGS

### What to Build
- Compare direct route vs alternative routes
- Show savings potential
- Recommend best option

### Flowchart

```
┌─────────────────────────────────────────────────────────────┐
│                   ROUTE COMPARISON FLOW                      │
└─────────────────────────────────────────────────────────────┘

                    ┌─────────────────────┐
                    │   BASELINE ROUTE    │
                    │   CN → US Direct    │
                    │   Cost: $58,496     │
                    │   Duty: $11,250     │
                    └──────────┬──────────┘
                               │
            ┌──────────────────┼──────────────────┐
            │                  │                  │
            ▼                  ▼                  ▼
   ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
   │  ROUTE A        │ │  ROUTE B        │ │  ROUTE C        │
   │  CN → VN → US   │ │  CN → MX → US   │ │  CN → US (FTZ)  │
   │  ─────────────  │ │  ─────────────  │ │  ─────────────  │
   │                 │ │                 │ │                 │
   │  Assembly in VN │ │  USMCA route    │ │  Foreign Trade  │
   │  avoids Sec 301 │ │  0% duty        │ │  Zone benefits  │
   │                 │ │                 │ │                 │
   │  Extra Cost:    │ │  Extra Cost:    │ │  Extra Cost:    │
   │  +$3,000        │ │  +$8,000        │ │  +$500          │
   │                 │ │                 │ │                 │
   │  New Duty: $0   │ │  New Duty: $0   │ │  Duty Deferred  │
   │                 │ │                 │ │                 │
   │  ─────────────  │ │  ─────────────  │ │  ─────────────  │
   │  Total: $50,246 │ │  Total: $53,246 │ │  Total: $56,996 │
   │  SAVE: $8,250   │ │  SAVE: $5,250   │ │  SAVE: $1,500   │
   │  ─────────────  │ │  ─────────────  │ │  ─────────────  │
   │  ⭐ RECOMMENDED │ │                 │ │                 │
   └─────────────────┘ └─────────────────┘ └─────────────────┘
```

### API Endpoint
```
POST /api/compare-routes
{
  "hs_code": "8518.30.20",
  "product_value": 45000,
  "origin_country": "CN",
  "destination_country": "US"
}

Response:
{
  "baseline": {
    "route": "CN → US",
    "total_cost": 58496,
    "duty": 11250
  },
  "alternatives": [
    {
      "route": "CN → VN → US",
      "total_cost": 50246,
      "savings": 8250,
      "strategy": "Vietnam assembly avoids Section 301",
      "recommended": true
    },
    {
      "route": "CN → MX → US",
      "total_cost": 53246,
      "savings": 5250,
      "strategy": "USMCA qualification"
    }
  ]
}
```

---

## FEATURE 4: SAVINGS DASHBOARD

### What to Build
- Summary cards showing potential savings
- Visual comparison chart
- Key metrics display

### UI Components

```
┌─────────────────────────────────────────────────────────────┐
│                    SAVINGS DASHBOARD                         │
└─────────────────────────────────────────────────────────────┘

┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│  CURRENT COST   │  │  OPTIMIZED COST │  │  YOUR SAVINGS   │
│  ─────────────  │  │  ─────────────  │  │  ─────────────  │
│                 │  │                 │  │                 │
│   $58,496       │  │   $50,246       │  │   $8,250        │
│                 │  │                 │  │   (14.1%)       │
│  Direct Import  │  │  Via Vietnam    │  │                 │
└─────────────────┘  └─────────────────┘  └─────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                    COST BREAKDOWN CHART                      │
│  ─────────────────────────────────────────────────────────  │
│                                                              │
│  Direct:    ████████████████████████████████████ $58,496    │
│  Vietnam:   ██████████████████████████████░░░░░░ $50,246    │
│  Mexico:    ███████████████████████████████░░░░░ $53,246    │
│  FTZ:       █████████████████████████████████░░░ $56,996    │
│                                                              │
│  ░░░ = Savings                                               │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                    ANNUAL PROJECTION                         │
│  ─────────────────────────────────────────────────────────  │
│                                                              │
│  If you ship 12x per year:                                   │
│                                                              │
│  Current Annual Cost:    $701,952                            │
│  Optimized Annual Cost:  $602,952                            │
│  ─────────────────────────────────────────────               │
│  ANNUAL SAVINGS:         $99,000                             │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## IMPLEMENTATION CHECKLIST

### Day 1: Foundation (8-10 hours)
- [ ] Project setup (Next.js + FastAPI)
- [ ] Basic UI layout and navigation
- [ ] Database schema (PostgreSQL/SQLite)
- [ ] Authentication (optional, use mock)
- [ ] Ocr 

### Day 2: Core Features (10-12 hours)
- [ ] HS Classification API + UI
- [ ] Landed Cost Calculator API + UI
- [ ] Hardcode duty rates for 20 HS codes
- [ ] Basic results display

### Day 3: Polish & Demo (8-10 hours)
- [ ] Route comparison feature
- [ ] Savings dashboard
- [ ] UI polish and animations
- [ ] Demo flow preparation
- [ ] Pitch deck finalization

---

## TECH STACK SUMMARY

```
FRONTEND                    BACKEND                     AI/DATA
─────────                   ───────                     ───────
Next.js 14                  FastAPI (Python)            OpenAI GPT-4 API
TypeScript                  or Node.js/Express          or Claude API
Tailwind CSS
shadcn/ui                   PostgreSQL/SQLite           Hardcoded tariff
Recharts (charts)           Redis (optional)            data (JSON)
```

---

## DEMO SCRIPT (3 minutes)

1. **Hook (30s)**: "Companies overpay $100B in duties annually..."
2. **Problem (30s)**: Show manual classification pain
3. **Demo Classification (45s)**: Type product → Get HS code instantly
4. **Demo Landed Cost (45s)**: Show full cost breakdown
5. **Demo Savings (30s)**: Compare routes, show $8K savings
6. **Close (30s)**: Market size, business model, ask

---

## KEY METRICS TO SHOW

- Classification time: <3 seconds
- Accuracy: 94% confidence
- Savings identified: 15-25% duty reduction
- Routes compared: 3-4 alternatives
- Annual savings projection: $50K-$100K

---

*Document Version: 1.0*
*For: Thapar Hackathon*
