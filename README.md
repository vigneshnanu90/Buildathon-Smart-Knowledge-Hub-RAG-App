# ✨ Smart Knowledge Hub — Universal Web & PDF RAG Studio

An enterprise-grade, full-stack **Retrieval-Augmented Generation (RAG)** platform that transforms any live website URL or batch of PDF documents into an interactive, voice-enabled, multilingual AI knowledge base[cite: 10].

Built with **Playwright (Stealth Headless Chromium)**, **FastAPI**, **LangChain**, **FAISS Vector Database (with In-Memory Singleton Caching)**, **SQLite SHA-256 Deduplication**, **Multi-Layer AI Guardrails**, **OpenAI (`gpt-4o-mini` & `text-embedding-3-small`)**, and a **React 19 (Vite)** glassmorphic frontend featuring live browser telemetry, an interactive arcade mini-game, and Indian regional voice chat[cite: 10].

🔗 **GitHub Repository:** [https://github.com/vigneshnanu90/Buildathon-Smart-Knowledge-Hub-RAG-App](https://github.com/vigneshnanu90/Buildathon-Smart-Knowledge-Hub-RAG-App)[cite: 5, 10]

---

## 🚀 Key Features

### 1. Dual Ingestion Engine (Live Websites + Multi-PDF Batch Upload)
* **Universal Playwright Web Scraper:** Scrapes static and dynamic websites (including government scheme portals like TNAU Agritech, PM-KISAN, Amazon, and Flipkart)[cite: 1, 2, 3].
  * Automatically converts HTML `<table>` elements and e-commerce product cards into structured **Markdown tables** so rows, prices, ratings, and links stay aligned[cite: 10].
  * Intercepts background `XHR/Fetch` JSON API payloads, `application/ld+json` schemas, and `<meta>` descriptions.
  * Automatically discovers, downloads, and parses embedded `.pdf` links found on webpages[cite: 10].
* **Multi-PDF Batch Processor (`PyMuPDF`):** Select and upload single or multiple PDF files simultaneously (`01_Leave_Policy.pdf`, `02_Hybrid_Work.pdf`, etc.) with live file-by-file progress tracking and structured page table extraction[cite: 1, 10].

### 2. Multi-Layer AI & System Guardrails 🛡️
* **Prompt-Injection & Jailbreak Defense:** Pre-screens incoming chat queries to block system-prompt overrides (*"ignore previous instructions"*, *"reveal system prompt"*, jailbreak personas) and enforces a 1,500-character input cap to prevent token exhaustion.
* **Empty-Library & Hallucination Guardrail:** Blocks OpenAI calls when the library is empty and strictly grounds answers in retrieved document excerpts and SQLite inventory metadata.
* **Deterministic Unicode Language-Lock Guardrail:** Inspects Unicode script ranges (`\u0B80-\u0BFF` Tamil, `\u0D00-\u0D7F` Malayalam, `\u0900-\u097F` Hindi, `\u0C00-\u0C7F` Telugu, `\u0C80-\u0CBF` Kannada) on every turn so English queries never accidentally reply in Tamil due to chat history or bilingual source chunks.
* **Input & File Sanitization Guardrails:** Enforces Pydantic `HttpUrl` validation, strips noisy ad-tracking URL parameters (`utm_*`, `adgrpid`, `hvadid`, `msclkid`, `gclid`), and verifies binary `%PDF` magic bytes.
* **Scraper Anti-Hang & Timeout Recovery:** Combines an 18-second Playwright navigation timeout, automatic `requests` HTTP fallback, and a 40-second frontend Axios abort timer so paywalled or blocked sites fail gracefully and reset the Live Monitor[cite: 3, 10].

### 3. Smart Deduplication, In-Memory FAISS Cache & Synced Library Control
* **SHA-256 Deduplication Registry (`SQLite`):** Hashes normalized URLs and raw binary PDF bytes before calling Playwright or OpenAI—returning instant cache hits (`<5ms`) and saving embedding tokens[cite: 10].
* **In-Memory FAISS Singleton Cache:** Keeps the FAISS vector index cached in RAM (`_vectorstore_cache`) while persisting to disk (`index.faiss` + `index.pkl`), delivering **10x faster** similarity searches[cite: 4, 10].
* **Balanced Multi-Source Retrieval:** Combines global top-$k$ vector similarity with per-source top-$k$ boosting so small 2-page PDFs are never drowned out by 200-chunk websites[cite: 10].
* **Live Website Force-Refresh (`🔄`) & Bulk Deletion (`🗑️`):** Re-scrape live websites with one click or delete single/multiple sources to purge vectors from FAISS and records from SQLite simultaneously[cite: 10].

### 4. Live Browser Telemetry Monitor & Interactive Mini-Game
* **Real-Time Playwright Visual Feed:** Streams live base64 browser snapshots (`page.screenshot()`), step logs, and a glowing cyan laser-scan progress bar every 650ms while scraping[cite: 1, 10].
* **Built-In Mini-Game ("Catch the Data Orbs"):** An interactive arcade tab inside the monitor where users can catch glowing data orbs and beat their high score while large websites or PDF batches are being indexed[cite: 1, 10].

### 5. Indian Multilingual Voice & Text AI + Actionable Exports
* **6 Indian Languages + Auto-Match:** Chat in **English, Tamil (தமிழ்), Malayalam (മലയാളം), Hindi (हिन्दी), Telugu (తెలుగు), and Kannada (ಕನ್ನಡ)**[cite: 10].
* **Voice Input (`🎙️`) & Read-Aloud (`🔊`):** Built-in **Web Speech API (`SpeechRecognition`)** for regional voice dictation and **Text-to-Speech (`SpeechSynthesis`)** on every AI response[cite: 3, 10].
* **One-Click CSV Table Export (`📥 Download CSV`):** Every Markdown table rendered by the AI includes an instant export button that downloads an Excel-compatible UTF-8 `.csv` file[cite: 3, 10].
* **Fluid Spring-Physics Chatbox & Local Persistence:** Smoothly morphs between a floating corner widget and a centered maximized modal, with `localStorage` chat persistence and a one-click **`🧹 Clear`** button[cite: 10].

---

## 🏗️ System Architecture

```text
+-----------------------------------------------------------------------------------+
|                        FRONTEND: REACT 19 + VITE (GLASSMORPHIC UI)                |
|  • 01 Add Website (URL)   • 02 Batch PDF Upload   • 03 Saved Library (Refresh/Del)|
|  • Live Playwright Telemetry Monitor + "Catch the Data Orbs" Mini-Game            |
|  • Fluid Spring-Physics Chatbox: Voice Mic + Read-Aloud TTS + 1-Click CSV Export  |
+-----------------------------------------+-----------------------------------------+
                                          | REST API (Axios)
                                          v
+-----------------------------------------------------------------------------------+
|                         BACKEND: PYTHON FASTAPI + ASYNCIO                         |
|                                                                                   |
|  [A] INPUT GUARDRAILS & SHA-256 DEDUPLICATION REGISTRY (SQLite)                   |
|      ├── Prompt-Injection Filter, Length Cap, HttpUrl & %PDF Magic-Byte Checks    |
|      ├── Strips ad-tracking URL noise & hashes URLs / binary PDF bytes            |
|      └── Instant cache hit (<5ms) if already indexed; supports Force-Refresh      |
|                                                                                   |
|  [B] FAULT-TOLERANT DUAL INGESTION ENGINE                                         |
|      ├── Playwright Stealth Scraper: 18s timeout guard, auto-scrolls lazy DOMs,   |
|      │   streams live JPEG snapshots, extracts HTML tables/e-commerce grids,      |
|      │   auto-downloads linked PDFs, & falls back to HTTP on bot-blocked sites.   |
|      └── PyMuPDF (fitz) Engine: Batch-extracts text + structured PDF tables.      |
|                                                                                   |
|  [C] BALANCED MULTI-SOURCE RAG PIPELINE (LangChain + FAISS + OpenAI)              |
|      ├── Chunking: RecursiveCharacterTextSplitter (1200 chars / 200 overlap)      |
|      ├── Vector Store: In-Memory Cached + Disk-Persisted FAISS Index              |
|      │   (Global Similarity + Per-Source Top-K Boost so small PDFs aren't buried) |
|      └── LLM Generation: OpenAI gpt-4o-mini with Unicode Script Language Lock     |
+-----------------------------------------------------------------------------------+
```

---

## 📂 Project Directory Structure

```text
universal-rag-assistant/
├── backend/
│   ├── app/
│   │   ├── api/
│   │   │   ├── __init__.py
│   │   │   ├── routes_chat.py        # /api/chat, /api/sources, & Delete endpoints
│   │   │   └── routes_ingest.py      # /api/ingest/url, /api/ingest/pdf, & /status
│   │   ├── services/
│   │   │   ├── __init__.py
│   │   │   ├── llm_service.py        # Guardrails, Unicode language lock & OpenAI RAG
│   │   │   ├── pdf_service.py        # PyMuPDF text & Markdown table extraction
│   │   │   ├── registry.py           # SQLite SHA-256 deduplication & URL cleaner
│   │   │   ├── scraper_service.py    # Playwright scraper + Live snapshots + Fallback
│   │   │   └── vector_service.py     # In-memory FAISS cache, merge, delete & search
│   │   ├── __init__.py
│   │   ├── config.py                 # Environment & storage path configuration
│   │   └── main.py                   # FastAPI entry point & CORS middleware
│   ├── storage/                      # Local FAISS index & SQLite DB (Git-ignored)
│   ├── requirements.txt              # Python dependencies (Python 3.13 compatible)
│   └── .env                          # OpenAI API Key (Excluded via .gitignore)
│
├── frontend/
│   ├── src/
│   │   ├── App.jsx                   # Main UI, Live Scanner, Mini-Game, Voice & CSV
│   │   ├── index.css                 # Glassmorphic theme, Grid locks & Laser styles
│   │   └── main.jsx                  # React DOM entry
│   ├── index.html
│   ├── package.json
│   └── vite.config.js
│
├── .gitignore
└── README.md
```

---

## ⚙️ Prerequisites

* **Python:** `3.10+` (Fully tested with **Python 3.13** on Windows)[cite: 4, 10]
* **Node.js & npm:** `v18+`
* **OpenAI API Key:** Valid key with access to `gpt-4o-mini` and `text-embedding-3-small`[cite: 10]

---

## 🛠️ Local Setup & Installation (Windows Command Prompt)

### Step 1: Clone the Repository
```cmd
git clone https://github.com/vigneshnanu90/Buildathon-Smart-Knowledge-Hub-RAG-App.git
cd Buildathon-Smart-Knowledge-Hub-RAG-App
```

### Step 2: Backend Setup (FastAPI + Playwright + FAISS)
Open a **Command Prompt (`cmd`)** terminal:

```cmd
cd backend
python -m venv venv
venv\Scripts\activate

:: Upgrade pip and install dependencies
python -m pip install --upgrade pip wheel setuptools
pip install -r requirements.txt

:: Install Headless Chromium for Playwright
playwright install chromium
```

Create a **`.env`** file inside the `backend/` folder:
```env
OPENAI_API_KEY=sk-proj-your_actual_openai_api_key_here
```

Start the FastAPI backend server:
```cmd
python -m uvicorn app.main:app --reload --port 8000
```
* **Backend Health Check:** `http://127.0.0.1:8000`
* **Interactive Swagger API Docs:** `http://127.0.0.1:8000/docs`

---

### Step 3: Frontend Setup (React 19 + Vite)
Open a **second Command Prompt (`cmd`)** terminal:

```cmd
cd frontend
npm install
npm run dev
```
* **Frontend Application URL:** `http://localhost:5173`

---

## 🔌 REST API Endpoints Summary

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `GET` | `/` | Health check and API status |
| `POST` | `/api/ingest/url` | Normalizes URL, scrapes via Playwright, deduplicates, and indexes into FAISS |
| `POST` | `/api/ingest/pdf` | Extracts text & tables from uploaded PDF(s) and indexes into FAISS |
| `GET` | `/api/ingest/status` | Streams real-time Playwright screenshot, step log, mode, and progress % |
| `GET` | `/api/sources` | Lists all deduplicated websites and PDFs stored in SQLite & FAISS |
| `POST` | `/api/sources/delete` | Bulk-deletes selected `source_hashes` from both FAISS and SQLite |
| `DELETE` | `/api/sources/{hash}` | Deletes a single source by hash from both FAISS and SQLite |
| `POST` | `/api/chat` | Runs input guardrails, queries cached FAISS, and returns multilingual answers |

---
