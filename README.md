
OVERVIEW :
The system combines Large Language Models (LLMs) with autonomous agent frameworks (like LangChain, AutoGen, or LlamaIndex) and retrieval systems (RAG). It automates administrative tasks, answers course-related queries, tracks assignments, and offers personalized guidance to scale student support effectively.

KEY FEATURES :

* **Automated Q&A & Support:**
* Answers common queries about syllabus details, deadlines, campus facilities, and academic policies instantly.
* **Context-Aware Assistance (RAG):**
*  Uses Retrieval-Augmented Generation to fetch accurate context from university databases, course PDFs, and FAQs.
* **Autonomous Task & Calendar Agent:**
* Automatically sets study reminders, schedules dynamic study sessions, and updates assignment progress.
* **Personalized Learning Guidance:**
* Analyzes student performance to recommend targeted study materials, practice problems, or learning paths.
* **Multi-Modal Interaction:**
* Supports voice inputs, document uploads (e.g., grading feedback or assignments), and text chat.

---

🛠️ Technology Stack
UI Framework: Streamlit
Agent Orchestration: LangGraph, LangChain Core
Vector Database: ChromaDB
Embeddings: HuggingFace (sentence-transformers/all-MiniLM-L6-v2)
LLM Inference: Groq API (llama-3.3-70b-versatile) / Local Ollama (llama3.1)
Persistence: SQLite3

---

PROJECT STRUCTURE :

```text
ai-student-support-system/
│
├── agent/                  # Core AI Agent modules and logic
│   ├── __init__.py
│   ├── tools.py            # Agent tools (Calendar, RAG lookup, Search)
│   ├── prompts.py          # System prompts and persona definitions
│   └── workflow.py        # LangChain / LangGraph execution loops
│
├── data/                   # Knowledge base documents and vectors
│   ├── raw/                # PDF syllabi, FAQs, text guides
│   └── vectorstore/        # Saved ChromaDB / FAISS indices
│
├── app.py                  # Main frontend entry point (Streamlit / FastAPI)
├── requirements.txt        # Python dependencies
└── README.md               # Documentation and setup instructions

```
