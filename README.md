# RAG Chatbot Production

A production-oriented Retrieval-Augmented Generation (RAG) pipeline for ingesting PDF documents, embedding their content, storing it in Qdrant, and answering questions grounded in the retrieved source material.

This project combines:

- PDF document ingestion and chunking
- OpenAI embedding generation
- Vector storage with Qdrant
- FastAPI + Inngest for event-driven workflows
- LLM-powered answer generation using the retrieved context

## Overview

The application processes a PDF by:

1. Loading the document
2. Splitting it into chunks using sentence-aware chunking
3. Embedding each chunk with OpenAI's embedding model
4. Upserting the vectors and payload into Qdrant
5. Querying the database for relevant chunks
6. Sending the context to a model to produce a grounded answer

## Architecture

- `data_loader.py` — PDF loading, chunking, and embeddings
- `vector_db.py` — Qdrant vector database wrapper
- `main.py` — FastAPI + Inngest app with ingestion and query functions
- `custom_types.py` — typed payload models used by the workflow

## Features

- PDF ingestion for document-based knowledge retrieval
- Embedding-based semantic search using OpenAI text-embedding-3-large
- Qdrant-backed similarity search with cosine distance
- Source-aware retrieval metadata
- Event-driven ingestion and querying via Inngest
- Simple, production-friendly Python app structure

## Tech Stack

- Python 3.12+
- FastAPI
- Inngest
- Qdrant
- OpenAI API
- LlamaIndex PDF reader
- python-dotenv

## Prerequisites

Before running this project, make sure you have:

- Python 3.12 or newer
- A running Qdrant instance
- An OpenAI API key
- Access to a project environment with the appropriate dependencies installed

## Installation

Clone the repository:

```bash
git clone https://github.com/ayah05/RAG-Chatbot-Production.git
cd RAG-Chatbot-Production
```

Create and activate a virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

If you are using the project metadata defined in `pyproject.toml`, you can also install it with:

```bash
pip install -e .
```

## Environment Setup

Create a `.env` file in the project root:

```env
OPENAI_API_KEY=your_openai_api_key_here
```

You may also add any additional configuration needed for your local deployment.

## Running Qdrant

Start a local Qdrant instance:

```bash
docker run -p 6333:6333 -v $(pwd)/qdrant_storage:/qdrant/storage qdrant/qdrant
```

The app is configured to connect to Qdrant at `http://localhost:6333` by default.

## Running the App

Start the API server:

```bash
uvicorn main:app --reload
```

This project uses Inngest functions registered in `main.py`, so the app can also be used in an event-driven workflow setup with the appropriate Inngest configuration.

## Workflow

### 1) Ingest a PDF

The ingestion workflow is triggered with an event such as:

```json
{
  "pdf_path": "/path/to/document.pdf",
  "source_id": "document-001"
}
```

This loads the PDF, splits it into chunks, embeds each chunk, and stores the results in Qdrant.

### 2) Query the indexed content

The query workflow can be triggered with a question:

```json
{
  "question": "What are the key findings in this document?",
  "top_k": 5
}
```

The app retrieves the top relevant chunks, constructs a context block, and asks the LLM to answer using only that retrieved context.

## Example Data Flow

```text
PDF -> chunking -> embeddings -> Qdrant -> semantic search -> LLM answer
```

## Notes

- The embedding dimension is set to `3072`, matching the `text-embedding-3-large` model.
- This project is designed for local or small-scale production-oriented deployment and can be extended for authentication, deployment orchestration, and front-end integration.
- The repository currently contains a basic vector search pipeline and is a strong foundation for a full document Q&A product.

## Future Improvements

Potential enhancements include:

- a web UI for chat interactions
- document metadata and filtering
- authentication and user-level access control
- multi-tenant indexing and document management
- deployment with Docker and CI/CD
- logging, metrics, and observability

## License

This project does not currently include a license file. If you plan to share or distribute it publicly, consider adding an appropriate open-source license.
