# 🔮 EmbedFlow-AI — AI Knowledge Assistant

A production-grade **Retrieval-Augmented Generation (RAG)** chatbot with a modular architecture. Ask questions about your private documents and get accurate, cited answers powered by Claude, OpenAI, or Gemini.

---

## Architecture

```
<img width="1536" height="1024" alt="EmbedFlow Architecture" src="https://github.com/user-attachments/assets/b62281bb-1c91-4b2f-bd4c-a50875a6b3aa" />


```

## Project Structure

```
rag_chatbot/
├── backend/
│   ├── main.py              # FastAPI app + REST endpoints
│   ├── rag_engine.py        # Document ingestion, embedding, FAISS retrieval
│   └── llm_provider.py      # Unified LLM interface (Anthropic/OpenAI/Gemini)
├── knowledge_base/
│   ├── getting_started.md
│   ├── technical_architecture.md
│   └── faq.md
├── frontend/
│   └── index.html           # Standalone interactive chat UI
├── vector_store/            # Auto-created: FAISS index + chunk metadata
├── requirements.txt
└── README.md
```

## Quick Start

### 1. Install dependencies
```bash
pip install -r requirements.txt
```

### 2. Set your API key
```bash
# Anthropic (Claude) — default
export ANTHROPIC_API_KEY=sk-ant-...

# OR OpenAI
export LLM_PROVIDER=openai
export OPENAI_API_KEY=sk-...

# OR Gemini
export LLM_PROVIDER=gemini
export GEMINI_API_KEY=AI...

# OR Local Ollama
export LLM_PROVIDER=ollama
export LLM_MODEL=llama3
```

### 3. Start the backend
```bash
cd backend
python main.py
# Server runs at http://localhost:8000
# API docs at http://localhost:8000/docs
```

### 4. Open the frontend
Open `frontend/index.html` in your browser — no server needed!
Click **Connect Backend** to link it to your running API.

---

## API Reference

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/health` | Health check, returns system stats |
| `POST` | `/chat` | Query the RAG pipeline |
| `POST` | `/ingest` | Upload and index a document |
| `GET` | `/documents` | List all indexed documents |
| `DELETE` | `/documents/{id}` | Remove a document from the index |

### POST /chat
```json
{
  "query": "What is RAG?",
  "history": [
    {"role": "user", "content": "..."},
    {"role": "assistant", "content": "..."}
  ],
  "top_k": 5,
  "temperature": 0.2,
  "max_tokens": 1024
}
```

Response:
```json
{
  "answer": "RAG stands for Retrieval-Augmented Generation...",
  "sources": [
    {
      "source": "getting_started.md",
      "text": "...",
      "score": 0.923,
      "chunk_index": 0
    }
  ],
  "model": "claude-sonnet-4-20250514",
  "provider": "anthropic",
  "input_tokens": 512,
  "output_tokens": 128
}
```

---

## Configuration (Environment Variables)

| Variable | Default | Description |
|----------|---------|-------------|
| `LLM_PROVIDER` | `anthropic` | `anthropic`, `openai`, `gemini`, `ollama` |
| `LLM_API_KEY` | — | Override for any provider's API key |
| `LLM_MODEL` | Provider default | Override model name |
| `LLM_BASE_URL` | — | Custom base URL (Ollama, proxy, etc.) |
| `KNOWLEDGE_DIR` | `../knowledge_base` | Directory of documents to auto-ingest |
| `PERSIST_DIR` | `../vector_store` | Where FAISS index is saved |
| `TOP_K` | `5` | Number of chunks to retrieve per query |
| `MAX_TOKENS` | `1024` | Max LLM output tokens |
| `TEMPERATURE` | `0.2` | LLM sampling temperature |

---

## Adding Custom Documents

**Method 1 — Auto-ingest on startup:**
Place `.md` or `.txt` files in the `knowledge_base/` directory. They'll be indexed automatically when the server starts.

**Method 2 — Live upload via API:**
```bash
curl -X POST http://localhost:8000/ingest \
  -F "file=@my_docs/company_handbook.md"
```

**Method 3 — Drag & drop in the UI:**
Drag files onto the chat interface.

---

## Using Local LLMs (Ollama)

```bash
# Install and start Ollama
ollama serve
ollama pull llama3

# Start backend with Ollama provider
LLM_PROVIDER=ollama LLM_MODEL=llama3 python backend/main.py
```

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Embeddings | SentenceTransformers `all-MiniLM-L6-v2` |
| Vector DB | FAISS (IndexFlatIP, cosine similarity) |
| Backend | FastAPI + Uvicorn |
| LLM | Anthropic Claude / OpenAI / Gemini / Ollama |
| Frontend | Vanilla JS + HTML/CSS (no build step) |
