# Cancer Awareness Assistant - Beginner's Guide

## 📋 Project Overview

This is an **AI-powered educational chatbot** that answers questions about cancer awareness by searching through hospital documents. It's designed as a prototype for engineering purposes and uses modern AI technologies to provide accurate, source-cited answers.

### ⚠️ Important Disclaimer
**This is NOT a diagnostic system and NOT clinically validated.** It provides educational information only and explicitly refuses to diagnose, rule out cancer, or recommend treatments. Always consult qualified healthcare professionals for medical decisions.

---

## 🎯 Project Agenda

### What This Project Does
1. **Answers questions from hospital documents** - Searches PDF, DOCX, and TXT files for relevant information
2. **Provides source citations** - Shows exactly which document and page each answer came from
3. **Safety features** - Refuses to diagnose or recommend treatments, returns urgent-care messages for emergency phrases
4. **Falls back to web search** - When documents don't contain the answer, searches the web (labeled as not from hospital documents)
5. **Stores conversation history** - Keeps chat logs in Supabase database
6. **Voice input support** - Allows users to speak questions instead of typing

### How It Works
```
User Question → Check Urgent Patterns → Check Hospital Profile → Search Documents
                                        ↓                      ↓
                                   Return Urgent Message  If relevant passage found → Answer with citations
                                                              ↓
                                                         If no relevant passage → Web search or general answer
```

---

## 📁 File-by-File Explanation

### Core Application Files

#### **1. app.py** - The Web Interface
**What it does:**
- Creates the Streamlit web interface (the chat UI you see in the browser)
- Handles user input (text and voice)
- Displays chat messages and hospital profile information
- Manages session state (chat history, hospital profile)
- Connects to the RAG system for answering questions

**Why it's used:**
- Streamlit is chosen because it's easy to build Python web apps without knowing HTML/CSS/JavaScript
- Provides built-in chat components and audio input
- Perfect for rapid prototyping

**Key functions:**
- `render_hospital()` - Displays hospital branding (name, colors, fonts, logo)
- `ask()` - Processes user questions and displays answers
- `persist_message()` - Saves chat to Supabase database

---

#### **2. rag.py** - The "Brain" (Retrieval-Augmented Generation)
**What it does:**
- Searches documents using vector embeddings (semantic search)
- Decides whether to answer from documents or fall back to web search
- Validates answers to ensure they're supported by evidence
- Handles voice transcription using Whisper
- Enforces safety rules (no diagnosis, no treatment recommendations)

**Why it's used:**
- RAG (Retrieval-Augmented Generation) is the standard pattern for building document-based AI assistants
- Ensures answers are grounded in actual documents, not hallucinated
- Vector embeddings allow semantic search (finds meaning, not just keyword matches)

**Key concepts:**
- **Embeddings**: Converts text to numbers that represent meaning. Similar texts have similar numbers.
- **ChromaDB**: Stores document chunks and their embeddings for fast retrieval
- **SentenceTransformer**: The model that creates embeddings (BAAI/bge-small-en-v1.5)
- **Groq API**: Fast AI inference for generating answers (uses OpenAI-compatible API)

**Key functions:**
- `retrieve()` - Searches ChromaDB for relevant document passages
- `answer_question()` - Main logic: checks urgent patterns, profile, documents, then web
- `validate_answer()` - Ensures AI answer is supported by document evidence
- `transcribe_speech()` - Converts audio to text using Whisper

---

#### **3. ingest.py** - Document Processing Pipeline
**What it does:**
- Reads documents from the `documents/` folder
- Extracts text from PDF, DOCX, and TXT files
- Chunks text into smaller pieces (for better search)
- Creates embeddings for each chunk
- Stores everything in ChromaDB (local vector database)
- Uploads documents to Supabase Storage
- Stores extracted text in Supabase database

**Why it's used:**
- Documents are too large to search as whole files
- Chunking makes search more precise (smaller, focused pieces)
- OCR (Optical Character Recognition) handles image-only PDFs
- Metadata (approval status, version) tracks document authority

**Key technologies:**
- **PyMuPDF**: Reads PDF files, handles text extraction and OCR
- **python-docx**: Reads Word documents (.docx)
- **RapidOCR**: OCR engine for image-based PDFs
- **SentenceTransformer**: Creates embeddings for chunks

**Process flow:**
```
Document Files → Extract Text → Split into Chunks → Create Embeddings → Store in ChromaDB
                                              ↓
                                   Upload to Supabase Storage & Database
```

---

#### **4. supabase_store.py** - Database Integration
**What it does:**
- Manages connection to Supabase (cloud database)
- Stores hospital profile (name, address, branding)
- Stores chat history (questions and answers)
- Stores document files in Supabase Storage
- Stores extracted document text in database

**Why it's used:**
- Supabase provides a managed PostgreSQL database with authentication
- Cloud storage keeps documents accessible from anywhere
- Chat history persists across sessions
- Free tier available for development

**Key functions:**
- `load_hospital_profile()` - Retrieves hospital information
- `save_chat_message()` - Stores each chat turn
- `load_chat_messages()` - Retrieves conversation history
- `upload_documents()` - Uploads files to Supabase Storage
- `replace_document_chunks()` - Stores extracted text chunks

---

#### **5. build_samples.py** - Sample Document Generator
**What it does:**
- Creates synthetic sample documents (DOCX and PDF)
- Used to generate test data for the prototype
- Not part of the runtime application

**Why it's used:**
- Provides realistic sample documents for testing
- Demonstrates the document processing pipeline
- Allows testing without real medical documents

---

### Configuration Files

#### **6. requirements.txt** - Python Dependencies
**What it does:**
- Lists all Python packages needed to run the project
- Used by `pip install -r requirements.txt` to install everything

**Each dependency explained:**
- `streamlit` - Web framework for the UI
- `chromadb` - Vector database for storing embeddings
- `sentence-transformers` - Creates text embeddings
- `pymupdf` - PDF reading and text extraction
- `rapidocr-onnxruntime` - OCR for image-based PDFs
- `python-docx` - Word document reading
- `python-dotenv` - Loads environment variables from .env
- `openai` - OpenAI API client (used with Groq)
- `supabase` - Supabase Python client

---

#### **7. .env.example** - Environment Variables Template
**What it does:**
- Shows what environment variables are needed
- User copies this to `.env` and fills in actual values
- **Never commit `.env` to git** (it's in .gitignore for security)

**Variables needed:**
- `SUPABASE_URL` - Your Supabase project URL
- `SUPABASE_KEY` - Your Supabase API key
- `OPENAI_API_KEY` - OpenAI API key (or Groq key)

---

#### **8. .gitignore** - Git Exclusions
**What it does:**
- Tells git which files/folders to ignore
- Prevents committing sensitive data (like .env)
- Excludes Python cache files, virtual environments, etc.

---

### Database Files

#### **9. sql/schema.sql** - Database Schema
**What it does:**
- Defines the database structure (tables, columns, policies)
- Must be run in Supabase SQL editor before first use
- Creates tables for hospital profile, chat messages, and document chunks

**Tables created:**
- `hospital_profile` - Hospital information (name, address, branding)
- `chat_messages` - Conversation history
- `document_chunks` - Extracted text from documents

---

### Document Folder

#### **10. documents/** - Source Documents
**What it does:**
- Contains all PDF, DOCX, and TXT files to be searched
- Sample documents included for testing
- You can add your own documents here

**Sample files:**
- `hospital.txt` - Hospital profile information
- `screening_basics.docx` - Information about cancer screening
- `symptoms_do_not_prove_cancer.pdf` - Educational content about symptoms
- `warning_signs.txt` - Warning signs information

---

### Other Folders

#### **11. chroma_db/** - Local Vector Database
**What it does:**
- Created automatically by `ingest.py`
- Stores document embeddings locally
- Allows fast semantic search without internet
- Deleted and recreated each time you run `ingest.py`

---

#### **12. health_agent/** - Virtual Environment
**What it does:**
- Python virtual environment (created during setup)
- Isolates project dependencies from system Python
- Contains installed packages and Python interpreter

---

## 🔧 Technology Stack Explained

### Why These Technologies?

| Technology | Purpose | Why Chosen |
|------------|---------|------------|
| **Python** | Main programming language | Easy to learn, huge ML/AI ecosystem |
| **Streamlit** | Web UI framework | No HTML/CSS needed, fast prototyping |
| **ChromaDB** | Vector database | Lightweight, runs locally, fast search |
| **SentenceTransformers** | Text embeddings | State-of-the-art semantic search |
| **Supabase** | Cloud database | Free tier, PostgreSQL, easy auth |
| **Groq API** | AI inference | Fast, affordable, OpenAI-compatible |
| **PyMuPDF** | PDF handling | Robust PDF parsing, supports OCR |
| **Whisper** | Speech-to-text | Accurate voice transcription |

---

## 🚀 How to Use This Project

### Step 1: Setup Environment
```powershell
# Create virtual environment
py -3.12 -m venv .venv
.venv\Scripts\activate

# Install dependencies
python -m pip install --upgrade pip
python -m pip install torch --index-url https://download.pytorch.org/whl/cpu
python -m pip install -r requirements.txt
```

### Step 2: Configure Secrets
1. Copy `.env.example` to `.env`
2. Fill in your Supabase URL and key
3. Fill in your OpenAI/Groq API key

### Step 3: Setup Supabase
1. Create a free Supabase account
2. Create a new project
3. Run `sql/schema.sql` in the Supabase SQL editor
4. Copy your project URL and key to `.env`

### Step 4: Process Documents
```powershell
python ingest.py
```
This will:
- Read all files in `documents/`
- Extract text and create chunks
- Generate embeddings
- Store in ChromaDB
- Upload to Supabase

### Step 5: Run the App
```powershell
streamlit run app.py
```
This opens a web browser with the chat interface.

---

## 🔄 Data Flow Diagram

```
┌─────────────────┐
│  User Question  │
└────────┬────────┘
         │
         ▼
┌─────────────────────────────────────────────────────────┐
│                   Safety Checks                          │
│  - Urgent symptoms? → Return emergency message          │
│  - Diagnosis request? → Refuse with scope message        │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│              Hospital Profile Check                      │
│  - Question about hospital info?                        │
│  - Return answer from Supabase profile table            │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│              Document Search (RAG)                       │
│  - Convert question to embedding                        │
│  - Search ChromaDB for similar chunks                   │
│  - Check similarity threshold (0.62)                    │
│  - If relevant passages found → AI answer with sources │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
              No relevant passages found
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│              Fallback (Web or General)                   │
│  - Use web search if current info needed               │
│  - Otherwise answer from general knowledge              │
│  - Label as "not from hospital documents"               │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│              Answer Validation                          │
│  - Check citations match retrieved passages             │
│  - Verify answer supported by evidence                  │
│  - If invalid → Refuse or retry                         │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│              Return to User                             │
│  - Display answer with citations                        │
│  - Save to Supabase chat history                        │
└─────────────────────────────────────────────────────────┘
```

---

## 🛡️ Safety Features

### 1. Urgent Pattern Detection
The system checks for emergency phrases like:
- "chest pain"
- "difficulty breathing"
- "heart attack"
- "suicid"
- "severe bleeding"

If detected, returns: *"This may need urgent medical care. Contact emergency services or a clinician now."*

### 2. Scope Enforcement
Refuses to:
- Diagnose cancer
- Rule cancer out
- Recommend personal treatments
- Provide dosages

### 3. Citation Validation
- AI must cite sources from retrieved passages
- Cannot invent citations
- Answer must be supported by document evidence
- Otherwise, refuses or retries

### 4. Web Search Labeling
When using web search or general knowledge, clearly labels: *"This answer is not from the hospital documents."*

---

## 📊 Key Algorithms

### Semantic Search (Embeddings)
1. Convert document chunks to vector embeddings (numbers representing meaning)
2. Convert user question to embedding
3. Calculate cosine similarity between question and all chunks
4. Return top 6 most similar chunks (if above threshold 0.62)

### Answer Validation
1. Check if answer cites only retrieved passages
2. Verify cited sources actually exist in context
3. Check if answer content overlaps with document text (≥35%)
4. If validation fails, refuse to answer

### Chunking Strategy
- Chunk size: 400 tokens
- Overlap: 80 tokens
- Maximum chunk: 480 tokens
- Preserves section headings for context

---

## 🎓 Learning Outcomes

By studying this project, beginners will learn:

1. **RAG Architecture** - How to build document-based AI assistants
2. **Vector Databases** - Semantic search with embeddings
3. **Streamlit** - Building Python web apps without frontend skills
4. **Supabase** - Cloud database integration
5. **Document Processing** - Extracting text from PDF, DOCX, TXT
6. **AI Safety** - Implementing guardrails for AI systems
7. **API Integration** - Using Groq/OpenAI APIs
8. **Environment Management** - Virtual environments and .env files

---

## 🔍 Common Questions

### Q: Why use ChromaDB instead of just searching text?
A: ChromaDB uses semantic search (meaning-based) instead of keyword search. It can find relevant passages even if they don't contain the exact words from the question.

### Q: Why chunk documents instead of searching whole files?
A: Whole files are too large and contain mixed topics. Chunking allows precise search for specific information and makes embeddings more accurate.

### Q: Why is similarity threshold 0.62?
A: This was calibrated on the sample documents. Passages above this threshold are relevant; below are unrelated. Threshold may need adjustment for different document sets.

### Q: Why validate AI answers?
A: AI models can hallucinate (make things up). Validation ensures answers are actually supported by the documents and cites real sources.

### Q: Can I use this for real patients?
A: NO. This is an engineering prototype with synthetic samples. Real medical use requires clinical validation, security audits, HIPAA compliance, and legal review.

---

## 🚀 Next Steps for Beginners

1. **Run the app** - Follow the setup instructions and try it out
2. **Add your own documents** - Put PDF/DOCX files in `documents/` and re-run `ingest.py`
3. **Experiment with questions** - Try different types of questions to see how the system responds
4. **Modify the code** - Change the similarity threshold, add new safety patterns, or customize the UI
5. **Read the code** - Start with `app.py`, then `rag.py` to understand the flow
6. **Explore ChromaDB** - Look at what's stored in `chroma_db/` after running `ingest.py`

---

## 📚 Additional Resources

- [Streamlit Documentation](https://docs.streamlit.io/)
- [ChromaDB Documentation](https://docs.trychroma.com/)
- [SentenceTransformers](https://www.sbert.net/)
- [Supabase Documentation](https://supabase.com/docs)
- [RAG Tutorial](https://www.pinecone.io/learn/series/rag/)

---

## 📝 Summary

This project demonstrates a complete RAG (Retrieval-Augmented Generation) system for document-based AI assistance. It combines:
- Modern AI technologies (embeddings, LLMs)
- Practical engineering (document processing, validation)
- Safety considerations (guardrails, scope limits)
- Cloud integration (Supabase)

Perfect for learning how to build real-world AI applications that are grounded in actual data rather than relying solely on AI model knowledge.
