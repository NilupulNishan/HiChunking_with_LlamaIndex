# 🚀 Hierarchical RAG Chatbot

Production-ready Retrieval-Augmented Generation system with hierarchical document chunking, powered by Azure OpenAI.

## ✨ Features

- **Hierarchical Chunking**: Multi-level document structure (sections → subsections → paragraphs)
- **Auto-Merge Retrieval**: Intelligent merging of sibling chunks for better context
- **State-of-the-Art Models**:
  - Embeddings: `text-embedding-3-large` (3072 dimensions)
  - LLM: `gpt-4o`
- **ChromaDB**: Fast, local vector database
- **300-Page PDF Support**: Handles large documents efficiently
- **Interactive CLI**: Beautiful command-line interface

## 📋 Prerequisites

- Python 3.9+
- Azure OpenAI account with:
  - `text-embedding-3-large` deployment
  - `gpt-4o` deployment
- 8GB+ RAM (for processing large PDFs)

## 🛠️ Installation

### 1. Clone/Create Project

```bash
# Create project structure
bash project_structure.sh

# Navigate to project
cd hierarchical-rag-chatbot
```

### 2. Create Virtual Environment

```bash
# Create venv
python -m venv venv

# Activate
# On Linux/Mac:
source venv/bin/activate
# On Windows:
venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Download NLTK Data

```bash
python -c "import nltk; nltk.download('punkt')"
```

### 5. Configure Azure OpenAI

```bash
# Copy environment template
cp config/.env.example .env

# Edit .env with your Azure credentials
nano .env  # or use your favorite editor
```

Edit `.env`:

```bash
AZURE_OPENAI_API_KEY=your-actual-api-key-here
AZURE_OPENAI_ENDPOINT=https://your-resource.openai.azure.com/
AZURE_OPENAI_API_VERSION=2024-02-15-preview
AZURE_OPENAI_EMBEDDING_DEPLOYMENT=text-embedding-3-large
AZURE_OPENAI_LLM_DEPLOYMENT=gpt-4o
EMBEDDING_DIMENSIONS=3072
```

## 📁 Project Structure

```
hierarchical-rag-chatbot/
├── src/
│   ├── chunking/
│   │   ├── pdf_processor.py       # PDF text extraction
│   │   └── hierarchical_chunker.py # Multi-level chunking
│   ├── embeddings/
│   │   └── azure_embeddings.py    # text-embedding-3-large
│   ├── storage/
│   │   └── chroma_store.py        # ChromaDB vector store
│   ├── retrieval/
│   │   ├── auto_merge_retriever.py # Auto-merge algorithm
│   │   └── query_engine.py        # Query orchestration
│   └── utils/
│       └── logger.py              # Logging setup
├── data/
│   ├── raw/                       # Place PDFs here
│   ├── processed/                 # Chunk visualizations
│   └── indexes/                   # ChromaDB storage
├── config/
│   ├── config.yaml                # Main configuration
│   └── .env                       # Azure credentials
├── run_indexing.py                # Index PDFs
├── run_chatbot.py                 # Interactive chatbot
└── requirements.txt               # Dependencies
```

## 🚀 Quick Start

### Step 1: Add Your PDFs

```bash
# Copy your PDFs to the raw data directory
cp /path/to/your/document.pdf data/raw/
```

### Step 2: Index Documents

```bash
python run_indexing.py
```

This will:
1. Extract text from PDFs
2. Create hierarchical chunks (3 levels)
3. Generate embeddings using text-embedding-3-large
4. Store in ChromaDB

**Expected output:**
```
🚀 Hierarchical RAG Indexing Pipeline

✓ Found 1 PDF file(s)

Processing: your_document.pdf
  ✓ Extracted 300 pages
  ✓ Total words: 150,000
  ✓ Created 1,234 chunks (890 leaf nodes)

┏━━━━━━━┳━━━━━━━┳━━━━━━━━━━━━┓
┃ Level ┃ Count ┃ Avg Tokens ┃
┡━━━━━━━╇━━━━━━━╇━━━━━━━━━━━━┩
│ 1     │ 45    │ 2048       │
│ 2     │ 299   │ 512        │
│ 3     │ 890   │ 128        │
└───────┴───────┴────────────┘

✅ Indexing Complete!
```

### Step 3: Run Chatbot

```bash
python run_chatbot.py
```

**Chat with your documents:**
```
🤖 Hierarchical RAG Chatbot

You: What are the main findings of this document?

Answer:
[Beautiful formatted response with citations]

📚 Sources (3 chunks):
[Relevant excerpts with page numbers]
```

## 💡 Usage Examples

### Basic Query
```python
You: Summarize the methodology section
```

### Specific Information
```python
You: What are the key recommendations mentioned?
```

### Page-Specific
```python
You: What does page 45 discuss?
```

### Commands
- `stats` - Show index statistics
- `clear` - Clear screen
- `exit` or `quit` - Exit chatbot

## ⚙️ Configuration

### Adjust Embedding Dimensions (Cost Optimization)

In `.env`:
```bash
# Reduce dimensions for lower cost (slight quality trade-off)
EMBEDDING_DIMENSIONS=1536  # Options: 3072, 1536, 768, 512, 256
```

### Adjust Chunk Sizes

In `config/config.yaml`:
```yaml
chunking:
  levels:
    - level: 1
      target_tokens: 2048  # Adjust as needed
    - level: 2
      target_tokens: 512
    - level: 3
      target_tokens: 128
```

### Adjust Retrieval

In `config/config.yaml`:
```yaml
retrieval:
  base:
    top_k: 12              # Number of leaf chunks to retrieve
  auto_merge:
    merge_threshold: 2     # Min siblings to trigger merge
```

## 💰 Cost Estimation

For a 300-page PDF (~150,000 words):

### One-Time Indexing
- **Embedding**: ~200k tokens × $0.00013/1k = **$0.026**
- **Storage**: Negligible (local ChromaDB)

### Per Query
- **Query embedding**: 1 × 50 tokens × $0.00013/1k = **$0.000007**
- **LLM (gpt-4o)**:
  - Input: ~4k tokens × $0.0025/1k = **$0.01**
  - Output: ~500 tokens × $0.01/1k = **$0.005**
- **Total per query**: ~**$0.015**

### Monthly (1000 queries)
- **Total**: ~**$15-20/month**

## 🔧 Advanced Features

### Programmatic Usage

```python
from src.storage.chroma_store import get_chroma_store
from src.retrieval.query_engine import create_query_engine
from llama_index.core import StorageContext

# Load index
chroma_store = get_chroma_store()
index = chroma_store.load_index()

# Create storage context
storage_context = StorageContext.from_defaults(
    vector_store=chroma_store.vector_store
)

# Create query engine
query_engine = create_query_engine(index, storage_context)

# Query
response = query_engine.query("Your question here")
print(response.response)

# Access sources
for node in response.source_nodes:
    print(f"Score: {node.score}")
    print(f"Text: {node.node.get_content()}")
```

### Batch Processing

```python
from src.chunking.pdf_processor import PDFProcessor

processor = PDFProcessor()
docs = processor.batch_process_pdfs(Path("data/raw"))
```

### Reset Index

```bash
python -c "
from src.storage.chroma_store import get_chroma_store
store = get_chroma_store()
store.reset()
print('Index reset')
"
```

## 📊 Monitoring

Logs are stored in `logs/`:
- `app.log` - All logs
- `error.log` - Errors only

View logs:
```bash
tail -f logs/app.log
```

## 🐛 Troubleshooting

### Issue: "No documents in index"
**Solution**: Run `python run_indexing.py` first

### Issue: "Azure OpenAI authentication error"
**Solution**: Check your `.env` file credentials

### Issue: "Out of memory"
**Solution**: 
- Process PDFs one at a time
- Reduce `EMBEDDING_DIMENSIONS`
- Reduce chunk sizes in config

### Issue: "ChromaDB locked"
**Solution**: Close all running instances

## 🚢 Deployment

### Docker (Coming Soon)
```bash
docker build -t hierarchical-rag .
docker run -p 8000:8000 hierarchical-rag
```

### Azure Deployment
See `docs/azure_deployment.md` for Azure App Service deployment guide.

## 📈 Performance Tips

1. **Use reduced dimensions** (1536 instead of 3072) for faster indexing
2. **Batch process** multiple PDFs at once
3. **Cache frequently asked questions** at application level
4. **Monitor token usage** to optimize costs

## 🤝 Contributing

Contributions welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## 📄 License

MIT License - See LICENSE file

## 🙏 Acknowledgments

- Based on [HiChunk paper](https://arxiv.org/abs/2509.11552)
- Built with [LlamaIndex](https://www.llamaindex.ai/)
- Powered by [Azure OpenAI](https://azure.microsoft.com/en-us/products/ai-services/openai-service)

## 📞 Support

For issues and questions:
- Create an issue on GitHub
- Check logs in `logs/error.log`
- Review configuration in `config/config.yaml`

---

**Built with ❤️ for better document understanding**