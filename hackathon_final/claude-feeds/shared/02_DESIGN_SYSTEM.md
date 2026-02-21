# Design System - TradeOptimize AI
## Complete Component Reference

---

## CSS Variables (globals.css)

```css
@import url('https://fonts.googleapis.com/css2?family=Silkscreen&family=Space+Grotesk:wght@400;600;700&display=swap');

:root {
  --bg-canvas: #C8C8C8;
  --bg-dark: #080808;
  --bg-panel: #D1D1D1;
  --text-main: #000000;
  --text-inv: #E8E8E8;
  --text-muted: #888888;
  --border-dark: #333333;
  --warning: #FF4141;
}

/* Dark theme (default) */
[data-theme="dark"] {
  --bg-canvas: #C8C8C8;
  --bg-dark: #080808;
}

/* Light theme */
[data-theme="light"] {
  --bg-canvas: #FFFFFF;
  --bg-dark: #F0F0F0;
  --text-main: #1A1A1A;
  --text-inv: #333333;
}

* {
  box-sizing: border-box;
  -webkit-font-smoothing: antialiased;
}

body {
  margin: 0;
  font-family: 'Space Grotesk', sans-serif;
  background-color: var(--bg-canvas);
  color: var(--text-main);
}

.font-pixel {
  font-family: 'Silkscreen', monospace;
  letter-spacing: -0.05em;
}

.label {
  font-size: 0.7rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  opacity: 0.8;
}

/* Scrollbars */
::-webkit-scrollbar { width: 6px; }
::-webkit-scrollbar-thumb { background: var(--bg-dark); }

/* Animations */
@keyframes slideIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

@keyframes blink {
  0%, 100% { opacity: 1; }
  50% { opacity: 0; }
}

.animate-slide-in { animation: slideIn 0.3s ease-out; }
.animate-blink { animation: blink 1s infinite; }
```

---

## Tailwind Config (tailwind.config.ts)

```typescript
import type { Config } from 'tailwindcss'

const config: Config = {
  content: ['./src/**/*.{js,ts,jsx,tsx,mdx}'],
  theme: {
    extend: {
      colors: {
        canvas: '#C8C8C8',
        dark: '#080808',
        panel: '#D1D1D1',
        'text-main': '#000000',
        'text-inv': '#E8E8E8',
        'text-muted': '#888888',
        warning: '#FF4141',
      },
      fontFamily: {
        sans: ['Space Grotesk', 'sans-serif'],
        pixel: ['Silkscreen', 'monospace'],
      },
      borderRadius: {
        none: '0px',
      },
    },
  },
  plugins: [],
}

export default config
```

---

## Core Components

### 1. AppLayout

```tsx
// src/components/layout/AppLayout.tsx
'use client'
import { ReactNode } from 'react'
import { Sidebar } from './Sidebar'

interface Props {
  children: ReactNode
  rightPanel?: ReactNode
}

export function AppLayout({ children, rightPanel }: Props) {
  return (
    <div className="h-screen grid grid-cols-[260px_1fr_400px] border-t-2 border-dark">
      <Sidebar />
      <main className="flex flex-col overflow-hidden bg-panel">
        {children}
      </main>
      {rightPanel && (
        <aside className="bg-dark text-text-inv p-6 flex flex-col gap-8 overflow-y-auto">
          {rightPanel}
        </aside>
      )}
    </div>
  )
}
```

### 2. Sidebar

```tsx
// src/components/layout/Sidebar.tsx
'use client'
import Link from 'next/link'
import { usePathname } from 'next/navigation'

const navItems = [
  { label: 'HS CLASSIFIER', path: '/classify' },
  { label: 'AI ASSISTANT', path: '/assistant' },
  { label: 'LANDED COST', path: '/landed-cost' },
  { label: 'COMPLIANCE', path: '/compliance' },
  { label: 'ROUTE OPTIMIZER', path: '/route' },
  { label: 'CARGO LOCATOR', path: '/cargo' },
]

export function Sidebar() {
  const pathname = usePathname()

  return (
    <aside className="border-r-2 border-dark p-6 flex flex-col justify-between bg-canvas">
      <div>
        {/* Logo */}
        <div className="mb-12">
          <div className="w-12 h-12 border-2 border-dark rounded-full flex items-center justify-center mb-4">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" className="w-6 h-6">
              <line x1="2" y1="22" x2="22" y2="2" />
              <polyline points="12 2 22 2 22 12" />
              <circle cx="12" cy="12" r="10" strokeWidth="1.5" />
            </svg>
          </div>
          <div className="label">SYSTEM</div>
          <div className="font-bold text-xl">TRADE<br/>OPTIMIZER</div>
        </div>

        {/* Nav */}
        <nav>
          <div className="label mb-2">MODULES</div>
          {navItems.map(item => (
            <Link
              key={item.path}
              href={item.path}
              className={`block py-3 border-b border-dark hover:pl-2 transition-all ${
                pathname === item.path ? 'font-pixel text-lg' : ''
              }`}
            >
              <span>{item.label}</span>
              <span className="float-right text-xs">{pathname === item.path ? '←' : '↓'}</span>
            </Link>
          ))}
        </nav>
      </div>

      <div>
        <div className="label">USER</div>
        <div className="text-sm">DEMO USER<br/>GLOBAL LOGISTICS</div>
        <div className="mt-6 text-xs opacity-60">V.2.4.0</div>
      </div>
    </aside>
  )
}
```

### 3. WorkspaceHeader

```tsx
// src/components/layout/WorkspaceHeader.tsx
interface Props {
  title: string
  pixelTitle: string
  metaLabel: string
  metaValue: string
}

export function WorkspaceHeader({ title, pixelTitle, metaLabel, metaValue }: Props) {
  return (
    <header className="p-6 border-b-2 border-dark bg-canvas flex justify-between items-end shrink-0">
      <div>
        <div className="label">ACTIVE MODULE</div>
        <h1 className="text-5xl font-bold leading-none">
          {title}<br/>
          <span className="font-pixel">{pixelTitle}</span>
        </h1>
      </div>
      <div className="text-right">
        <div className="label">{metaLabel}</div>
        <div className="font-pixel text-sm">{metaValue}</div>
      </div>
    </header>
  )
}
```

### 4. Input

```tsx
// src/components/ui/Input.tsx
interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label?: string
}

export function Input({ label, className = '', ...props }: InputProps) {
  return (
    <div>
      {label && <div className="label mb-1">{label}</div>}
      <input
        className={`w-full bg-transparent border border-dark p-3 focus:outline-none focus:border-2 ${className}`}
        {...props}
      />
    </div>
  )
}
```

### 5. Textarea

```tsx
// src/components/ui/Textarea.tsx
interface TextareaProps extends React.TextareaHTMLAttributes<HTMLTextAreaElement> {
  label?: string
}

export function Textarea({ label, className = '', ...props }: TextareaProps) {
  return (
    <div>
      {label && <div className="label mb-1">{label}</div>}
      <textarea
        className={`w-full bg-transparent border border-dark p-3 focus:outline-none focus:border-2 resize-none ${className}`}
        {...props}
      />
    </div>
  )
}
```

### 6. Button

```tsx
// src/components/ui/Button.tsx
interface Props extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary'
}

export function Button({ children, variant = 'primary', className = '', ...props }: Props) {
  const styles = {
    primary: 'bg-dark text-canvas hover:opacity-90',
    secondary: 'bg-transparent border-2 border-dark hover:bg-dark hover:text-canvas',
  }

  return (
    <button
      className={`font-pixel px-6 py-4 transition-all ${styles[variant]} ${className}`}
      {...props}
    >
      {children}
    </button>
  )
}
```

### 5. ResultCard

```tsx
// src/components/ui/ResultCard.tsx
import { ReactNode } from 'react'

interface Props {
  label: string
  children: ReactNode
}

export function ResultCard({ label, children }: Props) {
  return (
    <div className="border border-[#333] p-5">
      <div className="label text-[#888]">{label}</div>
      {children}
    </div>
  )
}
```

### 6. HSCodeDisplay

```tsx
// src/components/ui/HSCodeDisplay.tsx
interface Props {
  code: string
  size?: 'sm' | 'md' | 'lg'
}

export function HSCodeDisplay({ code, size = 'lg' }: Props) {
  const sizes = { sm: 'text-xl', md: 'text-3xl', lg: 'text-6xl' }
  return <div className={`font-pixel ${sizes[size]}`}>{code}</div>
}
```

### 7. Tag

```tsx
// src/components/ui/Tag.tsx
interface Props {
  children: React.ReactNode
  variant?: 'light' | 'dark'
}

export function Tag({ children, variant = 'light' }: Props) {
  return (
    <span className={`border px-2 py-1 text-xs uppercase mr-1 ${
      variant === 'dark' ? 'border-[#555] text-[#AAA]' : 'border-dark'
    }`}>
      {children}
    </span>
  )
}
```

### 8. ConfidenceBar

```tsx
// src/components/ui/ConfidenceBar.tsx
interface Props {
  label: string
  value: number
}

export function ConfidenceBar({ label, value }: Props) {
  return (
    <div className="mb-2">
      <div className="flex justify-between text-xs mb-1">
        <span>{label}</span>
        <span>{value}%</span>
      </div>
      <div className="h-1 bg-[#333]">
        <div className="h-full bg-text-inv" style={{ width: `${value}%` }} />
      </div>
    </div>
  )
}
```

### 9. ChatMessage

```tsx
// src/components/chat/Message.tsx
interface Props {
  type: 'ai' | 'user'
  content: string
  suggestions?: string[]
}

export function Message({ type, content, suggestions }: Props) {
  return (
    <div className={`max-w-[80%] p-4 text-sm animate-slide-in ${
      type === 'ai'
        ? 'self-start bg-dark text-text-inv border border-[#333]'
        : 'self-end border border-dark'
    }`}>
      {type === 'ai' && <div className="label text-[#888] mb-2">SYSTEM_CORE</div>}
      {content}
      {suggestions && (
        <div className="flex flex-wrap gap-2 mt-3">
          {suggestions.map(s => (
            <button key={s} className="border border-[#444] px-3 py-1 text-xs font-pixel text-[#AAA] hover:text-text-inv">
              {s}
            </button>
          ))}
        </div>
      )}
    </div>
  )
}
```

### 10. ThemeToggle

```tsx
// src/components/ui/ThemeToggle.tsx
'use client'
import { useThemeStore } from '@/store/useThemeStore'

export function ThemeToggle() {
  const { theme, toggleTheme } = useThemeStore()

  return (
    <button
      onClick={toggleTheme}
      className="border border-dark px-3 py-2 text-xs font-pixel hover:bg-dark hover:text-canvas transition-all"
    >
      {theme === 'dark' ? 'LIGHT_MODE' : 'DARK_MODE'}
    </button>
  )
}
```

### 11. Toast System

```tsx
// src/components/ui/Toast.tsx
'use client'
import { create } from 'zustand'
import { useEffect } from 'react'

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

export const useToastStore = create<ToastStore>((set) => ({
  toasts: [],
  add: (type, message) => {
    const id = crypto.randomUUID()
    set((s) => ({ toasts: [...s.toasts, { id, type, message }] }))
    setTimeout(() => set((s) => ({ toasts: s.toasts.filter((t) => t.id !== id) })), 5000)
  },
  remove: (id) => set((s) => ({ toasts: s.toasts.filter((t) => t.id !== id) })),
}))

export function ToastContainer() {
  const { toasts, remove } = useToastStore()

  return (
    <div className="fixed bottom-4 right-4 space-y-2 z-50">
      {toasts.map((toast) => (
        <div
          key={toast.id}
          className={`px-4 py-3 font-pixel text-sm animate-slide-in cursor-pointer ${
            toast.type === 'error' ? 'bg-warning text-white' :
            toast.type === 'success' ? 'bg-dark text-text-inv' :
            'bg-canvas border border-dark'
          }`}
          onClick={() => remove(toast.id)}
        >
          {toast.message}
        </div>
      ))}
    </div>
  )
}

// Usage: useToastStore.getState().add('success', 'Classification complete!')
```

---

## Page Template

```tsx
// src/app/(dashboard)/example/page.tsx
import { AppLayout } from '@/components/layout/AppLayout'
import { WorkspaceHeader } from '@/components/layout/WorkspaceHeader'
import { ResultCard } from '@/components/ui/ResultCard'
import { Button } from '@/components/ui/Button'

export default function ExamplePage() {
  return (
    <AppLayout
      rightPanel={
        <>
          <div className="flex justify-between items-center pb-6 border-b border-[#333]">
            <div className="label text-text-inv">LIVE_CONTEXT</div>
          </div>
          <ResultCard label="RESULT">
            <div className="font-pixel text-2xl mt-2">DATA_HERE</div>
          </ResultCard>
        </>
      }
    >
      <WorkspaceHeader
        title="EXAMPLE"
        pixelTitle="PAGE"
        metaLabel="SESSION ID"
        metaValue="EX_8824_XQ"
      />

      <div className="flex-1 overflow-y-auto p-6">
        {/* Main content here */}
      </div>

      <div className="p-6 bg-canvas border-t-2 border-dark flex justify-end">
        <Button>ACTION_&gt;</Button>
      </div>
    </AppLayout>
  )
}
```

---

## Copy these components into your project to maintain design consistency!
