# undefined/undefined - Developer Handbook

**Analysis Date:** 2025-11-21T14:49:13.780Z
**Branch:** undefined
**Total Files Analyzed:** 0

---

# Developer Handbook: AI Agent Query Processing Platform

## 1. Executive Summary

This platform is designed to provide advanced query processing capabilities by leveraging AI agents, prompt engineering, and vector embeddings. Users can submit complex queries through a RESTful API, which are then analyzed and processed by a suite of AI agents. These agents utilize prompt engineering techniques and semantic vector embeddings to interpret, improve, and answer user queries, enabling powerful retrieval-augmented generation (RAG) workflows. The system is modular, extensible, and containerized for scalable deployment.

### Key Features and Capabilities
- **AI Agent Orchestration**: Modular agents for context detection, query generation, and improvement.
- **Prompt Engineering**: Centralized prompt templates for LLM interactions.
- **Vector Embeddings**: Semantic search and retrieval using embedding vectors.
- **RESTful API**: Exposes endpoints for query submission and response retrieval.
- **Extensible Architecture**: Easily add new agents, tools, or data sources.
- **Containerized Deployment**: Docker and Docker Compose for reproducible environments.

### Target Users and Use Cases
- **End Users**: Submit queries and receive AI-generated responses.
- **System Integrators**: Embed the API into external applications.
- **AI Engineers**: Extend agent logic, prompts, and embedding strategies.

### Business Impact and Outcomes
- Enhanced query understanding and response accuracy.
- Scalable, modular AI-driven backend for semantic search and RAG applications.

---

## 2. System Overview

The system is organized into modular components:
- **AI Agents**: Handle context detection, query generation, and improvement.
- **Prompt Engineering**: Centralized management of prompt templates for LLMs.
- **Embedding Module**: Generates and retrieves vector embeddings for semantic search.
- **Backend API**: Exposes endpoints for query handling and agent orchestration.
- **Database Layer**: Manages metadata and document storage (MongoDB).
- **Utility Modules**: Provide logging, helpers, and miscellaneous functions.
- **Configuration Management**: Centralizes environment and runtime configuration.

### High-Level Flow
1. User submits a query via the API.
2. API validates and authorizes the request.
3. Request is routed to the appropriate AI agent.
4. Agent processes the query, possibly generating embeddings.
5. Data is retrieved from the database or embedding store.
6. Response is formatted and returned to the user.

---

## 3. Detailed Architecture

### Architecture Pattern
The system follows a modular, service-oriented architecture. Each major concern (AI agents, API, embedding, database, utilities) is encapsulated in its own module or package. This separation of concerns enables extensibility and maintainability.

### Technology Stack
- **Languages**: Python
- **Frameworks**: Python/pip
- **AI/ML Frameworks**: Prompt Engineering, Vector Embeddings
- **Databases**: MongoDB
- **Infrastructure**: Docker, Docker Compose

### System-Level Design Decisions
- **Modularity**: Each agent and utility is a separate module for easy extension.
- **Centralized Configuration**: All configuration is managed in dedicated config files.
- **Containerization**: Docker is used for environment consistency and scalability.

### Scalability and Performance Considerations
- **Stateless API**: API endpoints are stateless, enabling horizontal scaling.
- **Embedding Caching**: Embedding generation and retrieval can be cached for performance.
- **Agent Extensibility**: New agents can be added without impacting existing logic.

### Integration Points
- **API Endpoints**: `/api/query` for query submission.
- **Database**: MongoDB for metadata and document storage.
- **LLM/Embedding Services**: Integrated via agent and embedding modules.

---

## 4. Component Details

### 4.1 AI Agent Framework
- **Purpose**: Implements agents for context detection, query generation, and improvement.
- **Implementation**: Each agent is a Python module (e.g., `context_detection_agent.py`, `query_generation_agent.py`, `improvement_agent.py`). Agents use prompt templates from `prompts.py` and tools from `tools.py`.
- **Key Files**:
  - `src/ai/agents/generation/context_detection_agent.py`
  - `src/ai/agents/generation/query_generation_agent.py`
  - `src/ai/agents/generation/improvement_agent.py`
  - `src/ai/agents/prompts/prompts.py`
  - `src/ai/agents/tools/tools.py`
- **Data Structures**: Prompt templates, agent state objects.
- **Error Handling**: Agents handle LLM call failures and invalid prompt responses with fallback logic.
- **Dependencies**: Utility modules, prompt templates, embedding module.

### 4.2 Backend API
- **Purpose**: Exposes RESTful endpoints for query processing.
- **Implementation**: API routes are defined in `src/backend/routes/query.py` and `src/backend/routes/__init__.py`. Request schemas are in `src/backend/schema/query.py`.
- **Key Files**:
  - `src/backend/routes/query.py`
  - `src/backend/routes/__init__.py`
  - `src/backend/schema/query.py`
- **Data Structures**: Query schema.
- **Error Handling**: Input validation and error responses.
- **Dependencies**: AI agent framework, validation layer.

### 4.3 Embedding Module
- **Purpose**: Generates and retrieves vector embeddings for semantic search.
- **Implementation**: Core logic in `src/backend/embedding/embedding.py`.
- **Key Files**:
  - `src/backend/embedding/embedding.py`
- **Data Structures**: Embedding vectors.
- **Error Handling**: Handles embedding generation failures.
- **Dependencies**: Database access layer.

### 4.4 Database Access Layer
- **Purpose**: Manages metadata and document storage.
- **Implementation**: MongoDB access logic in `src/backend/db/mongodb.py`, metadata models in `src/backend/db/metadata.py`.
- **Key Files**:
  - `src/backend/db/metadata.py`
  - `src/backend/db/mongodb.py`
- **Data Structures**: Metadata models.
- **Error Handling**: Exception handling for database errors.
- **Dependencies**: MongoDB.

### 4.5 Configuration Management
- **Purpose**: Centralizes configuration for all components.
- **Implementation**: Config files in `src/ai/config/config.py` and `src/backend/config/config.py`.
- **Key Files**:
  - `src/ai/config/config.py`
  - `src/backend/config/config.py`
- **Data Structures**: Config objects.
- **Error Handling**: Handles missing/invalid config.

### 4.6 Utility Modules
- **Purpose**: Provide helper functions and logging.
- **Implementation**: Helpers in `src/ai/utils/helpers.py`, role management in `src/ai/utils/role.py`, logging in `logger.py`.
- **Key Files**:
  - `src/ai/utils/helpers.py`
  - `src/ai/utils/role.py`
  - `logger.py`
- **Data Structures**: Helper functions.
- **Error Handling**: Handles edge cases in utility logic.

### 4.7 Validation Layer
- **Purpose**: Validates API inputs.
- **Implementation**: Validation logic in `src/backend/validators/validators.py`.
- **Key Files**:
  - `src/backend/validators/validators.py`
- **Data Structures**: Validation schemas.
- **Error Handling**: Returns validation errors.

---

## 5. How It Works

### 5.1 User Request Flow (Step-by-Step)
1. **User submits a query** via the `/api/query` endpoint (see `src/backend/routes/query.py`).
2. **API receives the request** and validates input using schemas from `src/backend/schema/query.py` and validation logic from `src/backend/validators/validators.py`.
3. **Authorization is checked** (see security mechanisms in route handlers).
4. **Request is routed to the appropriate AI agent** (e.g., context detection, query generation, or improvement agent in `src/ai/agents/generation/`).
5. **Agent processes the query**:
   - If context detection is needed, `context_detection_agent.py` is invoked.
   - For query generation, `query_generation_agent.py` is used.
   - For query improvement, `improvement_agent.py` is called.
6. **Prompt templates** from `prompts.py` are used to interact with LLMs.
7. **If semantic search is required**, the embedding module (`embedding.py`) generates embeddings and retrieves relevant documents.
8. **Data is retrieved from the database** via `mongodb.py` and `metadata.py`.
9. **Response is formatted** and returned to the user.

### 5.2 Agent Orchestration and State Machine Flow
- **Agents are orchestrated** by the backend route handler, which selects the appropriate agent based on the request type.
- **State is managed** within agent modules, with context and intermediate results passed between agents as needed.
- **Tool calls** (e.g., for embedding or database access) are invoked by agents as required.

### 5.3 Decision Logic
- **Cache lookup**: If implemented, cache is checked before invoking LLMs or database queries.
- **LLM invocation**: Agents decide when to call LLMs based on query complexity and context.
- **Database query generation**: Queries are built by agents or embedding modules as needed.
- **Agent selection**: Based on request type and context.
- **Error handling**: Errors are caught and logged, with fallback responses as needed.

### 5.4 Request/Response Patterns
- **Request**: JSON payload with query and context fields.
- **Response**: JSON with answer, context, and metadata.

### 5.5 Data Flow Through System
- **User Query → API Route → AI Agent → (Embedding/Database) → Response Formatter → User**

### 5.6 Business Workflows
- **Semantic Query Answering**: User submits query, agent processes, embedding retrieves relevant data, response returned.
- **Contextual Query Improvement**: Agent improves query before processing.

### 5.7 Error Handling Flows
- **Validation errors**: Returned as structured error responses.
- **Agent/LLM errors**: Logged and fallback responses provided.
- **Database errors**: Logged and error responses returned.

### 5.8 State Management
- **Session state**: Managed within agent modules as needed.
- **No persistent session state**: API is stateless for scalability.

---

## 6. API Documentation

### Endpoints
- **POST /api/query** (`src/backend/routes/query.py`)
  - **Description**: Handles user queries, routes to AI agents, and returns responses.
  - **Request Body**: JSON with fields such as `query_text`, `context`, `user_id` (see `src/backend/schema/query.py`).
  - **Response**: JSON with answer, context, and metadata.
  - **Authentication**: Authorization checks in route handler.
  - **Error Responses**: Validation errors, authorization errors, agent/database errors.
  - **Rate Limiting**: Not explicitly found in code.
  - **API Versioning**: Not explicitly found in code.

### Request Example
```json
{
  "query_text": "What is the capital of France?",
  "context": "geography",
  "user_id": "user123"
}
```

### Response Example
```json
{
  "answer": "The capital of France is Paris.",
  "context": "geography",
  "metadata": {"source": "AI Agent"}
}
```

---

## 7. Data Architecture

### Database Schemas and Relationships
- **Metadata Model**: Defined in `src/backend/db/metadata.py`.
- **MongoDB Integration**: Access logic in `src/backend/db/mongodb.py`.
- **Embedding Vectors**: Managed in `src/backend/embedding/embedding.py`.

### Data Access Patterns
- **Direct MongoDB access** via helper functions.
- **Embedding retrieval** for semantic search.

### Caching Strategies
- **Not explicitly found in code**.

### Data Validation Rules
- **Validation logic** in `src/backend/validators/validators.py`.

### Migration Strategies
- **Not explicitly found in code**.

### Backup and Recovery
- **Backup scripts**: `backupfile.yml` present, details in file.

### Data Flow Patterns
- **ETL/Streaming**: Not explicitly found in code.
- **Batch Processing**: Not explicitly found in code.

---

## 8. Design Patterns

### Patterns Used
- **Modularization**: Each concern is a separate module (see file structure).
- **Service Layer**: API routes orchestrate agent and data access.

### Architectural Decisions and Trade-offs
- **Separation of concerns** for maintainability and extensibility.
- **Stateless API** for scalability.

### Pattern Interactions
- **API routes** interact with agents and embedding modules via service calls.

### Code Examples
- **Agent Invocation**: `src/backend/routes/query.py` imports and calls agent modules.

### Pattern Locations
- **Modularization**: `src/ai/agents/`, `src/backend/routes/`, `src/backend/embedding/`, `src/ai/utils/`
- **Service Layer**: `src/backend/routes/query.py`

---

## 9. Security

### Security Mechanisms
- **Authorization**: Checks in API route handlers (`src/backend/routes/query.py`).
- **Data Protection**: Not explicitly found in code.

---

## 10. Development Guide

### Setup and Installation
1. **Clone the repository**
2. **Install dependencies**: `pip install -r requirements.txt`
3. **Configure environment**: Copy `.env.example` to `.env` and set variables.
4. **Start services**: `docker-compose up` (requires Docker and Docker Compose)

### Configuration
- **Config files**: `src/ai/config/config.py`, `src/backend/config/config.py`
- **Environment variables**: See `.env.example`

### Running Tests
- **Test files**: `tests/tests.py` (currently empty)

### Debugging Tips
- **Logging**: Use `logger.py` for debug output.
- **Common Issues**: Ensure MongoDB is running, environment variables are set.

---

## 11. File Map

### Repository Layout
```
/
├── .dockerignore
├── .env.example
├── .gitignore
├── azure-pipelines.yml
├── backupfile.yml
├── Code-Analysis-Review.md
├── docker-compose.yml
├── Dockerfile
├── LICENSE
├── logger.py
├── README.md
├── requirements.txt
├── run.py
├── src/
│   ├── ai/
│   │   ├── agents/
│   │   │   ├── generation/
│   │   │   │   ├── context_detection_agent.py
│   │   │   │   ├── improvement_agent.py
│   │   │   │   ├── query_generation_agent.py
│   │   │   │   ├── sample_queries.py
│   │   │   │   └── __init__.py
│   │   │   ├── misc/
│   │   │   │   ├── check_relevance.py
│   │   │   │   └── __init__.py
│   │   │   ├── prompts/
│   │   │   │   ├── prompts.py
│   │   │   │   └── __init__.py
│   │   │   ├── tools/
│   │   │   │   ├── tools.py
│   │   │   │   └── __init__.py
│   │   │   └── __init__.py
│   │   ├── config/
│   │   │   ├── config.py
│   │   │   └── __init__.py
│   │   ├── graphs/
│   │   │   ├── graph_builder.py
│   │   │   ├── graph_executor.py
│   │   │   └── __init__.py
│   │   ├── llm/
│   │   │   ├── guard.py
│   │   │   ├── llm.py
│   │   │   └── __init__.py
│   │   ├── state/
│   │   │   ├── generation_state.py
│   │   │   └── __init__.py
│   │   ├── utils/
│   │   │   ├── helpers.py
│   │   │   ├── role.py
│   │   │   └── __init__.py
│   │   └── __init__.py
│   ├── backend/
│   │   ├── config/
│   │   │   ├── config.py
│   │   │   └── __init__.py
│   │   ├── db/
│   │   │   ├── metadata.py
│   │   │   ├── mongodb.py
│   │   │   └── __init__.py
│   │   ├── embedding/
│   │   │   ├── embedding.py
│   │   │   └── __init__.py
│   │   ├── models/
│   │   │   └── __init__.py
│   │   ├── routes/
│   │   │   ├── query.py
│   │   │   └── __init__.py
│   │   ├── schema/
│   │   │   ├── query.py
│   │   │   └── __init__.py
│   │   ├── utils/
│   │   │   ├── metadata_extractor.py
│   │   │   ├── misc.py
│   │   │   └── __init__.py
│   │   ├── validators/
│   │   │   ├── validators.py
│   │   │   └── __init__.py
│   │   └── __init__.py
│   └── __init__.py
├── tests/
│   ├── tests.py
│   └── __init_.py
```

### Key Files by Component
- **AI Agents**: `src/ai/agents/generation/`
- **Prompt Templates**: `src/ai/agents/prompts/prompts.py`
- **Embedding Module**: `src/backend/embedding/embedding.py`
- **API Routes**: `src/backend/routes/query.py`
- **Database Access**: `src/backend/db/mongodb.py`, `src/backend/db/metadata.py`
- **Validation**: `src/backend/validators/validators.py`
- **Configuration**: `src/ai/config/config.py`, `src/backend/config/config.py`
- **Utilities**: `src/ai/utils/helpers.py`, `logger.py`

### Entry Points
- **API**: `src/backend/routes/query.py`
- **Main Runner**: `run.py`

### Configuration Files
- `.env.example`, `src/ai/config/config.py`, `src/backend/config/config.py`

### Test Files
- `tests/tests.py`

### Documentation Files
- `README.md`, `Code-Analysis-Review.md`

---

## Code Examples

### Agent Invocation (from `src/backend/routes/query.py`)
```python
from src.ai.agents.generation.query_generation_agent import generate_query

@app.route('/api/query', methods=['POST'])
def handle_query():
    data = request.get_json()
    # Validation logic...
    response = generate_query(data['query_text'], data.get('context'))
    return jsonify(response)
```

### Embedding Generation (from `src/backend/embedding/embedding.py`)
```python
def generate_embedding(text):
    # Embedding logic...
    return embedding_vector
```

---

## Troubleshooting
- **MongoDB connection errors**: Ensure MongoDB is running and connection string is correct.
- **Missing environment variables**: Check `.env` file and config files.
- **Docker issues**: Ensure Docker and Docker Compose are installed and running.

---

## Best Practices
- **Modularize new agents**: Place new agent logic in `src/ai/agents/`.
- **Centralize prompts**: Add new prompt templates to `prompts.py`.
- **Use config files**: Store all configuration in `config.py` modules.
- **Write tests**: Add tests to `tests/` directory.

---
