# Design Implementation Guide - TradeOptimize AI
## Pixel-Perfect Implementation from Design Files

---

## 1. Design System Tokens (CSS Variables)

Extract these EXACTLY into your Tailwind config or CSS:

```css
:root {
    /* Colors */
    --bg-canvas: #C8C8C8;      /* Main background */
    --bg-dark: #080808;         /* Dark panels, buttons */
    --text-main: #000000;       /* Primary text */
    --text-inv: #E8E8E8;        /* Text on dark backgrounds */
    --accent-border: #000000;   /* Border color */

    /* Typography */
    --font-sans: 'Space Grotesk', 'Helvetica Neue', sans-serif;
    --font-pixel: 'Silkscreen', monospace;

    /* Spacing */
    --radius: 0px;              /* NO border radius - brutalist */
    --spacing-unit: 8px;
}
```

### Tailwind Config Extension:

```typescript
// tailwind.config.ts
export default {
  theme: {
    extend: {
      colors: {
        canvas: '#C8C8C8',
        dark: '#080808',
        'text-main': '#000000',
        'text-inv': '#E8E8E8',
      },
      fontFamily: {
        sans: ['Space Grotesk', 'Helvetica Neue', 'sans-serif'],
        pixel: ['Silkscreen', 'monospace'],
      },
      borderRadius: {
        none: '0px', // Enforce brutalist no-radius
      },
    },
  },
}
```

---

## 2. Font Loading (CRITICAL)

```html
<!-- Add to _document.tsx or layout.tsx -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Silkscreen&family=Space+Grotesk:wght@400;600;700&display=swap" rel="stylesheet">
```

---

## 3. Master Layout Structure

All pages follow this 3-column grid:

```
┌──────────────┬─────────────────────────┬──────────────────┐
│   SIDEBAR    │       WORKSPACE         │  INTELLIGENCE    │
│   (260px)    │        (1fr)            │    PANEL         │
│              │                         │    (400px)       │
│  - Logo      │  - Header with title    │  - Live context  │
│  - Nav menu  │  - Main content area    │  - AI results    │
│  - User info │  - Input area           │  - References    │
└──────────────┴─────────────────────────┴──────────────────┘
```

### React Component:

```tsx
// src/components/layout/AppLayout.tsx
export function AppLayout({ children, rightPanel }: Props) {
  return (
    <div className="grid grid-cols-[260px_1fr_400px] h-screen border-t-2 border-dark">
      <Sidebar />
      <main className="flex flex-col overflow-hidden bg-[#D1D1D1]">
        {children}
      </main>
      <aside className="bg-dark text-text-inv p-6 flex flex-col gap-8 border-l-2 border-dark overflow-y-auto">
        {rightPanel}
      </aside>
    </div>
  )
}
```

---

## 4. Component Library

### 4.1 Sidebar Component

```tsx
// src/components/layout/Sidebar.tsx
export function Sidebar() {
  const navItems = [
    { id: 'classifier', label: 'HS CLASSIFIER', path: '/classify' },
    { id: 'assistant', label: 'AI ASSISTANT', path: '/assistant' },
    { id: 'landed-cost', label: 'LANDED COST', path: '/landed-cost' },
    { id: 'compliance', label: 'COMPLIANCE', path: '/compliance' },
    { id: 'route', label: 'ROUTE OPTIMIZER', path: '/route' },
    { id: 'cargo', label: 'CARGO LOCATOR', path: '/cargo' },
  ]

  return (
    <aside className="border-r-2 border-dark p-6 flex flex-col justify-between bg-canvas">
      {/* Logo */}
      <div>
        <div className="mb-12">
          <div className="w-12 h-12 border-2 border-dark rounded-full flex items-center justify-center mb-4">
            <LogoIcon />
          </div>
          <div className="label">SYSTEM</div>
          <div className="font-bold text-xl">TRADE<br/>OPTIMIZER</div>
        </div>

        {/* Navigation */}
        <nav className="flex flex-col">
          <div className="label">MODULES</div>
          {navItems.map(item => (
            <NavItem key={item.id} {...item} />
          ))}
        </nav>
      </div>

      {/* User Info */}
      <div>
        <div className="label">USER</div>
        <div className="text-sm">JOHN DOE<br/>GLOBAL LOGISTICS</div>
        <div className="mt-6 text-xs opacity-60">V.2.4.0</div>
      </div>
    </aside>
  )
}
```

### 4.2 Nav Item Component

```tsx
// src/components/layout/NavItem.tsx
export function NavItem({ label, path, active }: NavItemProps) {
  return (
    <Link
      href={path}
      className={cn(
        "py-3 border-b border-dark cursor-pointer flex justify-between items-start",
        "transition-all hover:pl-2 hover:bg-black/5",
        active && "font-pixel text-lg"
      )}
    >
      <span>{label}</span>
      <span className="text-xs mt-0.5">{active ? '←' : '↓'}</span>
    </Link>
  )
}
```

### 4.3 Workspace Header

```tsx
// src/components/layout/WorkspaceHeader.tsx
export function WorkspaceHeader({ module, title, meta }: Props) {
  return (
    <header className="p-6 border-b-2 border-dark bg-canvas flex justify-between items-end">
      <div>
        <div className="label">ACTIVE MODULE</div>
        <h1 className="text-5xl font-bold leading-none">
          {title.split(' ')[0]}<br/>
          <span className="font-pixel">{title.split(' ').slice(1).join(' ')}</span>
        </h1>
      </div>
      <div className="text-right">
        <div className="label">{meta.label}</div>
        <div className="font-pixel text-sm">{meta.value}</div>
      </div>
    </header>
  )
}
```

### 4.4 Result Card (Intelligence Panel)

```tsx
// src/components/ui/ResultCard.tsx
export function ResultCard({ label, children }: Props) {
  return (
    <div className="border border-[#333] p-5">
      <div className="label text-[#888]">{label}</div>
      {children}
    </div>
  )
}
```

### 4.5 Tag Component

```tsx
// src/components/ui/Tag.tsx
export function Tag({ children, variant = 'light' }: Props) {
  return (
    <span className={cn(
      "border px-2 py-1 text-xs uppercase inline-block mr-1",
      variant === 'light' && "border-dark",
      variant === 'dark' && "border-[#555] text-[#AAA]"
    )}>
      {children}
    </span>
  )
}
```

### 4.6 HS Code Display (Pixel Style)

```tsx
// src/components/ui/HSCodeDisplay.tsx
export function HSCodeDisplay({ code, size = 'large' }: Props) {
  return (
    <div className={cn(
      "font-pixel",
      size === 'large' && "text-6xl tracking-wide",
      size === 'medium' && "text-3xl",
      size === 'small' && "text-xl"
    )}>
      {code}
    </div>
  )
}
```

### 4.7 Confidence Bar

```tsx
// src/components/ui/ConfidenceBar.tsx
export function ConfidenceBar({ value, label }: Props) {
  return (
    <div className="mb-2">
      <div className="flex justify-between text-xs mb-1">
        <span>{label}</span>
        <span>{value}%</span>
      </div>
      <div className="h-1 bg-[#333]">
        <div
          className="h-full bg-text-inv transition-all"
          style={{ width: `${value}%` }}
        />
      </div>
    </div>
  )
}
```

### 4.8 Chat Message Component

```tsx
// src/components/chat/Message.tsx
export function Message({ type, content, suggestions }: MessageProps) {
  return (
    <div className={cn(
      "max-w-[80%] p-4 text-sm leading-relaxed animate-slideIn",
      type === 'ai' && "self-start bg-dark text-text-inv border border-[#333]",
      type === 'user' && "self-end bg-transparent border border-dark"
    )}>
      {type === 'ai' && <div className="label text-[#888]">SYSTEM_CORE</div>}
      {content}
      {suggestions && (
        <div className="flex flex-wrap gap-2 mt-2">
          {suggestions.map(s => (
            <SuggestionChip key={s} label={s} />
          ))}
        </div>
      )}
    </div>
  )
}
```

### 4.9 Typing Indicator

```tsx
// src/components/chat/TypingIndicator.tsx
export function TypingIndicator() {
  return (
    <div className="flex gap-1 p-3 bg-dark w-fit">
      {[0, 1, 2].map(i => (
        <div
          key={i}
          className="w-1.5 h-1.5 bg-text-inv animate-pulse"
          style={{ animationDelay: `${i * 0.2}s` }}
        />
      ))}
    </div>
  )
}
```

### 4.10 Primary Button

```tsx
// src/components/ui/Button.tsx
export function Button({ children, variant = 'primary', ...props }: Props) {
  return (
    <button
      className={cn(
        "font-pixel px-6 py-4 transition-all",
        variant === 'primary' && "bg-dark text-canvas hover:opacity-90",
        variant === 'secondary' && "bg-transparent border-2 border-dark hover:bg-dark hover:text-canvas"
      )}
      {...props}
    >
      {children}
    </button>
  )
}
```

---

## 5. Page-Specific Implementations

### 5.1 HS Classifier Page (cp.md)

Key elements:
- Large product description textarea (textarea with border-dark)
- Image upload section with drag-drop
- Technical characteristics form
- AI analysis panel on right

```tsx
// src/app/classify/page.tsx
export default function ClassifyPage() {
  return (
    <AppLayout rightPanel={<ClassifierIntelligencePanel />}>
      <WorkspaceHeader
        title="CLASSIFY PRODUCT"
        meta={{ label: "ANALYSIS ID", value: "HS_8824_XQ" }}
      />
      <div className="flex-1 overflow-y-auto p-6">
        <div className="grid grid-cols-2 gap-6">
          <ProductDescriptionForm />
          <ImageUploadSection />
        </div>
        <TechnicalCharacteristicsForm />
      </div>
      <ActionBar>
        <Button>CLASSIFY_NOW ›</Button>
      </ActionBar>
    </AppLayout>
  )
}
```

### 5.2 Landed Cost Calculator (lc.md)

Key elements:
- Origin/Destination selectors
- Duty calculation breakdown
- Fee breakdown in right panel

### 5.3 Route Optimizer (ro.md)

Key elements:
- Map visualization (use react-simple-maps or mapbox)
- Route comparison cards
- Real-time port data

### 5.4 Trade Compliance (tc.md)

Key elements:
- Risk indicator bars
- Document requirements checklist
- Alert/warning styling (red: #FF4141)

### 5.5 AI Assistant (ai_assistant.md)

Key elements:
- Chat message list with scroll
- Input area at bottom
- Live context panel showing AI thinking

### 5.6 Cargo Locator (cargo.locator.md)

Key elements:
- Shipment tracking timeline
- Map with vessel positions
- Container details cards

---

## 6. Animations (CSS)

```css
/* Add to globals.css */

@keyframes slideIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

@keyframes pulse {
  0%, 100% { opacity: 0.3; transform: scale(0.8); }
  50% { opacity: 1; transform: scale(1.1); }
}

@keyframes blink {
  0%, 100% { opacity: 1; }
  50% { opacity: 0; }
}

.animate-slideIn {
  animation: slideIn 0.3s ease-out;
}

.animate-blink {
  animation: blink 1s infinite;
}
```

---

## 7. Scrollbar Styling

```css
/* Custom scrollbars */
::-webkit-scrollbar { width: 6px; }
::-webkit-scrollbar-thumb { background: var(--bg-dark); }

/* Dark panel scrollbar */
.intelligence-panel::-webkit-scrollbar-thumb { background: #333; }
```

---

## 8. Label Component (Used Everywhere)

```tsx
// src/components/ui/Label.tsx
export function Label({ children, icon }: Props) {
  return (
    <div className="text-[0.7rem] uppercase tracking-wider mb-1 opacity-80 flex items-center gap-1">
      {icon}
      {children}
    </div>
  )
}
```

---

## 9. Icons (SVG)

Use simple SVG icons throughout. The design uses minimal icons:

```tsx
// src/components/icons/index.tsx
export const LogoIcon = () => (
  <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
    <line x1="2" y1="22" x2="22" y2="2" />
    <polyline points="12 2 22 2 22 12" />
    <circle cx="12" cy="12" r="10" strokeWidth="1.5" />
  </svg>
)

export const ArrowIcon = () => (
  <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2">
    <line x1="5" y1="12" x2="19" y2="12" />
    <polyline points="12 5 19 12 12 19" />
  </svg>
)
```

---

## 10. Demo Checklist

Before presenting:

- [ ] All fonts loading correctly (Space Grotesk + Silkscreen)
- [ ] No border-radius anywhere (brutalist design)
- [ ] Dark panels have #080808 background
- [ ] Light canvas is #C8C8C8
- [ ] Pixel font used for: HS codes, nav items (active), buttons, status text
- [ ] All labels are UPPERCASE with letter-spacing
- [ ] Smooth animations on chat messages
- [ ] Typing indicator working
- [ ] Right panel shows "THINKING" with blinking spinner during AI calls
- [ ] Scrollbars styled correctly

---

## 11. Quick Win: Loading States

```tsx
// src/components/ui/AIThinkingStatus.tsx
export function AIThinkingStatus() {
  return (
    <div className="flex items-center gap-2 font-pixel text-sm">
      <div className="w-3 h-3 bg-text-inv animate-blink" />
      THINKING
    </div>
  )
}
```

---

## 12. Responsive Considerations

For demo on different screens, add breakpoints:

```tsx
// At tablet/small laptop, stack the layout
<div className="grid grid-cols-[260px_1fr_400px] lg:grid-cols-[200px_1fr_300px] md:grid-cols-1">
```

But for hackathon demo, assume a large screen presentation.
