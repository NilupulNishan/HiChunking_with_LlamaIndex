# Hierarchical RAG Chatbot

A production-ready chatbot built with hierarchical chunking and auto-merging retrieval-augmented generation (RAG) capabilities, powered by Azure OpenAI embeddings.

## Features

- **Hierarchical Chunking**: Multi-level document chunking for better context preservation
- **Auto-Merge Retrieval**: Intelligent chunk merging during retrieval for optimal context
- **Azure OpenAI Integration**: Leverages Azure OpenAI for embeddings and completions
- **Vector Storage**: Efficient storage and retrieval of document embeddings
- **FastAPI Backend**: RESTful API for seamless integration
- **PDF Processing**: Automated PDF document ingestion and processing

## Project Structure

```
hierarchical-rag-chatbot/
├── src/
│   ├── chunking/          # Hierarchical chunking logic
│   │   ├── hierarchical_chunker.py
│   │   └── pdf_processor.py
│   ├── retrieval/         # Retrieval & auto-merge
│   │   ├── retriever.py
│   │   └── auto_merge.py
│   ├── embeddings/        # Azure OpenAI embeddings
│   │   └── azure_embeddings.py
│   ├── storage/           # Vector storage
│   │   └── vector_store.py
│   └── api/               # FastAPI chatbot API
│       ├── chatbot.py
│       └── main.py
├── data/
│   ├── raw/              # Original PDFs
│   ├── processed/        # Processed chunks
│   └── indexes/          # Vector indexes
├── config/
│   ├── config.yaml
│   └── .env.example
├── tests/                # Unit tests
├── notebooks/            # Jupyter notebooks
├── logs/                 # Application logs
├── requirements.txt
├── setup.py
├── Dockerfile
└── README.md
```

## Prerequisites

- Python 3.8+
- Azure OpenAI API access
- Docker (optional, for containerized deployment)

## Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd hierarchical-rag-chatbot
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

4. **Configure environment variables**
   ```bash
   cp config/.env.example config/.env
   # Edit config/.env with your Azure OpenAI credentials
   ```

## Configuration

Edit `config/config.yaml` to customize:

- Chunking parameters (chunk size, overlap)
- Retrieval settings (top-k, similarity threshold)
- Azure OpenAI model configurations
- Vector store settings

Example `.env` file:
```env
AZURE_OPENAI_API_KEY=your_api_key_here
AZURE_OPENAI_ENDPOINT=https://your-resource.openai.azure.com/
AZURE_OPENAI_DEPLOYMENT_NAME=your_deployment_name
AZURE_OPENAI_API_VERSION=2024-02-01
```

## Usage

### 1. Process Documents

Place your PDF files in `data/raw/` and run:

```bash
python -m src.chunking.pdf_processor
```

### 2. Start the API Server

```bash
uvicorn src.api.main:app --reload --host 0.0.0.0 --port 8000
```

### 3. Query the Chatbot

**Using curl:**
```bash
curl -X POST "http://localhost:8000/chat" \
  -H "Content-Type: application/json" \
  -d '{"query": "What is the main topic of the document?"}'
```

**Using Python:**
```python
import requests

response = requests.post(
    "http://localhost:8000/chat",
    json={"query": "What is the main topic of the document?"}
)
print(response.json())
```

## Docker Deployment

Build and run with Docker:

```bash
docker build -t hierarchical-rag-chatbot .
docker run -p 8000:8000 --env-file config/.env hierarchical-rag-chatbot
```

## API Endpoints

- `POST /chat` - Send a query to the chatbot
- `GET /health` - Health check endpoint
- `POST /upload` - Upload new PDF documents
- `GET /documents` - List processed documents

## Development

### Running Tests

```bash
pytest tests/
```

### Jupyter Notebooks

Explore example notebooks in the `notebooks/` directory:
- `01_chunking_demo.ipynb` - Hierarchical chunking examples
- `02_retrieval_demo.ipynb` - Retrieval and auto-merge examples
- `03_end_to_end.ipynb` - Complete RAG pipeline

## Architecture

1. **Document Ingestion**: PDFs are processed and split into hierarchical chunks
2. **Embedding Generation**: Azure OpenAI generates embeddings for each chunk
3. **Vector Storage**: Embeddings are stored with metadata in a vector database
4. **Retrieval**: User queries are embedded and matched against stored chunks
5. **Auto-Merge**: Related chunks are intelligently merged for context
6. **Generation**: Azure OpenAI generates responses using retrieved context

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- Azure OpenAI for embeddings and completions
- FastAPI for the web framework
- LangChain for RAG utilities

## Support

For issues and questions, please open an issue on GitHub or contact the maintainers.

---

**Status**: ✅ Project structure created successfully!