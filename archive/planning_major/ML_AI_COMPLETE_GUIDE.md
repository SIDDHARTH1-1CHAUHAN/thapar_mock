# ML/AI Complete Guide - TradeOptimize AI
## Everything You Need to Know About AI in This Project

---

# EXECUTIVE SUMMARY

| Question | Answer |
|----------|--------|
| Do we train any models? | **NO** - We use pre-trained LLMs |
| What AI do we use? | **Groq API** (Llama 3.1 70B) - FREE |
| Fallback if Groq fails? | **Ollama** (local) or **Mock Data** |
| OCR for documents? | **Tesseract** (free, open source) |
| Image understanding? | **HuggingFace CLIP** (optional) |
| Total AI cost? | **$0** (all free tiers) |

---

# ML/AI COMPONENTS BREAKDOWN

## 1. Groq API - Primary LLM (FREE)

**What it is**: Cloud API running Llama 3.1 70B model
**Why we use it**: Fastest inference (800 tokens/sec), FREE tier
**What it does**: HS code classification, AI assistant chat

### Setup Steps
```
STEP 1: Get API Key (2 minutes)
─────────────────────────────────
1. Go to https://console.groq.com
2. Click "Sign Up" → Use Google account
3. Once logged in, click "API Keys" in left sidebar
4. Click "Create API Key"
5. Name it "hackathon" and copy the key
   (Starts with "gsk_")

STEP 2: Save the Key
─────────────────────
Add to backend/.env:
GROQ_API_KEY=gsk_xxxxxxxxxxxxxxxxxxxx

STEP 3: Verify It Works
───────────────────────
Run this in terminal:
curl -X POST "https://api.groq.com/openai/v1/chat/completions" \
  -H "Authorization: Bearer gsk_YOUR_KEY_HERE" \
  -H "Content-Type: application/json" \
  -d '{"model": "llama-3.1-70b-versatile", "messages": [{"role": "user", "content": "Hello"}]}'

Should return a JSON response with "Hello" greeting.
```

### Free Tier Limits
| Limit | Value | Is It Enough? |
|-------|-------|---------------|
| Requests/minute | 30 | Yes - demo won't hit this |
| Tokens/minute | 6,000 | Yes - ~10 classifications/min |
| Requests/day | 14,400 | Yes - way more than needed |

### When Groq is Used (Backend Phase 2)
```
Hour 3-6: Building HS Classification Microservice
├── llm_service.py calls Groq API
├── Prompt engineering for HS classification
├── Response parsing to extract HS codes
└── Redis caching to avoid repeat calls
```

---

## 2. Tesseract OCR - Document Text Extraction (FREE)

**What it is**: Open source OCR engine
**Why we use it**: Extract text from product images/invoices
**What it does**: Image → Text conversion

### Setup Steps (Windows)
```
STEP 1: Download Tesseract
──────────────────────────
Go to: https://github.com/UB-Mannheim/tesseract/wiki
Download: tesseract-ocr-w64-setup-5.3.x.exe

STEP 2: Install
───────────────
- Run the installer
- Use default path: C:\Program Files\Tesseract-OCR
- Check "Add to PATH" if available

STEP 3: Add to PATH (if not done by installer)
──────────────────────────────────────────────
1. Press Windows key, search "Environment Variables"
2. Click "Edit the system environment variables"
3. Click "Environment Variables" button
4. Under "System variables", find "Path", click "Edit"
5. Click "New", add: C:\Program Files\Tesseract-OCR
6. Click OK on all dialogs

STEP 4: Verify Installation
───────────────────────────
Open NEW terminal (important!), run:
tesseract --version

Should show: tesseract 5.3.x
```

### When Tesseract is Used (Backend Phase 2)
```
Hour 4-5: Building OCR Service
├── User uploads product image/invoice
├── Tesseract extracts text from image
├── Extracted text sent to Groq for classification
└── Combined result returned to frontend
```

### If Tesseract Fails
```python
# The code handles this gracefully:
async def extract_text(self, image_bytes: bytes) -> str:
    try:
        text = pytesseract.image_to_string(image)
        return text.strip()
    except Exception as e:
        logger.error(f"OCR failed: {e}")
        return ""  # Empty string - classification still works with description
```
**Text-based classification works perfectly without OCR!**

---

## 3. Ollama - Local LLM Fallback (FREE)

**What it is**: Run LLMs locally on your machine
**Why we use it**: Backup if Groq rate-limited or down
**What it does**: Same as Groq but slower (local)

### Setup Steps (Optional - Only if Groq Fails)
```
STEP 1: Download Ollama
───────────────────────
Go to: https://ollama.ai/download
Download Windows version, install

STEP 2: Pull Mistral Model
──────────────────────────
Open terminal, run:
ollama pull mistral

(Downloads ~4GB, takes 5-10 minutes)

STEP 3: Verify
──────────────
ollama run mistral "Hello"

Should respond with greeting.
```

### When Ollama is Used
```python
# In llm_service.py - automatic fallback:
async def classify(self, description: str):
    # Try Groq first
    if self.groq_api_key:
        try:
            return await self._groq_classify(prompt)
        except:
            pass  # Fall through to Ollama

    # Fallback to Ollama
    return await self._ollama_classify(prompt)
```

**You probably won't need Ollama - Groq is very reliable!**

---

## 4. HuggingFace CLIP - Image Understanding (OPTIONAL)

**What it is**: Vision model that understands images
**Why we might use it**: Better image-based classification
**Status**: OPTIONAL - Not in main implementation

### If You Want to Add It
```python
# pip install transformers torch

from transformers import CLIPProcessor, CLIPModel

model = CLIPModel.from_pretrained("openai/clip-vit-base-patch32")
processor = CLIPProcessor.from_pretrained("openai/clip-vit-base-patch32")

# Classify image
inputs = processor(images=image, return_tensors="pt")
features = model.get_image_features(**inputs)
```

**Skip this for hackathon - Tesseract + Groq is enough!**

---

# ML/AI TIMELINE - WHEN TO DO WHAT

## Before Hackathon (Night Before)
```
□ Get Groq API key (2 min)
  → https://console.groq.com
  → Create account with Google
  → Generate API key
  → Save it somewhere safe

□ Install Tesseract OCR (5 min)
  → Download from GitHub
  → Install with defaults
  → Add to PATH
  → Verify: tesseract --version

□ (Optional) Install Ollama (10 min)
  → Only if you want local fallback
  → ollama pull mistral
```

## Hour 0-3: Backend Phase 1 (No AI Yet)
```
Focus: Database setup, FastAPI structure
AI Work: None
Just set up the foundation.
```

## Hour 3-6: Backend Phase 2 (MAIN AI WORK)
```
This is where ALL the AI happens!

Hour 3:00-3:30
├── Create microservices/hs_classifier/ structure
├── Add requirements.txt with pytesseract, httpx
└── Create main.py with FastAPI app

Hour 3:30-4:30
├── Build llm_service.py
│   ├── Groq API integration
│   ├── Prompt engineering for HS codes
│   ├── Response parsing
│   └── Ollama fallback
└── Test classification works

Hour 4:30-5:00
├── Build ocr_service.py
│   ├── Tesseract integration
│   ├── Image preprocessing
│   └── Text extraction
└── Test OCR with sample image

Hour 5:00-5:30
├── Build cache_service.py
│   ├── Redis integration
│   └── Cache classification results
└── Test caching works

Hour 5:30-6:00
├── Build classify.py API endpoints
│   ├── POST /api/classify (text)
│   ├── POST /api/classify/image (OCR)
│   └── POST /api/classify/batch
├── Add rate limiting middleware
└── Test all endpoints
```

## Hour 6-9: Backend Phase 3 (No New AI)
```
Focus: Route Optimizer microservice
AI Work: None (uses algorithms, not ML)
```

## Hour 9-12: Backend Phase 4 (AI Assistant)
```
Hour 10-11: AI Assistant Chat
├── Build assistant_service.py
│   ├── Uses same Groq API
│   ├── Different prompt (conversational)
│   └── Context-aware responses
└── Integrate with frontend chat UI
```

---

# PROMPT ENGINEERING (Our "Fine-Tuning")

## Why We Don't Train Models

Traditional ML approach:
```
Collect data → Label data → Train model → Deploy
Time: Weeks | Cost: $$$$ | Accuracy: Variable
```

Our approach (Prompt Engineering):
```
Write detailed prompt → Use pre-trained LLM → Deploy
Time: Hours | Cost: $0 | Accuracy: 90%+
```

## The Classification Prompt (This IS the AI magic)

```python
HS_CLASSIFICATION_PROMPT = """You are an expert customs classification specialist with 20+ years of experience.
You have memorized the entire Harmonized System (HS) nomenclature and all General Interpretive Rules (GIR).

CLASSIFICATION RULES TO APPLY:
1. GIR 1: First, determine classification by the terms of headings and section/chapter notes
2. GIR 2(a): Incomplete articles classified as complete if having essential character
3. GIR 2(b): Mixtures classified by component giving essential character
4. GIR 3(a): Most specific heading preferred
5. GIR 3(b): Composite goods - classify by essential character component
6. GIR 3(c): If still undetermined, use heading occurring last in numerical order
7. GIR 4: Goods classified to most akin heading
8. GIR 5: Cases and containers classified with contents
9. GIR 6: Classification at subheading level follows same rules

COMMON HS CODE PATTERNS:
- Chapter 84: Machinery, mechanical appliances
- Chapter 85: Electrical machinery and equipment
- Chapter 61-62: Apparel (knit vs woven)
- Chapter 39: Plastics
- Chapter 73: Iron/steel articles
- Chapter 94: Furniture

Respond ONLY with valid JSON in this exact format:
{
    "hs_code": "XXXX.XX.XX",
    "confidence": 85,
    "description": "Official HS description",
    "chapter": "Chapter XX - Name",
    "gir_applied": "GIR X - Explanation",
    "reasoning": "Step-by-step classification logic",
    "alternatives": [{"code": "XXXX.XX", "reason": "Why this could also apply"}]
}
"""
```

**This prompt replaces months of model training!**

---

# FALLBACK STRATEGY (If AI Fails)

## Level 1: Groq Rate Limited
```
→ Automatic fallback to Ollama (local)
→ Slower (3-8 sec vs 2-3 sec) but works
```

## Level 2: Ollama Not Installed
```
→ Return mock classification
→ Still shows UI working
→ Use for demo if needed
```

## Level 3: Demo Emergency
```
→ Set NEXT_PUBLIC_USE_MOCK=true
→ All responses from DEMO_MOCK_DATA.md
→ Perfect demo every time
```

---

# TESTING AI BEFORE DEMO

## Test 1: Groq API
```bash
curl -X POST "https://api.groq.com/openai/v1/chat/completions" \
  -H "Authorization: Bearer $GROQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama-3.1-70b-versatile",
    "messages": [{"role": "user", "content": "Classify this product: wireless bluetooth headphones with noise cancellation"}]
  }'
```

## Test 2: Classification Endpoint
```bash
curl -X POST http://localhost:8001/api/classify \
  -H "Content-Type: application/json" \
  -d '{"description": "Solar-powered portable charger with LED flashlight"}'
```

## Test 3: OCR Endpoint
```bash
curl -X POST http://localhost:8001/api/classify/image \
  -F "image=@product_photo.jpg" \
  -F "additional_context=Power bank from China"
```

---

# QUICK REFERENCE CARD

```
┌─────────────────────────────────────────────────────────┐
│                 ML/AI QUICK REFERENCE                   │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  GROQ API (Primary LLM)                                │
│  ───────────────────────                               │
│  URL: https://api.groq.com/openai/v1/chat/completions  │
│  Model: llama-3.1-70b-versatile                        │
│  Free: 30 req/min, 6000 tokens/min                     │
│  Speed: 2-3 seconds                                     │
│                                                         │
│  TESSERACT (OCR)                                        │
│  ───────────────                                        │
│  Path: C:\Program Files\Tesseract-OCR                  │
│  Python: pytesseract.image_to_string(image)            │
│  Fallback: Use text description only                   │
│                                                         │
│  OLLAMA (Local Fallback)                               │
│  ───────────────────────                               │
│  URL: http://localhost:11434                            │
│  Model: mistral                                         │
│  Speed: 3-8 seconds                                     │
│                                                         │
│  REDIS (Caching)                                        │
│  ──────────────                                         │
│  Cache key: md5(description)                           │
│  TTL: 24 hours                                          │
│  Cached speed: <50ms                                    │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

# SUMMARY: WHAT TO DO WHEN

| Time | Action | Who |
|------|--------|-----|
| Night Before | Get Groq API key | Everyone |
| Night Before | Install Tesseract | Backend team |
| Night Before | (Optional) Install Ollama | Backend team |
| Hour 3-6 | Build classification microservice | Backend |
| Hour 3-6 | Implement Groq integration | Backend |
| Hour 3-6 | Implement OCR service | Backend |
| Hour 9-12 | Build AI assistant | Backend |
| Before Demo | Test all AI endpoints | Everyone |

**The AI work is concentrated in Backend Phase 2 (Hours 3-6). That's when all the magic happens!**
