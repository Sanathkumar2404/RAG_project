# RAG-Based Conversational AI System

A production-ready Retrieval-Augmented Generation (RAG) system built with FastAPI, LangChain, and Azure OpenAI, featuring multi-modal document processing, conversational memory, and dynamic prompt management.

## Features

- **Multi-Modal RAG**: Supports text and image-based retrieval
- **Conversational Memory**: PostgreSQL-backed chat history with session management
- **Dynamic Prompts**: Client-specific system prompts stored in database
- **Document Ingestion**: Automated pipeline for processing multiple file formats
- **User Authentication**: Secure OAuth2-based authentication system
- **PII Protection**: Automatic anonymization of sensitive information
- **Ensemble Retrieval**: Combines text and image embeddings for better results

## Tech Stack

- **Backend**: FastAPI, Python 3.8+
- **LLM**: Azure OpenAI (GPT-4)
- **Vector Store**: PostgreSQL with pgvector extension
- **Embeddings**: Azure OpenAI embeddings, OpenCLIP for images
- **Framework**: LangChain
- **Storage**: Azure Blob Storage

## Project Structure

```
rag-system/
├── app/
│   ├── __init__.py
│   ├── main.py              # FastAPI application & routes
│   ├── db.py                # Database connections & vector stores
│   ├── agents.py            # LangChain chains & agents
│   ├── ai_utils.py          # Utility functions
│   └── image_processing.py  # Multi-modal processing
├── login/
│   ├── users_model.py       # User database models
│   └── users_schemas.py     # Pydantic schemas
├── ingestion/
│   └── ingestion.py         # Document ingestion pipeline
├── requirements.txt
├── .env.example
├── .gitignore
└── README.md
```

## Installation

1. **Clone the repository**
```bash
git clone https://github.com/yourusername/rag-system.git
cd rag-system
```

2. **Create virtual environment**
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. **Install dependencies**
```bash
pip install -r requirements.txt
```

4. **Set up PostgreSQL**
```bash
# Install PostgreSQL and create database
createdb your_database_name

# Enable pgvector extension
psql -d your_database_name -c "CREATE EXTENSION vector;"
```

5. **Configure environment variables**
```bash
cp .env.example .env
# Edit .env with your credentials
```

6. **Initialize database tables**
```bash
python -m app.db
```

## Configuration

Create a `config.yaml` file or set environment variables:

```yaml
# Azure OpenAI
AZURE_OPENAI_API_KEY: your_key
AZURE_OPENAI_ENDPOINT: your_endpoint
AZURE_OPENAI_VERSION: 2024-02-15-preview

# Database
DB_HOST: localhost
DB_PORT: 5432
DB_USER: postgres
DB_PASSWORD: your_password
DB_SCHEMA: your_schema

# Azure Blob Storage
AZURE_STORAGE_CONNECTION_STRING: your_connection_string
```

## Usage

### Starting the API Server

```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
```

### Document Ingestion

Process documents from Azure Blob Storage:

```bash
python ingestion/ingestion.py
```

### API Endpoints

#### Authentication
- `POST /login` - User login
- `POST /signup` - User registration
- `GET /profile/{id}` - Get user profile

#### Chat
- `POST /api/v1/chat/submit/{chat_id}/streaming/retrieve` - Text-based RAG
- `POST /api/v1/chat/submit/{chat_id}/streaming/retrieve_img` - Multi-modal RAG
- `GET /api/v1/chat/history/{chat_id}/retrieve_chats` - Get chat history
- `POST /api/v1/chat/submit/{chat_id}/streaming/feedback` - Submit feedback

#### Prompts
- `GET /api/v1/prompt/{client_id}` - Fetch client-specific prompts

### Example Request

```python
import requests

response = requests.post(
    "http://localhost:8000/api/v1/chat/submit/user123/streaming/retrieve",
    json={
        "question": "What is the main topic of the document?",
        "client_id": "default"
    },
    headers={"Authorization": f"Bearer {token}"}
)

print(response.json())
```

## Supported File Formats

The ingestion pipeline supports:
- Documents: PDF, DOCX, TXT, MD
- Spreadsheets: CSV, XLSX
- Presentations: PPT, PPTX
- Data: JSON, YAML
- Images: PNG, JPG, JPEG, TIFF
- Logs: LOG files

## Features in Detail

### Dynamic Prompt Management
Store client-specific system prompts in PostgreSQL for easy customization:

```sql
CREATE TABLE prompts (
    client_id VARCHAR PRIMARY KEY,
    system_prompt TEXT,
    prompt_template TEXT
);
```

### PII Protection
Automatic anonymization of:
- Phone numbers
- Email addresses

### Conversational Memory
- PostgreSQL-backed message history
- Session-based conversation tracking
- Support for multi-turn dialogues

## Performance Monitoring

API responses include timing information:
```json
{
  "answer": "...",
  "azure_processing": {
    "input_time": "2025-01-14T10:30:00Z",
    "output_time": "2025-01-14T10:30:05Z",
    "processing_time_ms": 5000.0
  }
}
```

## Security

- OAuth2 password flow authentication
- Password hashing with bcrypt
- Token-based authorization
- CORS middleware configured

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- LangChain for the RAG framework
- Azure OpenAI for LLM capabilities
- FastAPI for the web framework

## Support

For issues and questions, please open an issue on GitHub.
