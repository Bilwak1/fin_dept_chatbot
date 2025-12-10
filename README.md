# Finance Department RAG Chatbot

An intelligent conversational assistant built with CrewAI and LangChain that answers questions about academic programs, courses, and requirements. Features hybrid retrieval (ChromaDB + FAISS), web search integration, and voice input support.

## Features

- **Hybrid RAG Architecture**: Combines persistent (ChromaDB) and in-memory (FAISS) vector stores for optimal retrieval
- **Multi-Source Intelligence**: 
  - Local knowledge base (PDFs, Word docs, Excel files)
  - User-uploaded documents
  - Real-time web search with Serper API
- **Agentic AI Framework**: CrewAI agents with intelligent routing between local and web sources
- **Voice Input**: Browser-based speech recognition for hands-free interaction
- **Multi-Session Chat**: Persistent conversation history with session management
- **Responsive UI**: Streamlit-based interface with collapsible source citations

## Architecture

```
User Query
    ↓
Question Classification (SCU-related vs General)
    ↓
├─ SCU Question → Local RAG (ChromaDB + FAISS)
│                      ↓
│                 CrewAI Agent (with site:scu.edu search if needed)
│
└─ General Question → CrewAI Agent (web search)
    ↓
Response with Citations
```

## Prerequisites

- Python 3.8+
- OpenAI API key ([get one here](https://platform.openai.com/api-keys))
- Serper API key for web search ([get free key](https://serper.dev))
- Google Colab (optional, for cloud deployment)

## Installation

### Local Setup

1. **Clone the repository**
```bash
git clone https://github.com/yourusername/finance-chatbot.git
cd finance-chatbot
```

2. **Install dependencies**
```bash
pip install -r requirements.txt
```

3. **Set up environment variables**
```bash
export OPENAI_API_KEY="your-openai-api-key"
export SERPER_API_KEY="your-serper-api-key"  # Optional but recommended
```

4. **Prepare knowledge base**
```bash
mkdir -p knowledge_base
# Add your PDF/Word/Excel files to this directory
```

5. **Run the application**
```bash
streamlit run app.py
```

The app will open in your browser at `http://localhost:8501`

### Google Colab Deployment

1. Upload `the_final_chatbot.py` to Google Drive
2. Mount Drive and install dependencies:
```python
from google.colab import drive
drive.mount('/content/drive')

!pip install -q -r requirements.txt
```

3. Set up ngrok for public URL:
```python
from pyngrok import ngrok
ngrok.set_auth_token("your-ngrok-token")

# Start Streamlit
!streamlit run app.py --server.port 8501 &
public_url = ngrok.connect(8501)
print(public_url)
```

## Usage

### Basic Workflow

1. **Configure API Keys**: Enter your OpenAI and Serper API keys in the sidebar
2. **Build Knowledge Base**: 
   - Set the KB folder path (default: `/content/drive/MyDrive/NLP/FinChat`)
   - Click "🔄 Build / Rebuild KB Index"
3. **Upload Documents** (optional): Add session-specific files via the sidebar uploader
4. **Enable Web Search**: Toggle "🌐 Enable Web Search" for general knowledge questions
5. **Start Chatting**: Ask questions in natural language

### Example Queries

**Academic Questions** (uses local KB):
- "What are the prerequisites for FNCE 3000?"
- "Tell me about the MSFA program requirements"
- "What's the difference between Finance major and Real Estate minor?"

**General Questions** (uses web search):
- "What are the current trends in fintech?"
- "Explain the difference between NPV and IRR"
- "What happened in the stock market today?"

### Managing Chat Sessions

- **New Chat**: Create a fresh conversation thread
- **Switch Sessions**: Click any previous chat to restore it
- **Delete Sessions**: Use the 🗑️ button to remove unwanted chats

## Configuration

### Customizing the Knowledge Base

Edit the default path in the sidebar or via code:
```python
DEFAULT_KB_DIR = "/path/to/your/knowledge/base"
```

### Adjusting Retrieval Parameters

Modify chunk size and overlap in `make_text_splitter()`:
```python
RecursiveCharacterTextSplitter(
    chunk_size=800,      # Characters per chunk
    chunk_overlap=100,   # Overlap between chunks
    # ...
)
```

### Customizing the Agent

Update the agent's role, goal, or backstory in `get_crew_agent_and_tool()`:
```python
agent = Agent(
    role="Your Custom Role",
    goal="Your custom goal",
    backstory="Your custom instructions",
    # ...
)
```

## Project Structure

```
finance-chatbot/
├── app.py                      # Main Streamlit application
├── requirements.txt            # Python dependencies
├── README.md                   # This file
├── knowledge_base/             # Local document storage
│   ├── program_guides/
│   ├── course_catalogs/
│   └── policy_documents/
└── assets/
    └── logo.png                # Organization logo
```

## Technical Details

### Vector Store Strategy

- **ChromaDB**: Persistent storage for knowledge base, survives restarts
- **FAISS**: In-memory storage for uploaded files, optimized for speed
- **Deduplication**: Combines results from both stores and removes duplicates

### Document Processing

Supported formats:
- PDF (via PyPDFLoader)
- Word (.docx, .doc via UnstructuredWordDocumentLoader)
- Excel (.xlsx, .xls via UnstructuredExcelLoader)

### Security Features

- API keys hashed with SHA-256 for user identification
- Keys stored only in session state (not persisted to disk)
- Input sanitization and guardrails against harmful requests

## Troubleshooting

### Common Issues

**"No module named 'crewai'"**
```bash
pip install --upgrade crewai crewai-tools
```

**"ChromaDB not persisting data"**
- Ensure the persist directory has write permissions
- Check that `chroma_vectorstore.persist()` is called after indexing

**"Web search not working"**
- Verify SERPER_API_KEY is set correctly
- Check API quota at serper.dev dashboard

**"Voice input not appearing"**
- Ensure browser supports Web Speech API (Chrome/Edge recommended)
- Check microphone permissions in browser settings

### Performance Optimization

For large knowledge bases (1000+ documents):
- Increase `chunk_size` to 1200
- Reduce `k` parameter in similarity search to 2-3
- Consider using only FAISS for faster retrieval

## Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- **CrewAI** for the agentic framework
- **LangChain** for document processing and vector store abstractions
- **Streamlit** for the interactive web interface
- **OpenAI** for GPT-4 language models
- **Serper** for web search capabilities

## Contact

For questions or support, please open an issue on GitHub or contact the maintainers.

---

**Note**: This chatbot is designed for academic advising assistance. It does not provide personalized financial or investment advice. For official academic guidance, always consult your institution's advising office.