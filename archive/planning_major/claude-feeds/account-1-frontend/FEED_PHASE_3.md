# Frontend Phase 3: Landed Cost + Compliance (Hours 6-9)
## Branch: `feature/fe-phase-3-cost-compliance`

---

## AI/ML IN THIS PHASE

```
┌─────────────────────────────────────────────────────────┐
│  NO DIRECT AI - API CALLS TO BACKEND                   │
│  ────────────────────────────────────                  │
│                                                         │
│  Landed Cost Calculator:                                │
│  → POST /api/v1/landed-cost/calculate                  │
│  → Uses LIVE USITC tariff API (not AI)                 │
│  → Section 301 rates hardcoded                         │
│                                                         │
│  Compliance Check:                                      │
│  → POST /api/v1/compliance/check                       │
│  → Uses OFAC screening (rule-based, not AI)            │
│  → Document requirements are rule-based                │
│                                                         │
│  These features use LIVE DATA, not AI!                 │
└─────────────────────────────────────────────────────────┘
```

---

## Prerequisites
- Phase FE-2 must be merged to develop
- Pull latest: `git checkout develop && git pull`

---

## Your Mission

Build the Landed Cost Calculator and Trade Compliance pages with toast notifications and loading skeletons.

---

## Deliverables

### 1. Landed Cost Page (`/landed-cost`)

```
frontend/src/
├── app/(dashboard)/landed-cost/page.tsx
└── components/features/landed-cost/
    ├── CostCalculatorForm.tsx
    ├── CostBreakdown.tsx
    ├── DutyRateDisplay.tsx
    └── CostPanel.tsx           # Right panel
```

#### CostCalculatorForm.tsx
```tsx
'use client'
import { useState } from 'react'
import { Input } from '@/components/ui/Input'
import { Button } from '@/components/ui/Button'

interface FormData {
  hsCode: string
  productValue: number
  quantity: number
  originCountry: string
  destinationCountry: string
  shippingMode: 'ocean' | 'air' | 'rail'
  incoterm: string
}

export function CostCalculatorForm({ onSubmit }: { onSubmit: (data: FormData) => void }) {
  const [form, setForm] = useState<FormData>({
    hsCode: '',
    productValue: 0,
    quantity: 1,
    originCountry: 'CN',
    destinationCountry: 'US',
    shippingMode: 'ocean',
    incoterm: 'FOB',
  })

  return (
    <div className="grid grid-cols-2 gap-6">
      <div className="space-y-4">
        <div className="label">PRODUCT DETAILS</div>
        <Input
          label="HS CODE"
          value={form.hsCode}
          onChange={(e) => setForm({ ...form, hsCode: e.target.value })}
          placeholder="8504.40.95"
        />
        <div className="grid grid-cols-2 gap-4">
          <Input
            label="VALUE (USD)"
            type="number"
            value={form.productValue}
            onChange={(e) => setForm({ ...form, productValue: Number(e.target.value) })}
          />
          <Input
            label="QUANTITY"
            type="number"
            value={form.quantity}
            onChange={(e) => setForm({ ...form, quantity: Number(e.target.value) })}
          />
        </div>
      </div>

      <div className="space-y-4">
        <div className="label">SHIPPING DETAILS</div>
        <div className="grid grid-cols-2 gap-4">
          <div>
            <div className="label">ORIGIN</div>
            <select
              value={form.originCountry}
              onChange={(e) => setForm({ ...form, originCountry: e.target.value })}
              className="w-full bg-transparent border border-dark p-3"
            >
              <option value="CN">China</option>
              <option value="VN">Vietnam</option>
              <option value="IN">India</option>
              <option value="DE">Germany</option>
            </select>
          </div>
          <div>
            <div className="label">DESTINATION</div>
            <select
              value={form.destinationCountry}
              onChange={(e) => setForm({ ...form, destinationCountry: e.target.value })}
              className="w-full bg-transparent border border-dark p-3"
            >
              <option value="US">United States</option>
              <option value="CA">Canada</option>
              <option value="MX">Mexico</option>
              <option value="GB">United Kingdom</option>
            </select>
          </div>
        </div>

        <div className="label">SHIPPING MODE</div>
        <div className="flex gap-4">
          {['ocean', 'air', 'rail'].map((mode) => (
            <button
              key={mode}
              onClick={() => setForm({ ...form, shippingMode: mode as any })}
              className={`px-4 py-2 border font-pixel text-sm ${
                form.shippingMode === mode ? 'bg-dark text-canvas' : 'border-dark'
              }`}
            >
              {mode.toUpperCase()}
            </button>
          ))}
        </div>
      </div>

      <div className="col-span-2">
        <Button onClick={() => onSubmit(form)} className="w-full">
          CALCULATE_LANDED_COST ›
        </Button>
      </div>
    </div>
  )
}
```

#### CostBreakdown.tsx
```tsx
interface CostBreakdownProps {
  data: {
    productValue: number
    baseDuty: number
    section301: number
    mpf: number
    hmf: number
    freight: number
    insurance: number
    totalLanded: number
    perUnit: number
  }
}

export function CostBreakdown({ data }: CostBreakdownProps) {
  const rows = [
    { label: 'Product Value', value: data.productValue },
    { label: 'Base Duty', value: data.baseDuty, indent: true },
    { label: 'Section 301 Tariff', value: data.section301, indent: true, warning: data.section301 > 0 },
    { label: 'MPF', value: data.mpf, indent: true },
    { label: 'HMF', value: data.hmf, indent: true },
    { label: 'Freight', value: data.freight },
    { label: 'Insurance', value: data.insurance },
  ]

  return (
    <div className="border border-dark p-6 space-y-3">
      <div className="label">COST BREAKDOWN</div>
      {rows.map((row) => (
        <div key={row.label} className={`flex justify-between text-sm ${row.indent ? 'pl-4' : ''}`}>
          <span className={row.warning ? 'text-warning' : ''}>{row.label}</span>
          <span className="font-pixel">${row.value.toLocaleString('en-US', { minimumFractionDigits: 2 })}</span>
        </div>
      ))}
      <div className="border-t border-dark pt-3 mt-3">
        <div className="flex justify-between font-bold">
          <span>TOTAL LANDED</span>
          <span className="font-pixel text-xl">${data.totalLanded.toLocaleString('en-US', { minimumFractionDigits: 2 })}</span>
        </div>
        <div className="flex justify-between text-sm opacity-60 mt-1">
          <span>Per Unit</span>
          <span>${data.perUnit.toFixed(2)}</span>
        </div>
      </div>
    </div>
  )
}
```

### 2. Compliance Page (`/compliance`)

```
frontend/src/
├── app/(dashboard)/compliance/page.tsx
└── components/features/compliance/
    ├── ComplianceChecker.tsx
    ├── RiskIndicator.tsx
    ├── DocumentChecklist.tsx
    └── SanctionsAlert.tsx
```

#### RiskIndicator.tsx
```tsx
interface RiskIndicatorProps {
  label: string
  status: 'clear' | 'warning' | 'danger' | 'pending'
  details?: string
}

export function RiskIndicator({ label, status, details }: RiskIndicatorProps) {
  const statusConfig = {
    clear: { icon: '✓', color: 'text-green-500', bg: 'bg-green-500/10' },
    warning: { icon: '!', color: 'text-yellow-500', bg: 'bg-yellow-500/10' },
    danger: { icon: '✕', color: 'text-warning', bg: 'bg-warning/10' },
    pending: { icon: '○', color: 'text-[#888]', bg: 'bg-[#333]/10' },
  }

  const config = statusConfig[status]

  return (
    <div className={`flex items-start gap-3 p-3 ${config.bg}`}>
      <span className={`${config.color} font-pixel`}>{config.icon}</span>
      <div className="flex-1">
        <div className="font-medium text-sm">{label}</div>
        {details && <div className="text-xs opacity-60 mt-1">{details}</div>}
      </div>
    </div>
  )
}
```

#### DocumentChecklist.tsx
```tsx
interface Document {
  name: string
  status: 'verified' | 'missing' | 'pending'
}

export function DocumentChecklist({ documents }: { documents: Document[] }) {
  const statusIcon = {
    verified: '☑',
    missing: '☐',
    pending: '◐',
  }

  return (
    <div className="border border-dark p-4">
      <div className="label mb-3">REQUIRED DOCUMENTS</div>
      <div className="space-y-2">
        {documents.map((doc) => (
          <div key={doc.name} className="flex items-center gap-2 text-sm">
            <span className={doc.status === 'verified' ? 'text-green-500' : doc.status === 'missing' ? 'text-warning' : ''}>
              {statusIcon[doc.status]}
            </span>
            <span>{doc.name}</span>
            <span className="ml-auto text-xs opacity-60 uppercase">[{doc.status}]</span>
          </div>
        ))}
      </div>
    </div>
  )
}
```

### 3. Toast Notification System

```tsx
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
  add: (type: Toast['type'], message: string) => void
  remove: (id: string) => void
}

export const useToast = create<ToastStore>((set) => ({
  toasts: [],
  add: (type, message) => {
    const id = Math.random().toString(36).slice(2)
    set((s) => ({ toasts: [...s.toasts, { id, type, message }] }))
    setTimeout(() => {
      set((s) => ({ toasts: s.toasts.filter((t) => t.id !== id) }))
    }, 5000)
  },
  remove: (id) => set((s) => ({ toasts: s.toasts.filter((t) => t.id !== id) })),
}))

export function ToastContainer() {
  const { toasts, remove } = useToast()

  if (toasts.length === 0) return null

  return (
    <div className="fixed bottom-4 right-4 z-50 space-y-2">
      {toasts.map((toast) => (
        <div
          key={toast.id}
          onClick={() => remove(toast.id)}
          className={`px-4 py-3 font-pixel text-sm cursor-pointer animate-slide-in ${
            toast.type === 'error' ? 'bg-warning text-white' :
            toast.type === 'success' ? 'bg-dark text-text-inv' :
            toast.type === 'warning' ? 'bg-yellow-500 text-black' :
            'bg-canvas border border-dark'
          }`}
        >
          {toast.message}
        </div>
      ))}
    </div>
  )
}

// Usage:
// const { add } = useToast()
// add('success', 'Calculation complete!')
```

### 4. Loading Skeletons

```tsx
// src/components/ui/Skeleton.tsx
export function Skeleton({ className = '' }: { className?: string }) {
  return <div className={`animate-pulse bg-[#333] ${className}`} />
}

export function CardSkeleton() {
  return (
    <div className="border border-[#333] p-5 space-y-3">
      <Skeleton className="h-4 w-24" />
      <Skeleton className="h-8 w-full" />
      <Skeleton className="h-4 w-3/4" />
      <Skeleton className="h-4 w-1/2" />
    </div>
  )
}

export function TableSkeleton({ rows = 5 }: { rows?: number }) {
  return (
    <div className="space-y-2">
      {Array.from({ length: rows }).map((_, i) => (
        <div key={i} className="flex gap-4">
          <Skeleton className="h-6 w-1/4" />
          <Skeleton className="h-6 w-1/2" />
          <Skeleton className="h-6 w-1/4" />
        </div>
      ))}
    </div>
  )
}
```

### 5. Add ToastContainer to Layout

```tsx
// src/app/(dashboard)/layout.tsx
import { ToastContainer } from '@/components/ui/Toast'

export default function DashboardLayout({ children }: { children: React.ReactNode }) {
  return (
    <>
      {children}
      <ToastContainer />
    </>
  )
}
```

---

## Commit Schedule

```bash
# Hour 6:30
git commit -m "feat(fe3): landed cost page layout and form"

# Hour 7:00
git commit -m "feat(fe3): cost breakdown visualization"

# Hour 7:30
git commit -m "feat(fe3): compliance checker page"

# Hour 8:00
git commit -m "feat(fe3): risk indicators and document checklist"

# Hour 8:30
git commit -m "feat(fe3): toast notification system"

# Hour 9:00
git commit -m "feat(fe3): loading skeletons and polish"
git push -u origin feature/fe-phase-3-cost-compliance
```

---

## Testing Checklist

- [ ] Landed cost form calculates and displays breakdown
- [ ] Cost breakdown shows Section 301 warning in red
- [ ] Compliance page shows risk indicators
- [ ] Document checklist displays correctly
- [ ] Toast notifications appear and auto-dismiss
- [ ] Loading skeletons display during data fetch
