# NyayaFlow – AI-Powered Legal Intelligence & Case Management Platform

[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16%20%2B%20pgvector-blue.svg)](https://www.postgresql.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-Python%203.10-green.svg)](https://fastapi.tiangolo.com/)
[![Express](https://img.shields.io/badge/Node.js-Express%20v4-black.svg)](https://expressjs.com/)
[![React](https://img.shields.io/badge/Frontend-React%2018%20%2B%20Vite%20%2B%20Tailwind-61DAFB.svg)](https://reactjs.org/)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)

**NyayaFlow** is a commercial-grade, end-to-end AI-powered legal intelligence and case management platform built for legal chambers, corporate legal departments, and individual practitioners. It combines a rigorous 12-table relational database normalized from **1NF through 5NF** in PostgreSQL with private, local **Retrieval-Augmented Generation (RAG)** using dense vector search (`pgvector`), PyMuPDF document extraction, and local LLMs (Qwen2.5 / Llama3.2 via Ollama).

The platform requires **zero paid cloud APIs** (no OpenAI, Anthropic, or cloud billing) and runs entirely on local infrastructure with strict privacy guarantees for confidential legal documents.

---

## 🏛️ System Architecture

```
                             +----------------------------------------+
                             |           React Frontend (SPA)         |
                             |  (Vite + Tailwind CSS + Lucide Icons)  |
                             +-------------------+--------------------+
                                                 |
                                       REST APIs / JWT Auth
                                                 |
                                                 v
                             +----------------------------------------+
                             |          Express.js Backend            |
                             |  - Authentication & RBAC (Lawyer/User) |
                             |  - Case & Document Management (CRUD)   |
                             |  - Audit Activity Logging              |
                             |  - HTTP Gateway to AI Service          |
                             +--------+----------------------+--------+
                                      |                      |
                              Pool / pg Queries         HTTP REST APIs
                                      |                      |
                                      v                      v
          +-----------------------------+     +-------------------------------+
          |   PostgreSQL 16 + pgvector  |     |      FastAPI AI Service       |
          |  - Relational Normalized DB |<----+  - PyMuPDF Text Extraction    |
          |  - 12 Core Tables           |     |  - Tesseract OCR Fallback     |
          |  - HNSW Vector Indexing     |     |  - Sentence-Transformers      |
          |  - ACID Transactions        |     |  - Ollama LLM RAG Pipeline    |
          +-----------------------------+     +---------------+---------------+
                                                              |
                                                     Local Model Inference
                                                              |
                                                              v
                                              +-------------------------------+
                                              |       Local Ollama Daemon     |
                                              |  - Model: Qwen2.5 / llama3    |
                                              |  - Port 11434 (Host / Docker) |
                                              +-------------------------------+
```

---

## ⚡ Key Features

### 1. Relational Database Design (1NF to 5NF)
- **12 Interconnected Normalized Tables**: `users`, `cases`, `case_members`, `parties`, `case_parties`, `legal_documents`, `document_chunks`, `extracted_facts`, `chats`, `messages`, `hearings`, `activity_logs`.
- **Elimination of Anomalies**: Eliminates insertion, update, and deletion anomalies through formal functional dependency and multivalued dependency analysis.
- **ACID Transactions & Foreign Key Cascades**: Guarantees referential integrity across all case filings, hearing schedules, and audit entries.
- **Detailed Specification**: Complete entity descriptions, candidate keys, and mathematical proofs available in [`DATABASE_DESIGN.md`](./DATABASE_DESIGN.md).

### 2. Legal Document Management & OCR Ingestion
- Upload and organize petitions, orders, judgments, affidavits, and contracts.
- **PyMuPDF (`fitz`) Text Extraction**: Preserves page numbers and formatting.
- **Tesseract OCR Fallback**: Automatically processes scanned PDFs and image-based orders with negligible native text layers.
- **Secure Local Storage**: Files are stored on a persistent volume with SHA-256 cryptographic checksums; documents are never exposed via public URLs.

### 3. Automated Legal Fact Extraction
- Heuristic pattern matching and NLP entity extraction for:
  - **Petitioner / Claimant**
  - **Respondent / Defendant**
  - **Appearing Advocates & Counsel**
  - **Presiding Judicial Forum & Bench**
  - **Case & FIR Identifiers**
  - **Referenced Legal Sections** (e.g. Section 66 IT Act, Section 420 IPC, Article 226)
  - **Interim Orders & Judicial Directives**
- Retains exact source page number and excerpt.
- **Lawyer Verification Portal**: Enables attorneys to review, correct, and verify facts.

### 4. AI Legal Document Summarization
- One-click structured summaries identifying Case Header, Forum, Parties, Facts, Issues, Arguments, Directives, and Next Steps.
- **Side-by-Side Split Reader**: Inspect the original PDF alongside the generated AI summary and verified facts.
- **Anti-Hallucination Indicator**: Clearly distinguishes extracted facts from missing particulars.

### 5. Grounded AI Legal Co-Counsel (RAG)
- Conversational interface with case-level and document-level grounding.
- **Dense Vector Search**: Generates 384-dimensional embeddings using `sentence-transformers/all-MiniLM-L6-v2` stored in PostgreSQL with HNSW vector indices (`vector_cosine_ops`).
- **Verifiable Citations**: Every answer displays the document name, page number, confidence similarity score, and verbatim excerpt.
- **Honest Bounds**: Explicitly informs the user if case filings do not contain sufficient facts to answer a prompt.

### 6. Case & Hearing Timeline Management
- Chronological judicial agenda with courtroom, presiding judge, hearing purpose, and outcome notes.
- Automatically calculates the next active hearing date across case dockets.

---

## 🛠️ Technology Stack

| Layer | Technologies |
| :--- | :--- |
| **Frontend** | React 18, Vite, Tailwind CSS, Lucide React icons, Recharts, React Router v6, Axios |
| **Backend API** | Node.js 20+, Express.js, JWT, bcryptjs, PostgreSQL `pg` pool, Multer, Helmet, Morgan |
| **AI Microservice** | Python 3.10, FastAPI, Uvicorn, PyMuPDF, PyTesseract, Sentence-Transformers, PyTorch |
| **Vector & Relational DB** | PostgreSQL 16, `pgvector` extension (HNSW index, 384 dimensions) |
| **Local LLM** | Ollama (`qwen2.5:latest` or `llama3.2:1b`) |
| **Containerization** | Docker, Docker Compose, Multi-stage builds |

---

## 👥 Demo Accounts (Pre-Seeded)

The database includes synthetic legal cases and pre-seeded test accounts for instant evaluation:

| Role | Email Address | Password | Enrolment ID / Organization |
| :--- | :--- | :--- | :--- |
| **Lawyer (Lead Counsel)** | `advocate.sharma@nyayaflow.com` | `Password@123` | D/1482/2012 (Sharma & Associates) |
| **Lawyer (Corporate Counsel)** | `advocate.menon@nyayaflow.com` | `Password@123` | K/0912/2015 (Menon Corporate Law) |
| **Administrator** | `admin@nyayaflow.com` | `Password@123` | D/ADMIN/2005 (NyayaFlow Global HQ) |
| **Client (Observer)** | `client.verma@nyayaflow.com` | `Password@123` | Anand Tech Solutions Pvt Ltd |

---

## 🚀 Quick Start with Docker Compose

Ensure Docker and Docker Compose are installed on your host machine.

### 1. Clone the repository and configure environment
```bash
cp .env.example .env
```

### 2. Start Ollama (on macOS host)
If running on macOS, launch Ollama natively so Docker containers can communicate via `host.docker.internal:11434`:
```bash
# Pull recommended lightweight model
ollama run qwen2.5:latest
```
*(Alternative ultra-lightweight model: `ollama run llama3.2:1b`)*

### 3. Launch the complete application suite
```bash
docker compose up --build
```

### 4. Access the Platform
- **Frontend Web Application**: [http://localhost:3000](http://localhost:3000)
- **Express Backend API**: [http://localhost:5001](http://localhost:5001)
- **FastAPI AI Microservice & Swagger Docs**: [http://localhost:8000/docs](http://localhost:8000/docs)
- **PostgreSQL Database**: `localhost:5432` (`db: nyayaflow_db`, `user: postgres`)

---

## 💻 Native Local Development Setup (Without Docker)

### Prerequisites
- Node.js v20+ & npm
- Python 3.9+ & pip
- PostgreSQL 16 with `pgvector` extension installed (`brew install pgvector` or Docker)

### 1. Database Initialization
```bash
# Create database
psql -U postgres -c "CREATE DATABASE nyayaflow_db;"

# Run migrations and seed data
cd backend
npm install
npm run migrate
npm run seed
```

### 2. Start Express Backend
```bash
cd backend
npm run dev
# Running on http://localhost:5001
```

### 3. Start Python FastAPI AI Service
```bash
cd ai-service
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --port 8000 --reload
# Running on http://localhost:8000
```

### 4. Start React Frontend
```bash
cd frontend
npm install
npm run dev
# Running on http://localhost:3000
```

---

## 🧪 Automated Testing

### Backend Unit & Security Tests
Verifies password hashing, JWT signing, role-based authorization, and candidate key validation:
```bash
cd backend
npm test
```

### AI Pipeline Tests
Verifies chunking page preservation, sliding overlap, 384-dimensional vector normalization, and legal fact extraction:
```bash
cd ai-service
pytest tests/
```

---

## 📚 Technical Documentation

- **[`DATABASE_DESIGN.md`](./DATABASE_DESIGN.md)**: Mathematical proofs for 1NF through 5NF, entity relationships, candidate keys, and anomaly elimination.
- **[`ARCHITECTURE.md`](./ARCHITECTURE.md)**: End-to-end RAG workflows, security boundaries, and service communication.
- **[`API_DOCUMENTATION.md`](./API_DOCUMENTATION.md)**: REST endpoints for auth, cases, documents, facts, hearings, chats, and AI analysis.
