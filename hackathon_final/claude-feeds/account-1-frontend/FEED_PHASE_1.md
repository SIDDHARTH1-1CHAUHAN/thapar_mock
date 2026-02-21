# Frontend Phase 1: Foundation (Hours 0-3)
## Branch: `feature/fe-phase-1-foundation`

---

## AI/ML IN THIS PHASE

```
┌─────────────────────────────────────────────────────────┐
│  NO AI IN THIS PHASE - SCAFFOLDING ONLY                │
│  ───────────────────────────────────────               │
│                                                         │
│  This phase sets up:                                    │
│  • Next.js 14 project structure                        │
│  • Design system (colors, fonts, CSS)                  │
│  • Layout components (Sidebar, Header)                 │
│  • Theme toggle (light/dark mode)                      │
│  • Placeholder pages for all features                  │
│                                                         │
│  AI integration starts in Phase 2!                     │
│  Just get the UI foundation running.                   │
└─────────────────────────────────────────────────────────┘
```

---

## Your Mission

Set up the Next.js project with the complete design system and core layout components.

---

## Deliverables

### 1. Project Setup
```bash
npx create-next-app@14 frontend --typescript --tailwind --eslint --app --src-dir
cd frontend
npm install zustand @tanstack/react-query axios lucide-react recharts
npm install jspdf jspdf-autotable xlsx file-saver html2canvas
npm install leaflet react-leaflet @types/leaflet
```

> **Note**: Leaflet is for real-world maps in Cargo Tracking and Route Optimizer pages (Phase 4)

### 2. File Structure to Create

```
frontend/src/
├── app/
│   ├── layout.tsx              # Root layout with fonts + theme
│   ├── page.tsx                # Redirect to /classify
│   ├── globals.css             # Design tokens from shared doc
│   └── (dashboard)/
│       ├── layout.tsx          # Dashboard layout wrapper
│       ├── classify/page.tsx   # Placeholder
│       ├── assistant/page.tsx  # Placeholder
│       ├── landed-cost/page.tsx
│       ├── compliance/page.tsx
│       ├── route/page.tsx
│       ├── cargo/page.tsx
│       └── analytics/page.tsx
├── components/
│   ├── layout/
│   │   ├── AppLayout.tsx       # 3-column grid
│   │   ├── Sidebar.tsx         # Navigation
│   │   ├── WorkspaceHeader.tsx # Page headers
│   │   └── ThemeToggle.tsx     # Light/dark switch
│   └── ui/
│       ├── Button.tsx
│       ├── Input.tsx
│       ├── ResultCard.tsx
│       ├── Tag.tsx
│       └── HSCodeDisplay.tsx
├── store/
│   └── useThemeStore.ts        # Theme state with persistence
├── lib/
│   └── utils.ts                # Helper functions
└── types/
    └── index.ts                # TypeScript types
```

### 3. Theme System (IMPORTANT!)

```typescript
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
      theme: 'dark',
      toggleTheme: () => set((state) => ({
        theme: state.theme === 'dark' ? 'light' : 'dark'
      })),
    }),
    { name: 'tradeopt-theme' }
  )
)
```

```tsx
// src/app/layout.tsx
'use client'
import { useThemeStore } from '@/store/useThemeStore'
import { useEffect } from 'react'
import './globals.css'

export default function RootLayout({ children }: { children: React.ReactNode }) {
  const { theme } = useThemeStore()

  useEffect(() => {
    document.documentElement.setAttribute('data-theme', theme)
  }, [theme])

  return (
    <html lang="en" data-theme={theme}>
      <head>
        <link rel="preconnect" href="https://fonts.googleapis.com" />
        <link rel="preconnect" href="https://fonts.gstatic.com" crossOrigin="" />
        <link href="https://fonts.googleapis.com/css2?family=Silkscreen&family=Space+Grotesk:wght@400;600;700&display=swap" rel="stylesheet" />
      </head>
      <body>{children}</body>
    </html>
  )
}
```

### 4. Core Components

Copy ALL components from `shared/02_DESIGN_SYSTEM.md`:
- AppLayout
- Sidebar
- WorkspaceHeader
- Button
- ResultCard
- ThemeToggle

### 5. Placeholder Pages

Each page should use AppLayout and show a "Coming Soon" state:

```tsx
// src/app/(dashboard)/classify/page.tsx
import { AppLayout } from '@/components/layout/AppLayout'
import { WorkspaceHeader } from '@/components/layout/WorkspaceHeader'

export default function ClassifyPage() {
  return (
    <AppLayout>
      <WorkspaceHeader
        title="CLASSIFY"
        pixelTitle="PRODUCT"
        metaLabel="STATUS"
        metaValue="PHASE_2"
      />
      <div className="flex-1 flex items-center justify-center">
        <div className="text-center">
          <div className="font-pixel text-2xl mb-2">COMING_SOON</div>
          <p className="text-sm opacity-60">This feature will be implemented in Phase 2</p>
        </div>
      </div>
    </AppLayout>
  )
}
```

---

## Commit Schedule

```bash
# Hour 0:30
git add .
git commit -m "feat(fe1): project scaffolding with Next.js 14"

# Hour 1:00
git commit -m "feat(fe1): globals.css with design tokens and fonts"

# Hour 1:30
git commit -m "feat(fe1): AppLayout and Sidebar components"

# Hour 2:00
git commit -m "feat(fe1): WorkspaceHeader and UI components"

# Hour 2:30
git commit -m "feat(fe1): theme system with light/dark mode"

# Hour 3:00
git commit -m "feat(fe1): placeholder pages and routing"
git push -u origin feature/fe-phase-1-foundation
```

---

## Testing Checklist

Before creating PR:
- [ ] `npm run dev` works without errors
- [ ] All 7 routes accessible (/classify, /assistant, /landed-cost, /compliance, /route, /cargo, /analytics)
- [ ] Sidebar navigation works
- [ ] Theme toggle switches between light/dark
- [ ] Theme persists on page refresh
- [ ] Fonts loading correctly (Space Grotesk + Silkscreen)

---

## PR Template

```markdown
## Phase FE-1: Frontend Foundation

### Changes
- Next.js 14 + TypeScript + Tailwind project setup
- Design system implementation (fonts, colors, layout)
- Core components (AppLayout, Sidebar, WorkspaceHeader)
- Theme system with light/dark mode toggle
- Routing for all 7 dashboard pages

### Screenshots
[Add light mode and dark mode screenshots]

### Testing
- [x] Dev server runs
- [x] Navigation works
- [x] Theme toggle works

### Dependencies
None (first phase)
```

---

## Notes

- Use the EXACT color values from the design system
- NO border-radius anywhere - brutalist design
- Silkscreen font for: buttons, HS codes, nav items (active), labels
- Space Grotesk for: body text, descriptions

**After completing this phase, wait for merge before starting Phase 2!**
