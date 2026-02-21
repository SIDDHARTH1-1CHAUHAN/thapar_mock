# Quick Copy-Paste Components
## Ready-to-Use React Components from Design Files

These components are extracted from the design files and ready to copy into your Next.js project.

---

## 1. Global CSS (globals.css)

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@import url('https://fonts.googleapis.com/css2?family=Silkscreen&family=Space+Grotesk:wght@400;600;700&display=swap');

:root {
  --bg-canvas: #C8C8C8;
  --bg-dark: #080808;
  --text-main: #000000;
  --text-inv: #E8E8E8;
  --spacing-unit: 8px;
}

* {
  box-sizing: border-box;
  -webkit-font-smoothing: antialiased;
}

body {
  margin: 0;
  padding: 0;
  background-color: var(--bg-canvas);
  color: var(--text-main);
  font-family: 'Space Grotesk', sans-serif;
  overflow: hidden;
}

h1, h2, h3, h4, .label {
  text-transform: uppercase;
  letter-spacing: 0.02em;
  margin: 0;
  font-weight: 600;
}

.label {
  font-size: 0.7rem;
  letter-spacing: 0.05em;
  margin-bottom: 4px;
  opacity: 0.8;
  display: flex;
  align-items: center;
  gap: 4px;
}

.pixel-text {
  font-family: 'Silkscreen', monospace;
  letter-spacing: -0.05em;
}

/* Scrollbars */
::-webkit-scrollbar { width: 6px; }
::-webkit-scrollbar-thumb { background: var(--bg-dark); }
.dark-panel::-webkit-scrollbar-thumb { background: #333; }

/* Animations */
@keyframes slideIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

@keyframes blink {
  0%, 100% { opacity: 1; }
  50% { opacity: 0; }
}

@keyframes pulse {
  0%, 100% { opacity: 0.3; transform: scale(0.8); }
  50% { opacity: 1; transform: scale(1.1); }
}

.animate-slide-in { animation: slideIn 0.3s ease-out; }
.animate-blink { animation: blink 1s infinite; }
```

---

## 2. Tailwind Config (tailwind.config.ts)

```typescript
import type { Config } from 'tailwindcss'

const config: Config = {
  content: [
    './src/pages/**/*.{js,ts,jsx,tsx,mdx}',
    './src/components/**/*.{js,ts,jsx,tsx,mdx}',
    './src/app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {
      colors: {
        canvas: '#C8C8C8',
        dark: '#080808',
        'dark-panel': '#D1D1D1',
        'text-main': '#000000',
        'text-inv': '#E8E8E8',
        'warning': '#FF4141',
        'border-light': '#333',
      },
      fontFamily: {
        sans: ['Space Grotesk', 'Helvetica Neue', 'sans-serif'],
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

## 3. Layout Component (src/components/layout/AppLayout.tsx)

```tsx
'use client'

import { ReactNode } from 'react'
import { Sidebar } from './Sidebar'

interface AppLayoutProps {
  children: ReactNode
  rightPanel?: ReactNode
}

export function AppLayout({ children, rightPanel }: AppLayoutProps) {
  return (
    <div className="h-screen flex flex-col">
      <div className="grid grid-cols-[260px_1fr_400px] flex-1 border-t-2 border-dark">
        <Sidebar />
        <main className="flex flex-col overflow-hidden bg-[#D1D1D1]">
          {children}
        </main>
        {rightPanel && (
          <aside className="bg-dark text-text-inv p-6 flex flex-col gap-8 border-l-2 border-dark overflow-y-auto dark-panel">
            {rightPanel}
          </aside>
        )}
      </div>
    </div>
  )
}
```

---

## 4. Sidebar (src/components/layout/Sidebar.tsx)

```tsx
'use client'

import Link from 'next/link'
import { usePathname } from 'next/navigation'

const navItems = [
  { id: 'classifier', label: 'HS CLASSIFIER', path: '/classify' },
  { id: 'assistant', label: 'AI ASSISTANT', path: '/assistant' },
  { id: 'landed-cost', label: 'LANDED COST', path: '/landed-cost' },
  { id: 'compliance', label: 'COMPLIANCE', path: '/compliance' },
  { id: 'route', label: 'ROUTE OPTIMIZER', path: '/route' },
  { id: 'cargo', label: 'CARGO LOCATOR', path: '/cargo' },
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

        {/* Navigation */}
        <nav className="flex flex-col">
          <div className="label">MODULES</div>
          {navItems.map(item => {
            const isActive = pathname === item.path
            return (
              <Link
                key={item.id}
                href={item.path}
                className={`py-3 border-b border-dark cursor-pointer flex justify-between items-start transition-all hover:pl-2 hover:bg-black/5 ${
                  isActive ? 'font-pixel text-lg' : ''
                }`}
              >
                <span>{item.label}</span>
                <span className="text-xs mt-0.5">{isActive ? '←' : '↓'}</span>
              </Link>
            )
          })}
        </nav>
      </div>

      {/* User Info */}
      <div>
        <div className="label">USER</div>
        <div className="text-sm">DEMO USER<br/>GLOBAL LOGISTICS</div>
        <div className="mt-6 text-xs opacity-60">V.2.4.0</div>
      </div>
    </aside>
  )
}
```

---

## 5. Workspace Header (src/components/layout/WorkspaceHeader.tsx)

```tsx
interface WorkspaceHeaderProps {
  title: string
  pixelTitle: string
  metaLabel: string
  metaValue: string
}

export function WorkspaceHeader({ title, pixelTitle, metaLabel, metaValue }: WorkspaceHeaderProps) {
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

---

## 6. Result Card (src/components/ui/ResultCard.tsx)

```tsx
import { ReactNode } from 'react'

interface ResultCardProps {
  label: string
  children: ReactNode
}

export function ResultCard({ label, children }: ResultCardProps) {
  return (
    <div className="border border-[#333] p-5">
      <div className="label text-[#888]">{label}</div>
      {children}
    </div>
  )
}
```

---

## 7. HS Code Display (src/components/ui/HSCodeDisplay.tsx)

```tsx
interface HSCodeDisplayProps {
  code: string
  size?: 'small' | 'medium' | 'large'
}

export function HSCodeDisplay({ code, size = 'large' }: HSCodeDisplayProps) {
  const sizeClasses = {
    small: 'text-xl',
    medium: 'text-3xl',
    large: 'text-6xl tracking-wide',
  }

  return (
    <div className={`font-pixel ${sizeClasses[size]}`}>
      {code}
    </div>
  )
}
```

---

## 8. Tag Component (src/components/ui/Tag.tsx)

```tsx
interface TagProps {
  children: React.ReactNode
  variant?: 'light' | 'dark'
}

export function Tag({ children, variant = 'light' }: TagProps) {
  return (
    <span className={`border px-2 py-1 text-xs uppercase inline-block mr-1 ${
      variant === 'light' ? 'border-dark' : 'border-[#555] text-[#AAA]'
    }`}>
      {children}
    </span>
  )
}
```

---

## 9. Confidence Bar (src/components/ui/ConfidenceBar.tsx)

```tsx
interface ConfidenceBarProps {
  label: string
  value: number
}

export function ConfidenceBar({ label, value }: ConfidenceBarProps) {
  return (
    <div className="mb-2">
      <div className="flex justify-between text-xs mb-1">
        <span>{label}</span>
        <span>{value}%</span>
      </div>
      <div className="h-1 bg-[#333]">
        <div
          className="h-full bg-text-inv transition-all duration-300"
          style={{ width: `${value}%` }}
        />
      </div>
    </div>
  )
}
```

---

## 10. AI Thinking Status (src/components/ui/AIThinkingStatus.tsx)

```tsx
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

## 11. Chat Message (src/components/chat/Message.tsx)

```tsx
interface MessageProps {
  type: 'ai' | 'user'
  content: string
  suggestions?: string[]
  onSuggestionClick?: (suggestion: string) => void
}

export function Message({ type, content, suggestions, onSuggestionClick }: MessageProps) {
  return (
    <div className={`max-w-[80%] p-4 text-sm leading-relaxed animate-slide-in ${
      type === 'ai'
        ? 'self-start bg-dark text-text-inv border border-[#333]'
        : 'self-end bg-transparent border border-dark text-text-main'
    }`}>
      {type === 'ai' && <div className="label text-[#888]">SYSTEM_CORE</div>}
      <div>{content}</div>
      {suggestions && suggestions.length > 0 && (
        <div className="flex flex-wrap gap-2 mt-3">
          {suggestions.map(s => (
            <button
              key={s}
              onClick={() => onSuggestionClick?.(s)}
              className="border border-[#444] px-3 py-1.5 text-xs font-pixel cursor-pointer text-[#AAA] hover:border-text-inv hover:text-text-inv hover:bg-white/5 transition-all"
            >
              {s}
            </button>
          ))}
        </div>
      )}
    </div>
  )
}
```

---

## 12. Typing Indicator (src/components/chat/TypingIndicator.tsx)

```tsx
export function TypingIndicator() {
  return (
    <div className="flex gap-1 p-3 bg-dark w-fit">
      {[0, 1, 2].map(i => (
        <div
          key={i}
          className="w-1.5 h-1.5 bg-text-inv rounded-none"
          style={{
            animation: 'pulse 1s infinite ease-in-out',
            animationDelay: `${i * 0.2}s`,
          }}
        />
      ))}
    </div>
  )
}
```

---

## 13. Chat Input Area (src/components/chat/ChatInput.tsx)

```tsx
'use client'

import { useState } from 'react'

interface ChatInputProps {
  onSend: (message: string) => void
  placeholder?: string
}

export function ChatInput({ onSend, placeholder = 'Type your message...' }: ChatInputProps) {
  const [input, setInput] = useState('')

  const handleSend = () => {
    if (input.trim()) {
      onSend(input.trim())
      setInput('')
    }
  }

  return (
    <div className="p-6 bg-canvas border-t-2 border-dark flex gap-3">
      <input
        type="text"
        value={input}
        onChange={(e) => setInput(e.target.value)}
        onKeyDown={(e) => e.key === 'Enter' && handleSend()}
        className="flex-1 bg-transparent border border-dark p-4 font-sans text-base focus:outline-none"
        placeholder={placeholder}
      />
      <button
        onClick={handleSend}
        className="bg-dark text-canvas px-6 font-pixel hover:opacity-90 transition-opacity"
      >
        SEND_&gt;
      </button>
    </div>
  )
}
```

---

## 14. Primary Button (src/components/ui/Button.tsx)

```tsx
import { ButtonHTMLAttributes } from 'react'

interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: 'primary' | 'secondary'
}

export function Button({ children, variant = 'primary', className = '', ...props }: ButtonProps) {
  const baseClasses = 'font-pixel px-6 py-4 transition-all disabled:opacity-50'
  const variantClasses = {
    primary: 'bg-dark text-canvas hover:opacity-90',
    secondary: 'bg-transparent border-2 border-dark hover:bg-dark hover:text-canvas',
  }

  return (
    <button
      className={`${baseClasses} ${variantClasses[variant]} ${className}`}
      {...props}
    >
      {children}
    </button>
  )
}
```

---

## 15. Intelligence Panel Header (src/components/panels/IntelligenceHeader.tsx)

```tsx
import { AIThinkingStatus } from '../ui/AIThinkingStatus'

interface IntelligenceHeaderProps {
  isThinking?: boolean
}

export function IntelligenceHeader({ isThinking = false }: IntelligenceHeaderProps) {
  return (
    <div className="flex justify-between items-center pb-6 border-b border-[#333]">
      <div className="label text-text-inv">LIVE_CONTEXT</div>
      {isThinking && <AIThinkingStatus />}
    </div>
  )
}
```

---

## 16. Action Bar (src/components/layout/ActionBar.tsx)

```tsx
import { ReactNode } from 'react'

interface ActionBarProps {
  children: ReactNode
}

export function ActionBar({ children }: ActionBarProps) {
  return (
    <div className="p-6 bg-canvas border-t-2 border-dark flex justify-end gap-3 shrink-0">
      {children}
    </div>
  )
}
```

---

## 17. Warning Alert (src/components/ui/Alert.tsx)

```tsx
interface AlertProps {
  children: React.ReactNode
  variant?: 'warning' | 'info'
}

export function Alert({ children, variant = 'warning' }: AlertProps) {
  const colors = {
    warning: 'text-[#FF4141]',
    info: 'text-[#AAA]',
  }

  return (
    <div className={`text-xs mt-3 ${colors[variant]}`}>
      ! {children}
    </div>
  )
}
```

---

## 18. Form Input (src/components/ui/Input.tsx)

```tsx
import { InputHTMLAttributes } from 'react'

interface InputProps extends InputHTMLAttributes<HTMLInputElement> {
  label?: string
}

export function Input({ label, className = '', ...props }: InputProps) {
  return (
    <div>
      {label && <div className="label">{label}</div>}
      <input
        className={`w-full bg-transparent border border-dark p-3 font-sans focus:outline-none focus:border-2 ${className}`}
        {...props}
      />
    </div>
  )
}
```

---

## 19. Form Textarea (src/components/ui/Textarea.tsx)

```tsx
import { TextareaHTMLAttributes } from 'react'

interface TextareaProps extends TextareaHTMLAttributes<HTMLTextAreaElement> {
  label?: string
}

export function Textarea({ label, className = '', ...props }: TextareaProps) {
  return (
    <div>
      {label && <div className="label">{label}</div>}
      <textarea
        className={`w-full bg-transparent border border-dark p-3 font-sans focus:outline-none focus:border-2 resize-none ${className}`}
        {...props}
      />
    </div>
  )
}
```

---

## 20. Image Upload Zone (src/components/ui/ImageUpload.tsx)

```tsx
'use client'

import { useState, useCallback } from 'react'

interface ImageUploadProps {
  onUpload: (file: File) => void
}

export function ImageUpload({ onUpload }: ImageUploadProps) {
  const [isDragging, setIsDragging] = useState(false)
  const [preview, setPreview] = useState<string | null>(null)

  const handleDrop = useCallback((e: React.DragEvent) => {
    e.preventDefault()
    setIsDragging(false)
    const file = e.dataTransfer.files[0]
    if (file && file.type.startsWith('image/')) {
      setPreview(URL.createObjectURL(file))
      onUpload(file)
    }
  }, [onUpload])

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const file = e.target.files?.[0]
    if (file) {
      setPreview(URL.createObjectURL(file))
      onUpload(file)
    }
  }

  return (
    <div
      onDragOver={(e) => { e.preventDefault(); setIsDragging(true) }}
      onDragLeave={() => setIsDragging(false)}
      onDrop={handleDrop}
      className={`border-2 border-dashed p-6 text-center cursor-pointer transition-colors ${
        isDragging ? 'border-dark bg-dark/10' : 'border-[#888]'
      }`}
    >
      {preview ? (
        <img src={preview} alt="Preview" className="max-h-48 mx-auto" />
      ) : (
        <>
          <div className="text-4xl mb-2">↑</div>
          <div className="label justify-center">DROP IMAGE OR CLICK</div>
          <input
            type="file"
            accept="image/*"
            onChange={handleChange}
            className="hidden"
            id="image-upload"
          />
          <label htmlFor="image-upload" className="cursor-pointer font-pixel text-sm mt-2 inline-block hover:underline">
            BROWSE_FILES
          </label>
        </>
      )}
    </div>
  )
}
```

---

## Quick Start

1. Copy globals.css content to `src/app/globals.css`
2. Update `tailwind.config.ts` with the config above
3. Create `src/components/` folder structure
4. Copy components as needed
5. Use `<AppLayout>` as your page wrapper

```tsx
// Example page
import { AppLayout } from '@/components/layout/AppLayout'
import { WorkspaceHeader } from '@/components/layout/WorkspaceHeader'
import { ResultCard } from '@/components/ui/ResultCard'
import { HSCodeDisplay } from '@/components/ui/HSCodeDisplay'

export default function ClassifyPage() {
  return (
    <AppLayout
      rightPanel={
        <>
          <ResultCard label="PROBABLE HS CODE">
            <HSCodeDisplay code="8504.40.95" />
          </ResultCard>
        </>
      }
    >
      <WorkspaceHeader
        title="CLASSIFY"
        pixelTitle="PRODUCT"
        metaLabel="ANALYSIS ID"
        metaValue="HS_8824_XQ"
      />
      {/* Page content */}
    </AppLayout>
  )
}
```

---

All components follow the brutalist design system with:
- Zero border radius
- Silkscreen pixel font for UI elements
- Space Grotesk for body text
- Dark (#080808) and canvas (#C8C8C8) color scheme
