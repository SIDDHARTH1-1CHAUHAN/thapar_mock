# Frontend Phase 2: Classify + AI Assistant (Hours 3-6)
## Branch: `feature/fe-phase-2-classify-assistant`

---

## AI INTEGRATION IN THIS PHASE

```
┌─────────────────────────────────────────────────────────┐
│  FRONTEND CALLS BACKEND AI ENDPOINTS                   │
│  ──────────────────────────────────                    │
│                                                         │
│  HS Classification Page:                                │
│  → POST /api/classify (text description)               │
│  → POST /api/classify/image (with OCR)                 │
│  → Backend uses Groq LLM + Tesseract OCR               │
│                                                         │
│  AI Assistant Page:                                     │
│  → POST /api/v1/assistant/chat                         │
│  → Backend uses Groq LLM (conversational)              │
│                                                         │
│  For now: Use MOCK API until backend is ready!         │
│  Mock responses already included in this phase.        │
└─────────────────────────────────────────────────────────┘
```

---

## Prerequisites
- Phase FE-1 must be merged to develop
- Pull latest: `git checkout develop && git pull`

---

## Your Mission

Build the HS Classification page and AI Assistant chat interface.

---

## Deliverables

### 1. HS Classification Page (`/classify`)

```
frontend/src/
├── app/(dashboard)/classify/page.tsx
└── components/features/classify/
    ├── ProductDescriptionForm.tsx
    ├── ImageUploadSection.tsx
    ├── TechnicalSpecs.tsx
    └── ClassificationPanel.tsx    # Right panel content
```

#### ProductDescriptionForm.tsx
```tsx
'use client'
import { useState } from 'react'
import { Textarea } from '@/components/ui/Textarea'

export function ProductDescriptionForm({ onSubmit }: { onSubmit: (desc: string) => void }) {
  const [description, setDescription] = useState('')

  return (
    <div className="space-y-4">
      <div className="label">PRODUCT DESCRIPTION</div>
      <Textarea
        value={description}
        onChange={(e) => setDescription(e.target.value)}
        placeholder="Describe your product in detail. Include materials, function, intended use..."
        className="h-40"
      />
      <div className="text-xs opacity-60">
        TIP: Include technical specifications, materials, and primary function for better classification.
      </div>
    </div>
  )
}
```

#### ImageUploadSection.tsx
```tsx
'use client'
import { useState, useCallback } from 'react'

export function ImageUploadSection({ onUpload }: { onUpload: (file: File) => void }) {
  const [preview, setPreview] = useState<string | null>(null)
  const [isDragging, setIsDragging] = useState(false)

  const handleDrop = useCallback((e: React.DragEvent) => {
    e.preventDefault()
    setIsDragging(false)
    const file = e.dataTransfer.files[0]
    if (file?.type.startsWith('image/')) {
      setPreview(URL.createObjectURL(file))
      onUpload(file)
    }
  }, [onUpload])

  return (
    <div
      onDragOver={(e) => { e.preventDefault(); setIsDragging(true) }}
      onDragLeave={() => setIsDragging(false)}
      onDrop={handleDrop}
      className={`border-2 border-dashed p-8 text-center transition-colors ${
        isDragging ? 'border-dark bg-dark/5' : 'border-[#888]'
      }`}
    >
      {preview ? (
        <div>
          <img src={preview} alt="Preview" className="max-h-48 mx-auto mb-4" />
          <button onClick={() => setPreview(null)} className="text-xs underline">
            REMOVE_IMAGE
          </button>
        </div>
      ) : (
        <>
          <div className="text-4xl mb-2">↑</div>
          <div className="label justify-center mb-2">DROP PRODUCT IMAGE</div>
          <div className="text-xs opacity-60">or click to browse</div>
          <input
            type="file"
            accept="image/*"
            onChange={(e) => {
              const file = e.target.files?.[0]
              if (file) {
                setPreview(URL.createObjectURL(file))
                onUpload(file)
              }
            }}
            className="absolute inset-0 opacity-0 cursor-pointer"
          />
        </>
      )}
    </div>
  )
}
```

### 2. AI Assistant Page (`/assistant`)

```
frontend/src/
├── app/(dashboard)/assistant/page.tsx
└── components/features/assistant/
    ├── ChatContainer.tsx
    ├── MessageList.tsx
    ├── Message.tsx
    ├── TypingIndicator.tsx
    ├── SuggestionChips.tsx
    └── ChatInput.tsx
```

#### Message.tsx
```tsx
interface MessageProps {
  type: 'ai' | 'user'
  content: string
  suggestions?: string[]
  onSuggestionClick?: (s: string) => void
}

export function Message({ type, content, suggestions, onSuggestionClick }: MessageProps) {
  return (
    <div className={`max-w-[80%] p-4 text-sm leading-relaxed animate-slide-in ${
      type === 'ai'
        ? 'self-start bg-dark text-text-inv border border-[#333]'
        : 'self-end border border-dark'
    }`}>
      {type === 'ai' && <div className="label text-[#888] mb-2">SYSTEM_CORE</div>}
      <div className="whitespace-pre-wrap">{content}</div>
      {suggestions && suggestions.length > 0 && (
        <div className="flex flex-wrap gap-2 mt-3">
          {suggestions.map(s => (
            <button
              key={s}
              onClick={() => onSuggestionClick?.(s)}
              className="border border-[#444] px-3 py-1.5 text-xs font-pixel text-[#AAA] hover:text-text-inv hover:border-text-inv transition-all"
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

#### TypingIndicator.tsx
```tsx
export function TypingIndicator() {
  return (
    <div className="flex gap-1 p-3 bg-dark w-fit self-start">
      {[0, 1, 2].map(i => (
        <div
          key={i}
          className="w-1.5 h-1.5 bg-text-inv"
          style={{
            animation: 'pulse 1s infinite',
            animationDelay: `${i * 0.2}s`,
          }}
        />
      ))}
    </div>
  )
}
```

#### ChatInput.tsx
```tsx
'use client'
import { useState } from 'react'

export function ChatInput({ onSend, disabled }: { onSend: (msg: string) => void; disabled?: boolean }) {
  const [input, setInput] = useState('')

  const handleSend = () => {
    if (input.trim() && !disabled) {
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
        placeholder="Query trade database or ask for classification help..."
        className="flex-1 bg-transparent border border-dark p-4 focus:outline-none"
        disabled={disabled}
      />
      <button
        onClick={handleSend}
        disabled={disabled}
        className="bg-dark text-canvas px-6 font-pixel disabled:opacity-50"
      >
        SEND_&gt;
      </button>
    </div>
  )
}
```

### 3. Keyboard Shortcuts

```tsx
// src/hooks/useKeyboardShortcuts.ts
'use client'
import { useEffect } from 'react'
import { useRouter } from 'next/navigation'

export function useKeyboardShortcuts() {
  const router = useRouter()

  useEffect(() => {
    const handleKeyDown = (e: KeyboardEvent) => {
      // Ctrl/Cmd + K = AI Assistant
      if ((e.ctrlKey || e.metaKey) && e.key === 'k') {
        e.preventDefault()
        router.push('/assistant')
      }
      // Ctrl/Cmd + Shift + C = Classify
      if ((e.ctrlKey || e.metaKey) && e.shiftKey && e.key === 'C') {
        e.preventDefault()
        router.push('/classify')
      }
      // Ctrl/Cmd + Shift + L = Landed Cost
      if ((e.ctrlKey || e.metaKey) && e.shiftKey && e.key === 'L') {
        e.preventDefault()
        router.push('/landed-cost')
      }
    }

    window.addEventListener('keydown', handleKeyDown)
    return () => window.removeEventListener('keydown', handleKeyDown)
  }, [router])
}

// Add to dashboard layout:
// useKeyboardShortcuts()
```

### 4. API Client Setup (Real + Mock)

```typescript
// src/lib/api.ts
const API_BASE = process.env.NEXT_PUBLIC_API_URL || 'http://localhost:8000'
const HS_SERVICE_URL = process.env.NEXT_PUBLIC_HS_SERVICE_URL || 'http://localhost:8001'
const USE_MOCK = process.env.NEXT_PUBLIC_USE_MOCK === 'true'

const MOCK_DELAY = 1500

// Classification API
export async function classifyProduct(description: string, image?: File) {
  if (USE_MOCK) {
    await new Promise(r => setTimeout(r, MOCK_DELAY))
    return {
      hs_code: '8504.40.95',
      confidence: 94,
      description: 'Static converters; power supplies',
      chapter: 'Chapter 85 - Electrical machinery',
      gir_applied: 'GIR 3(b) - Essential character',
      reasoning: 'Based on GIR 3(b), the essential character is the charging function.',
      primary_function: 'Charging electronic devices',
      alternatives: [
        { code: '8513.10.40', description: 'Portable flashlights', why_not: 'Secondary feature' },
      ],
      cached: false,
      processing_time_ms: 2340,
    }
  }

  // Real API call
  const response = await fetch(`${HS_SERVICE_URL}/api/classify`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ description }),
  })
  if (!response.ok) throw new Error('Classification failed')
  return response.json()
}

// Image Classification API
export async function classifyFromImage(image: File, additionalContext?: string) {
  if (USE_MOCK) {
    await new Promise(r => setTimeout(r, MOCK_DELAY))
    return {
      classification: {
        hs_code: '8504.40.95',
        confidence: 91,
        description: 'Static converters',
        reasoning: 'OCR extracted product details from image',
      },
      ocr_data: {
        raw_text: 'Product detected from image',
        detected_fields: {},
      },
    }
  }

  const formData = new FormData()
  formData.append('image', image)
  if (additionalContext) formData.append('additional_context', additionalContext)

  const response = await fetch(`${HS_SERVICE_URL}/api/classify/image`, {
    method: 'POST',
    body: formData,
  })
  if (!response.ok) throw new Error('Image classification failed')
  return response.json()
}

// AI Assistant Chat API
export async function sendChatMessage(message: string, context?: Array<{role: string, content: string}>) {
  if (USE_MOCK) {
    await new Promise(r => setTimeout(r, MOCK_DELAY))
    return {
      response: `Processing your query about: "${message}"\n\nBased on the information provided, I recommend checking the tariff schedule for relevant HS codes.\n\n**Key points:**\n- Verify the product's primary function\n- Check Section 301 tariff applicability\n- Ensure required documentation is ready`,
      suggestions: ['VIEW_DETAILS', 'CALCULATE_COST', 'CHECK_COMPLIANCE']
    }
  }

  const response = await fetch(`${API_BASE}/api/v1/assistant/chat`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ message, context }),
  })
  if (!response.ok) throw new Error('Chat request failed')
  return response.json()
}

// Landed Cost API
export async function calculateLandedCost(params: {
  hs_code: string
  product_value: number
  quantity: number
  origin_country: string
  destination_country: string
  shipping_mode: string
}) {
  if (USE_MOCK) {
    await new Promise(r => setTimeout(r, MOCK_DELAY))
    return {
      total_landed_cost: 81634,
      cost_per_unit: 16.33,
      breakdown: {
        product_value: params.product_value,
        base_duty: 0,
        section_301: params.product_value * 0.25,
        mpf: 216.50,
        hmf: 78.13,
        freight: 1850,
        insurance: 312.50,
      },
      effective_duty_rate: 25.47,
      warnings: ['Section 301 tariff applies: 25%'],
    }
  }

  const response = await fetch(`${API_BASE}/api/v1/landed-cost/calculate`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(params),
  })
  if (!response.ok) throw new Error('Landed cost calculation failed')
  return response.json()
}

// Compliance Check API
export async function checkCompliance(params: {
  hs_code: string
  origin_country: string
  destination_country: string
  supplier_name?: string
}) {
  if (USE_MOCK) {
    await new Promise(r => setTimeout(r, MOCK_DELAY))
    return {
      overall_risk_score: 72,
      risk_level: 'MEDIUM',
      checks: [
        { category: 'OFAC Sanctions', status: 'CLEAR', details: 'No matches found' },
        { category: 'Section 301', status: 'WARNING', details: '25% additional duty applies' },
      ],
      required_documents: [
        { name: 'Commercial Invoice', status: 'required' },
        { name: 'Bill of Lading', status: 'required' },
        { name: 'UN38.3 Test Summary', status: 'required' },
      ],
      warnings: ['Section 301 tariffs apply'],
    }
  }

  const response = await fetch(`${API_BASE}/api/v1/compliance/check`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(params),
  })
  if (!response.ok) throw new Error('Compliance check failed')
  return response.json()
}
```

### 5. React Query Provider Setup

```tsx
// src/app/providers.tsx
'use client'
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { useState } from 'react'

export function Providers({ children }: { children: React.ReactNode }) {
  const [queryClient] = useState(() => new QueryClient({
    defaultOptions: {
      queries: {
        staleTime: 60 * 1000, // 1 minute
        retry: 1,
      },
    },
  }))

  return (
    <QueryClientProvider client={queryClient}>
      {children}
    </QueryClientProvider>
  )
}

// Add to src/app/layout.tsx:
// import { Providers } from './providers'
// Wrap {children} with <Providers>{children}</Providers>
```

### 6. Custom Hooks for API Calls

```tsx
// src/hooks/useClassification.ts
import { useMutation, useQuery } from '@tanstack/react-query'
import { classifyProduct, classifyFromImage } from '@/lib/api'

export function useClassification() {
  return useMutation({
    mutationFn: (description: string) => classifyProduct(description),
  })
}

export function useImageClassification() {
  return useMutation({
    mutationFn: ({ image, context }: { image: File; context?: string }) =>
      classifyFromImage(image, context),
  })
}

// src/hooks/useChat.ts
import { useMutation } from '@tanstack/react-query'
import { sendChatMessage } from '@/lib/api'

export function useChat() {
  return useMutation({
    mutationFn: ({ message, context }: { message: string; context?: any[] }) =>
      sendChatMessage(message, context),
  })
}
```
```

---

## Commit Schedule

```bash
# Hour 3:30
git commit -m "feat(fe2): classify page layout and product form"

# Hour 4:00
git commit -m "feat(fe2): image upload with drag-drop"

# Hour 4:30
git commit -m "feat(fe2): AI assistant chat container"

# Hour 5:00
git commit -m "feat(fe2): message components with animations"

# Hour 5:30
git commit -m "feat(fe2): keyboard shortcuts hook"

# Hour 6:00
git commit -m "feat(fe2): mock API integration and polish"
git push -u origin feature/fe-phase-2-classify-assistant
```

---

## Testing Checklist

- [ ] /classify page loads with form and image upload
- [ ] Image drag-drop works
- [ ] /assistant page shows chat interface
- [ ] Messages appear with slide-in animation
- [ ] Typing indicator animates
- [ ] Suggestion chips are clickable
- [ ] Keyboard shortcuts work (Ctrl+K, Ctrl+Shift+C)

---

## PR Template

```markdown
## Phase FE-2: Classify + AI Assistant

### Changes
- HS Classification page with product form
- Image upload with drag-drop support
- AI Assistant chat interface
- Message animations and typing indicator
- Keyboard shortcuts (Ctrl+K, Ctrl+Shift+C)
- Mock API integration

### Screenshots
[Add screenshots of both pages]

### Testing
- [x] Image upload works
- [x] Chat messages animate
- [x] Keyboard shortcuts function
```
