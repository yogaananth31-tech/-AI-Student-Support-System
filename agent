 -1 +1,84 @@
# AI-Student-Support-System
# 🎓 AI-Driven Autonomous Student Support Assistant
## 📖 Overview
The **AI-Driven Student Support Assistant** is an autonomous, context-aware AI application designed to help university students instantly find accurate answers regarding institutional regulations, elective credits, and administrative notices. 
Instead of relying on rigid, hardcoded rules, this system utilizes a **LangGraph ReAct agent** alongside a **Retrieval-Augmented Generation (RAG)** pipeline. It dynamically queries a local vector database of university PDFs only when necessary, while maintaining persistent, multi-turn conversational memory via a local SQLite database.
## ✨ Key Features
* **Agentic Reasoning:** Powered by LangGraph's `create_react_agent`, the system autonomously decides whether to retrieve external documents or rely on conversational context to answer a query.
* **Persistent Stateful Memory:** Utilizes LangGraph's `SqliteSaver` checkpointer tied to unique `thread_id` sessions. Conversations are saved across reloads without manual array passing or context-window overflow.
* **Source-Grounded RAG Pipeline:** Embeds and chunks institutional PDFs using HuggingFace `all-MiniLM-L6-v2` and ChromaDB, ensuring the AI only answers based on verified university documents.
* **Hybrid Cloud/Local Inference:** Defaults to Groq's lightning-fast **Llama 3.3 70B** API for high-performance reasoning, with built-in codebase support to seamlessly swap to local, offline inference via **Ollama**.
* **Streamlit Interface:** A clean, responsive chat interface featuring one-click Session ID generation for starting fresh conversation threads.
## 🛠️ Technology Stack
* **UI Framework:** Streamlit
* **Agent Orchestration:** LangGraph, LangChain Core
* **Vector Database:** ChromaDB
* **Embeddings:** HuggingFace (`sentence-transformers/all-MiniLM-L6-v2`)
* **LLM Inference:** Groq API (`llama-3.3-70b-versatile`) / Local Ollama (`llama3.1`)
* **Persistence:** SQLite3
## 📂 Project Structure
```text
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
‎agent.py‎
+65
Lines changed: 65 additions & 0 deletions
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,65 @@
import os
import sqlite3
import streamlit as st
from langchain_ollama import ChatOllama
from langchain_community.document_loaders import PyPDFLoader
from langchain_text_splitters import RecursiveCharacterTextSplitter
from langchain_huggingface import HuggingFaceEmbeddings
from langchain_community.vectorstores import Chroma
from langchain_core.tools import Tool
from langgraph.prebuilt import create_react_agent
from langgraph.checkpoint.sqlite import SqliteSaver
def get_llm():
    # ChatOllama connects to your local hardware; no API key needed
    return ChatOllama(
        model="llama3.1",
        temperature=0.3
    )
@st.cache_resource
def initialize_vector_store():
    docs = []
    data_dir = "./data"
    if os.path.exists(data_dir):
        for file in os.listdir(data_dir):
            if file.endswith(".pdf"):
                loader = PyPDFLoader(os.path.join(data_dir, file))
                docs.extend(loader.load())
    
    if not docs:
        return None
    text_splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=200)
    splits = text_splitter.split_documents(docs)
    embeddings = HuggingFaceEmbeddings(model_name="all-MiniLM-L6-v2")
    vectorstore = Chroma.from_documents(splits, embeddings, persist_directory="./chroma_db")
    return vectorstore.as_retriever(search_kwargs={"k": 3})
def get_agent_executor(thread_id: str = "default_thread"):
    retriever = initialize_vector_store()
    llm = get_llm()
    
    tools = []
    if retriever:
        retriever_tool = Tool(
            name="university_document_search",
            func=lambda q: "\n".join([d.page_content for d in retriever.invoke(q)]),
            description="Searches university regulations, syllabi, FAQs, and notices for accurate answers."
        )
        tools.append(retriever_tool)
    conn = sqlite3.connect("chat_history.db", check_same_thread=False)
    memory = SqliteSaver(conn)
    agent_executor = create_react_agent(
        model=llm,
        tools=tools,
        checkpointer=memory
    )
    
    return agent_executor
def create_agent(thread_id: str = "default_thread"):
    return get_agent_executor(thread_id)
‎app.py‎
+71
Lines changed: 71 additions & 0 deletions
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,71 @@
import streamlit as st
import uuid
from agent import create_agent
# Initialize page configuration
st.set_page_config(page_title="AI Student Support", page_icon="🎓")
st.title("🎓 AI Student Support Assistant")
# Initialize session state for LangGraph memory Thread ID
if "thread_id" not in st.session_state:
    st.session_state.thread_id = str(uuid.uuid4())
# Initialize session state for Streamlit UI message history
if "messages" not in st.session_state:
    st.session_state.messages = [
        {"role": "assistant", "content": "Hello! I am your AI Student Support Assistant. How can I help you today?"}
    ]
# Sidebar for session management
with st.sidebar:
    st.header("Session Management")
    st.write(f"**Current Session ID:**\n`{st.session_state.thread_id}`")
    
    # Button to generate a new thread ID (clears memory for a fresh start)
    if st.button("Start New Conversation"):
        st.session_state.thread_id = str(uuid.uuid4())
        st.session_state.messages = [
            {"role": "assistant", "content": "Hello! I am your AI Student Support Assistant. How can I help you today?"}
        ]
        st.rerun()
# Render previous chat messages from Streamlit session state
for msg in st.session_state.messages:
    with st.chat_message(msg["role"]):
        st.markdown(msg["content"])
# Capture user input
if prompt := st.chat_input("Ask a question about the university..."):
    # 1. Display user message in the UI instantly
    with st.chat_message("user"):
        st.markdown(prompt)
    
    # 2. Save user message to Streamlit UI history
    st.session_state.messages.append({"role": "user", "content": prompt})
    # 3. Process the AI response
    with st.spinner("Thinking..."):
        try:
            # Initialize agent
            agent = create_agent(st.session_state.thread_id)
            
            # Pass ONLY the new prompt to LangGraph.
            # LangGraph handles the history automatically via its SQLite checkpointer.
            config = {"configurable": {"thread_id": st.session_state.thread_id}}
            response = agent.invoke(
                {"messages": [("user", prompt)]}, 
                config=config
            )
            
            # Extract the AI's final response content
            ai_response = response["messages"][-1].content
            
        except Exception as e:
            ai_response = f"An error occurred: {str(e)}"
    
    # 4. Display AI response in the UI
    with st.chat_message("assistant"):
        st.markdown(ai_response)
        
    # 5. Save AI response to Streamlit UI history
    st.session_state.messages.append({"role": "assistant", "content": ai_response})
‎chat_history.db-shm‎
32 KB
Binary file not shown.
‎chat_history.db-wal‎
668 KB
Binary file not shown.
‎data/ACADEMIC REGULATIONS 2025).pdf‎
656 KB
Binary file not shown.
‎data/BE CSE.pdf‎
1.1 MB
Binary file not shown.
‎data/Mock University Data (1).pdf‎
46.9 KB
Binary file not shown.
‎requirements.txt‎
+17
Lines changed: 17 additions & 0 deletions
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,17 @@
# Environment variable management
python-dotenv==1.0.1
# --- Dependencies for Agentic Pipeline ---
# LangChain Orchestration & Integrations
langchain>=0.3.0
langchain-core>=0.3.0
langchain-community>=0.3.0
langchain-groq>=0.2.0
langchain-chroma>=0.1.4
langchain-huggingface>=0.1.0
langgraph>=0.2.0
langgraph-checkpoint-sqlite>=2.0.0
# Vector Store & Embeddings
chromadb
sentence-transformers
