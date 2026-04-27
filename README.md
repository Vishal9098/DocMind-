# 📄 DocMind — Local Document Q&A System

> Ask questions about any PDF — answers grounded **only** in the document. No external APIs, no internet required.

![Python](https://img.shields.io/badge/Python-3.10+-blue?style=flat-square)
![FastAPI](https://img.shields.io/badge/FastAPI-0.111-green?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-lightgrey?style=flat-square)

---

## 🧠 How It Works

```
PDF Upload → Text Extraction → Chunking → Embedding → FAISS Index
                                                            ↓
User Question → Embed Question → Similarity Search → Top-K Chunks → Extract Answer
```

### Architecture at a Glance

| Layer | Tool Used | Why |
|---|---|---|
| **Text Extraction** | `pdfplumber` | Reliable text + table extraction from PDFs |
| **Chunking** | Custom sliding window | Overlapping 300-word chunks preserve context |
| **Embeddings** | `sentence-transformers/all-MiniLM-L6-v2` | Fast, ~80MB, runs on CPU |
| **Vector Store** | `FAISS (IndexFlatIP)` | In-memory cosine similarity search |
| **Answer Extraction** | Keyword-overlap scoring | Extractive QA — no hallucination risk |
| **API** | `FastAPI` | Async, OpenAPI docs auto-generated |
| **Frontend** | Vanilla HTML/CSS/JS | Zero dependencies, ships inside FastAPI |

---

## 📦 Setup

### 1. Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/doc-qa-system.git
cd doc-qa-system
```

### 2. Create Virtual Environment

```bash
python -m venv venv

# Linux / macOS
source venv/bin/activate

# Windows
venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r backend/requirements.txt
```

> First run downloads the `all-MiniLM-L6-v2` model (~80 MB) from HuggingFace automatically.

### 4. Run the Server

```bash
cd backend
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

### 5. Open in Browser

```
http://localhost:8000
```

---

## 🚀 Usage

1. **Upload** — Drag & drop a PDF (or click to browse). The system extracts text, splits it into chunks, and builds a vector index.
2. **Ask** — Type any question in the chat box and press Enter.
3. **Explore** — Click "Show source passages" under any answer to see which parts of the document were used.

---

## 🔌 API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/upload` | Upload and index a PDF |
| `POST` | `/ask` | Ask a question about an indexed PDF |
| `GET`  | `/documents` | List all loaded documents |
| `DELETE` | `/documents/{doc_id}` | Remove a document |
| `GET`  | `/health` | Server health check |

Auto-generated docs available at: `http://localhost:8000/docs`

---

## 📁 Project Structure

```
doc-qa-system/
├── backend/
│   ├── main.py              # FastAPI app — upload, index, query
│   └── requirements.txt     # Python dependencies
├── frontend/
│   └── index.html           # Single-file UI (served by FastAPI)
└── README.md
```

---

## ⚙️ Configuration

You can tune these constants in `backend/main.py`:

```python
CHUNK_SIZE    = 300   # words per chunk
CHUNK_OVERLAP = 50    # word overlap between chunks
TOP_K         = 5     # number of chunks retrieved per query
```

---

## 🛠️ Tech Decisions & Reasoning

### Why `all-MiniLM-L6-v2`?
- Only ~80 MB, fast on CPU (no GPU needed)
- Great semantic search quality for general text
- Pre-trained on 1B+ sentence pairs

### Why FAISS over a full vector DB?
- In-memory, zero setup — perfect for a local tool
- `IndexFlatIP` with L2-normalised vectors = exact cosine similarity
- No persistence needed for session-based Q&A

### Why extractive QA instead of a generative model?
- Zero hallucination — answers come directly from the document
- No large LLM download (no 4GB GGUF file)
- Answers are always traceable to source text

---

## 🧪 Running Tests (optional)

```bash
pip install httpx pytest
pytest backend/test_main.py
```

---

## 📋 Requirements

- Python 3.10+
- 200 MB disk space (model cache)
- 512 MB RAM minimum (1 GB recommended for large PDFs)

---

## 📄 License

MIT — free to use, modify, and distribute.
