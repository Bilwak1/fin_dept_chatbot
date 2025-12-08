# 🎓 SCU Finance Department ChatBot

An intelligent AI-powered chatbot designed for Santa Clara University's Leavey School of Business Finance Department. This chatbot helps students get instant answers to frequently asked questions about finance programs, courses, requirements, and general knowledge.

## 📋 Table of Contents

- [Features](#features)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [API Keys Setup](#api-keys-setup)
- [Deployment](#deployment)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## ✨ Features

### 🤖 Intelligent Question Answering
- **Dual-mode operation**: Handles both SCU-specific questions and general knowledge queries
- **Context-aware responses**: Automatically detects question type and routes appropriately
- **Multi-source retrieval**: Combines knowledge base, uploaded documents, and web search

### 🎤 Voice Input
- **Hold-to-speak functionality**: Press and hold the mic button to speak
- **Real-time transcription**: See your words appear as you speak
- **Visual feedback**: Animated "Listening..." indicator during recording
- **Browser-based**: No additional software required (uses Web Speech API)

### 📚 Knowledge Management
- **Dual Vector Store**: Uses both Chroma (persistent) and FAISS (in-memory) for optimal performance
- **Document Support**: PDF, Word (.docx, .doc), and Excel (.xlsx, .xls) files
- **Runtime Uploads**: Add documents on-the-fly without rebuilding the knowledge base
- **Smart Chunking**: Recursive text splitting with 1000-token chunks and 150-token overlap

### 💬 Multi-Session Chat
- **Session Management**: Create, switch between, and delete multiple chat sessions
- **Conversation History**: Maintains full chat history per session
- **Auto-titling**: Sessions automatically titled based on first question
- **User Isolation**: Hashed API keys ensure user privacy

### 🌐 Web Search Integration
- **Real-time Information**: Fetches current data via Serper API (Google Search)
- **Source Citations**: Automatically provides clickable links to web sources
- **SCU-focused Search**: Prioritizes official scu.edu sources for university questions

### 🎨 User Interface
- **Clean Design**: Modern, intuitive Streamlit interface
- **Responsive Layout**: Works on desktop and mobile devices
- **Visual Indicators**: Loading states, status messages, and error handling
- **Source Display**: Expandable panels for viewing retrieved documents

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     User Interface (Streamlit)               │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ Text Input   │  │ Voice Input  │  │  File Upload │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│              Question Type Detection                         │
│      (SCU-related vs General Knowledge)                      │
└─────────────────────────────────────────────────────────────┘
                            │
            ┌───────────────┴───────────────┐
            ▼                               ▼
┌───────────────────────┐       ┌───────────────────────┐
│  RAG Pipeline         │       │  Web Search Pipeline  │
│  ┌─────────────────┐  │       │  ┌─────────────────┐  │
│  │ Chroma Vector   │  │       │  │ Serper API      │  │
│  │ Store           │  │       │  │ (Google Search) │  │
│  └─────────────────┘  │       │  └─────────────────┘  │
│  ┌─────────────────┐  │       └───────────────────────┘
│  │ FAISS Vector    │  │
│  │ Store           │  │
│  └─────────────────┘  │
└───────────────────────┘
            │
            ▼
┌─────────────────────────────────────────────────────────────┐
│              CrewAI Agent (GPT-4o-mini)                      │
│  • Synthesizes information from all sources                  │
│  • Generates contextual responses                            │
│  • Provides source citations                                 │
└─────────────────────────────────────────────────────────────┘
            │
            ▼
┌─────────────────────────────────────────────────────────────┐
│              Response with Citations                         │
└─────────────────────────────────────────────────────────────┘
```

### Technology Stack

**Framework & UI:**
- `Streamlit` - Web application framework
- `Streamlit Components` - Custom JavaScript integration for voice input

**LLM & AI:**
- `OpenAI GPT-4o-mini` - Language model
- `CrewAI` - Multi-agent AI framework
- `LangChain` - LLM application framework

**Vector Databases:**
- `Chroma` - Persistent vector store
- `FAISS` - High-performance in-memory vector store
- `OpenAI Embeddings` - Text embeddings (text-embedding-ada-002)

**Document Processing:**
- `PyPDF` - PDF parsing
- `Unstructured` - Document loaders for Word/Excel
- `RecursiveCharacterTextSplitter` - Intelligent text chunking

**Search & Tools:**
- `SerperDevTool` - Web search integration (Google Search API)

**Deployment:**
- `ngrok` - Public URL tunneling for Colab deployment
- `pyngrok` - Python wrapper for ngrok

## 📋 Prerequisites

- Python 3.10 or higher
- Google Colab account (for cloud deployment)
- Google Drive (for knowledge base storage)
- OpenAI API key
- Serper API key (optional, for web search)
- Modern web browser with Web Speech API support (Chrome, Edge, Safari)

## 🚀 Installation

### Option 1: Google Colab (Recommended)

1. **Open the notebook in Google Colab:**
   ```
   https://colab.research.google.com/
   ```

2. **Mount your Google Drive:**
   ```python
   from google.colab import drive
   drive.mount("/content/drive")
   ```

3. **Install dependencies:**
   ```bash
   !pip install -q "crewai[tools]" crewai \
       langchain langchain-openai langchain-community langchain-chroma \
       faiss-cpu \
       "unstructured[all-docs]" pypdf python-docx openpyxl \
       streamlit pyngrok
   ```

4. **Run the application** (see Usage section)

### Option 2: Local Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/yourusername/scu-finance-chatbot.git
   cd scu-finance-chatbot
   ```

2. **Create a virtual environment:**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

4. **Run the application:**
   ```bash
   streamlit run app.py
   ```

## ⚙️ Configuration

### Directory Structure

Create the following directory structure in your Google Drive:

```
MyDrive/
└── NLP/
    └── FinChat/
        ├── SCU_Logo.png          # University logo (optional)
        ├── document1.pdf          # Knowledge base documents
        ├── document2.docx
        ├── document3.xlsx
        └── chroma_db/            # Auto-generated vector store
```

### Environment Variables

Set the following paths in `app.py`:

```python
DEFAULT_KB_DIR = "/content/drive/MyDrive/NLP/FinChat"  # Knowledge base path
LOGO_PATH = "/content/drive/MyDrive/NLP/FinChat/SCU_Logo.png"  # Logo path
```

## 🔑 API Keys Setup

### 1. OpenAI API Key (Required)

**Get your key:**
1. Visit [OpenAI Platform](https://platform.openai.com/api-keys)
2. Sign up or log in
3. Navigate to API Keys section
4. Create a new secret key
5. Copy the key immediately (it won't be shown again)

**Pricing:**
- GPT-4o-mini: $0.15 per 1M input tokens, $0.60 per 1M output tokens
- Embeddings: $0.02 per 1M tokens

**Add to chatbot:**
- Enter in the sidebar "Enter Your OpenAI API Key" field
- Or set as environment variable: `os.environ["OPENAI_API_KEY"] = "sk-..."`

### 2. Serper API Key (Optional - For Web Search)

**Get your key:**
1. Visit [Serper.dev](https://serper.dev)
2. Sign up for a free account
3. Get 2,500 free searches per month
4. Copy your API key from the dashboard

**Add to chatbot:**
- Enter in the sidebar "Serper API Key" field
- Enable "🌐 Enable Web Search" checkbox

### 3. Ngrok Auth Token (For Colab Deployment)

**Get your token:**
1. Visit [ngrok.com](https://ngrok.com)
2. Sign up for a free account
3. Go to [Your Authtoken](https://dashboard.ngrok.com/get-started/your-authtoken)
4. Copy your authtoken

**Add to code:**
```python
ngrok.set_auth_token("YOUR_NGROK_TOKEN_HERE")
```

## 📖 Usage

### Starting the ChatBot

**In Google Colab:**

1. Run all cells in order
2. When ngrok connects, you'll see a public URL:
   ```
   ✅ App is live at: https://xxxx-xxxx-xxxx.ngrok-free.dev
   ```
3. Click the URL to open the chatbot
4. Click "Visit Site" on the ngrok warning page

**Locally:**
```bash
streamlit run app.py
```
The app will open at `http://localhost:8501`

### Using the ChatBot

#### 1. **Initial Setup**

**Enter API Keys:**
- Add your OpenAI API key in the sidebar
- Optionally add Serper API key for web search

**Build Knowledge Base:**
1. Upload PDF/Word/Excel files to your Google Drive folder
2. Click "🔄 Build / Rebuild KB Index" in the sidebar
3. Wait for indexing to complete

#### 2. **Asking Questions**

**Text Input:**
- Type your question in the chat box
- Press Enter to submit

**Voice Input:**
1. Click and hold the 🗣️ (or 🎤) button
2. Speak your question clearly
3. Release the button when done
4. Press Enter to submit

**Example Questions:**
- "What are the prerequisites for FNCE 121?"
- "Tell me about the MSFA program"
- "What is the difference between corporate finance and investment management?"
- "Who are the faculty members in the finance department?"
- "What is the current S&P 500 price?" (requires web search)

#### 3. **Managing Sessions**

- **New Chat**: Click "➕ New Chat" in the sidebar
- **Switch Chats**: Click on any session in the list
- **Delete Chat**: Click the 🗑️ button next to a session
- **Session Titles**: Auto-generated from first question

#### 4. **Uploading Additional Documents**

1. Click "Upload PDF / Word / Excel files" in sidebar
2. Select one or more files
3. Click "📦 Index Uploaded Files"
4. Documents are added to the current session

#### 5. **Web Search**

- Enable with "🌐 Enable Web Search" checkbox
- Automatically used for general knowledge questions
- For SCU questions, used only if local context is insufficient
- Sources displayed at the bottom of responses

### Question Types

**SCU-Related Questions** (uses knowledge base):
- Keywords: scu, santa clara, leavey, msfa, finance department, fnce, admission, etc.
- Searches local documents first
- Falls back to scu.edu web search if needed

**General Questions** (uses web search):
- Any non-SCU-specific question
- Fetches current information from the web
- Displays source links

## 📁 Project Structure

```
scu-finance-chatbot/
│
├── app.py                          # Main application file
│   ├── Configuration               # Streamlit config, paths
│   ├── Helper Functions           
│   │   ├── get_openai_api_key()   # API key management
│   │   ├── get_serper_api_key()   # Serper API management
│   │   ├── is_scu_related_question() # Question classification
│   │   └── get_user_hash()        # User authentication
│   │
│   ├── Document Processing
│   │   ├── load_docs_from_directory()  # Load KB documents
│   │   ├── load_docs_from_uploaded_files()  # Handle uploads
│   │   ├── make_text_splitter()   # Text chunking
│   │   └── build_vectorstore()    # Chroma + FAISS indexing
│   │
│   ├── Retrieval System
│   │   └── retrieve_context()     # Multi-source retrieval
│   │
│   ├── AI Agent
│   │   ├── get_crew_agent_and_tool()  # CrewAI agent setup
│   │   ├── run_agent_answer()     # Generate responses
│   │   └── extract_urls_from_answer() # Parse citations
│   │
│   ├── Voice Recognition
│   │   └── add_voice_input_to_chat()  # JavaScript integration
│   │
│   └── Streamlit UI              # User interface code
│
├── requirements.txt               # Python dependencies
├── README.md                      # This file
└── LICENSE                        # MIT License
```

## 🔧 Troubleshooting

### Common Issues

#### 1. Voice Recognition Not Working

**Problem**: Mic button doesn't respond or no transcription appears

**Solutions:**
- Ensure you're using Chrome, Edge, or Safari (Firefox doesn't support Web Speech API)
- Check browser permissions: Allow microphone access
- For Colab: Make sure you're using HTTPS (ngrok provides this)
- Clear browser cache and reload

#### 2. Ngrok Tunnel Already Exists

**Error**: `PyngrokNgrokHTTPError: endpoint already online`

**Solution:**
```python
# Add this before ngrok.connect()
!pkill -9 ngrok
!killall -9 ngrok
from pyngrok import ngrok
ngrok.kill()
time.sleep(3)
```

#### 3. OpenAI API Rate Limit

**Error**: `RateLimitError: Rate limit exceeded`

**Solutions:**
- Wait a few seconds and try again
- Upgrade to a paid OpenAI plan
- Reduce chunk size in `make_text_splitter()`

#### 4. Knowledge Base Not Found

**Error**: `Directory does not exist`

**Solution:**
- Check the `DEFAULT_KB_DIR` path in `app.py`
- Ensure Google Drive is mounted: `drive.mount("/content/drive")`
- Create the directory manually in Google Drive

#### 5. Chroma Persistence Issues

**Problem**: Knowledge base needs to be rebuilt every session

**Solution:**
- Ensure write permissions to Google Drive
- Check if `chroma_db/` folder is being created
- Try using a local temp directory for testing

#### 6. Streamlit Won't Start

**Error**: `Port 8501 already in use`

**Solution:**
```bash
!pkill -9 -f streamlit
!fuser -k 8501/tcp
```

#### 7. Dependencies Installation Fails

**Solution:**
```bash
# Install packages individually
!pip install --upgrade pip
!pip install streamlit
!pip install "crewai[tools]"
!pip install langchain langchain-openai
# etc.
```

## 🎯 Best Practices

### For Administrators

1. **Knowledge Base Management:**
   - Organize documents by topic (admissions, courses, programs)
   - Use clear, descriptive filenames
   - Update documents regularly
   - Remove outdated information

2. **Performance Optimization:**
   - Keep document chunks under 1000 tokens
   - Index only relevant documents
   - Rebuild index after major updates
   - Use FAISS for faster searches

3. **Security:**
   - Never commit API keys to version control
   - Use environment variables for production
   - Rotate API keys regularly
   - Monitor API usage and costs

### For Users

1. **Asking Questions:**
   - Be specific and clear
   - Use keywords related to your topic
   - For courses, include course codes (e.g., "FNCE 121")
   - Ask follow-up questions for clarification

2. **Voice Input:**
   - Speak clearly and at a normal pace
   - Hold the button for the entire question
   - Minimize background noise
   - Review transcription before submitting

3. **Managing Sessions:**
   - Create new sessions for different topics
   - Use descriptive first questions (auto-titled)
   - Delete old sessions to keep organized

## 🔒 Privacy & Security

- **API Key Hashing**: User API keys are hashed with SHA-256
- **Local Processing**: No data sent to external servers (except LLM APIs)
- **Session Isolation**: Each user's sessions are separate
- **No Data Persistence**: Chat history stored only in memory
- **HTTPS**: Ngrok provides encrypted connections

## 📊 Performance Metrics

- **Response Time**: 2-5 seconds (local context)
- **Web Search Response**: 5-10 seconds
- **Voice Transcription**: Real-time
- **Document Indexing**: ~1-2 seconds per document
- **Concurrent Users**: Limited by API rate limits

## 🚀 Future Enhancements

- [ ] Add conversation memory/context
- [ ] Implement streaming responses
- [ ] Support more document types (HTML, Markdown)
- [ ] Add admin dashboard for analytics
- [ ] Export chat history to PDF
- [ ] Multi-language support
- [ ] Integration with Canvas LMS
- [ ] Mobile app version
- [ ] Custom embeddings fine-tuning
- [ ] A/B testing for response quality

## 🤝 Contributing

Contributions are welcome! Please follow these guidelines:

1. **Fork the repository**
2. **Create a feature branch:**
   ```bash
   git checkout -b feature/amazing-feature
   ```
3. **Commit your changes:**
   ```bash
   git commit -m "Add amazing feature"
   ```
4. **Push to the branch:**
   ```bash
   git push origin feature/amazing-feature
   ```
5. **Open a Pull Request**

### Code Style

- Follow PEP 8 style guide
- Add docstrings to all functions
- Include type hints
- Write meaningful commit messages

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👥 Authors

- **Bilwa Khaparde, Sangita Poudel, Kai Yang , Sami Shikhare** - *Initial work* - 

## 🙏 Acknowledgments

- Santa Clara University Leavey School of Business
- Finance Department faculty and staff
- OpenAI for GPT-4o-mini
- LangChain and CrewAI communities


## 📈 Changelog

### Version 1.0.0 (2024-12-07)
- Initial release
- Multi-session chat support
- Voice input functionality
- Dual vector store (Chroma + FAISS)
- Web search integration
- SCU-specific question routing

---

**Made with ❤️ for Santa Clara University Finance Department**
