# -AI-Student-Support-System
 AI-Driven Autonomous Student Support Assistant
📖 Overview
The AI-Driven Student Support Assistant is an autonomous, context-aware AI application designed to help university students instantly find accurate answers regarding institutional regulations, elective credits, and administrative notices.

Instead of relying on rigid, hardcoded rules, this system utilizes a LangGraph ReAct agent alongside a Retrieval-Augmented Generation (RAG) pipeline. It dynamically queries a local vector database of university PDFs only when necessary, while maintaining persistent, multi-turn conversational memory via a local SQLite database.

✨ Key Features
Agentic Reasoning: 
Powered by LangGraph's create_react_agent, the system autonomously decides whether to retrieve external documents or rely on conversational context to answer a query.
Persistent Stateful Memory: 
Utilizes LangGraph's SqliteSaver checkpointer tied to unique thread_id sessions. Conversations are saved across reloads without manual array passing or context-window overflow.
Source-Grounded RAG Pipeline: 
Embeds and chunks institutional PDFs using HuggingFace all-MiniLM-L6-v2 and ChromaDB, ensuring the AI only answers based on verified university documents.
Hybrid Cloud/Local Inference: Defaults to Groq's lightning-fast Llama 3.3 70B API for high-performance reasoning, with built-in codebase support to seamlessly swap to local, offline inference via Ollama.
Streamlit Interface: 
A clean, responsive chat interface featuring one-click Session ID generation for starting fresh conversation threads.
🛠️ Technology Stack

UI Framework: Streamlit
Agent Orchestration: LangGraph, LangChain Core
Vector Database: ChromaDB
Embeddings: HuggingFace (sentence-transformers/all-MiniLM-L6-v2)
LLM Inference: Groq API (llama-3.3-70b-versatile) / Local Ollama (llama3.1)
Persistence: SQLite3
📂 Project Structure

ai-student-support-agent/
│
├── data/                    # Drop your university PDF documents here
├── chroma_db/               # Auto-generated vector database directory
├── chat_history.db          # Auto-generated SQLite memory checkpointer
│
├── app.py                   # Streamlit UI and session management
├── agent.py                 # LangGraph ReAct agent and RAG pipeline logic
├── requirements.txt         # Python dependencies
├── .env                     # API keys (not tracked in git)
└── README.md                # Project documentation

🚀 Getting Started
1. Prerequisites
Python 3.10+

Git

Optional: Ollama (if running locally without an API key)

2. Installation
Clone the repository and set up a virtual environment:

Bash
git clone [https://github.com/yourusername/ai-student-support-agent.git](https://github.com/yourusername/ai-student-support-agent.git)
cd ai-student-support-agent

# Create and activate virtual environment
python -m venv venv
# On Windows: venv\Scripts\activate
# On Mac/Linux: source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
3. Environment Setup
Create a .env file in the root directory and add your Groq API key:

Code snippet
GROQ_API_KEY=your_groq_api_key_here
(You can obtain a free API key from the Groq Cloud Console.)

4. Add Knowledge Base
Drag and drop your university's PDF documents (FAQs, syllabi, rulebooks) into the /data folder. The system will automatically chunk and vectorize them on the first run.

🧠 Usage
Start the Streamlit application:

Bash
streamlit run app.py
Chat: Ask questions like "What is the penalty for arriving late to the hostel?" or "Who approves Form-11B?".

Memory: The AI remembers previous messages in the current session.

New Session: Click "Start New Conversation" in the sidebar to generate a new Session ID and clear the active context.

🤝 Contributing
Feel free to open issues or submit pull requests for future enhancements (e.g., migrating to PostgreSQL for multi-tenant scalability, or adding live web-scraping tools).

📝 License
This project is licensed under the MIT License.
