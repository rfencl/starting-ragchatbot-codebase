# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the Application

Start the RAG system:

```bash
./run.sh
```

Or manually:

```bash
cd backend && uv run uvicorn app:app --reload --port 8000
```

Install dependencies:

```bash
uv sync
```

## Environment Setup

Create `.env` file in root with:

``` bash
ANTHROPIC_API_KEY=sk-ant-api03-H8o...wAAA
```

## Architecture Overview

This is a RAG (Retrieval-Augmented Generation) chatbot system that answers questions about course materials using semantic search and Claude AI.

### Core Components

**RAG System Flow (backend/rag_system.py)**

- Main orchestrator that coordinates all components
- Handles document ingestion, query processing, and response generation
- Uses tool-based approach where AI can call search functions

**Vector Storage (backend/vector_store.py)**  

- ChromaDB-based semantic search with two collections:
  - `course_catalog`: Course metadata (titles, instructors, lessons)
  - `course_content`: Actual chunked course material
- Supports filtering by course name and lesson number

**Document Processing (backend/document_processor.py)**

- Converts course documents into structured Course/Lesson objects
- Chunks content into 800-character segments with 100-character overlap
- Supports PDF, DOCX, TXT formats

**AI Generation (backend/ai_generator.py)**

- Uses Anthropic Claude (Sonnet 4) with tool calling capabilities
- Maintains conversation history via session management
- Can invoke search tools during response generation

**Search Tools (backend/search_tools.py)**

- Implements CourseSearchTool that AI can call
- Handles course name resolution and content filtering
- Returns formatted search results with sources

### API Structure

FastAPI application serving:

- `POST /api/query`: Main chat endpoint
- `GET /api/courses`: Course analytics
- Static frontend files at root `/`

### Key Configuration

All settings in `backend/config.py`:

- Chunk size: 800 chars, overlap: 100 chars
- Max search results: 5
- Conversation history: 2 exchanges
- ChromaDB path: `./chroma_db`
- Embedding model: `all-MiniLM-L6-v2`

### Document Loading

On startup, automatically loads all documents from `../docs/` folder. Avoids reprocessing existing courses by checking titles in vector store.

### Session Management

Tracks conversation history per session for context-aware responses. Sessions created automatically if not provided in requests.
