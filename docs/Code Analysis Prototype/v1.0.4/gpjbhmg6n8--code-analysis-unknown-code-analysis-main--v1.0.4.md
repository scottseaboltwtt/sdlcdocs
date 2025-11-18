# Code Analysis: AI-Powered Natural Language to MongoDB Query Service

**Analysis Date:** 2025-11-18  
**Branch:** Not Specified  
**Total Files Analyzed:** 0  

---

## Executive Summary

### Overview of the Software
This software solution offers an AI-powered service that enables users to pose business inquiries in plain English, receiving structured responses directly from a MongoDB database. Users are not required to have knowledge of database query syntax; instead, they interact with a straightforward API endpoint that interprets their intent, formulates secure database queries, and delivers the results.

### Business Value
- **Accessibility**: Empowers non-technical users to access and analyze business data without requiring technical expertise.
- **Efficiency**: Minimizes the need for technical personnel to craft custom queries for every business question.
- **Speed**: Facilitates rapid, data-driven decision-making by providing immediate answers to business inquiries.
- **Consistency**: Guarantees that queries are secure, validated, and adhere to established business rules.

### Key Features
- Natural language query interface via HTTP API
- AI-driven translation of inquiries to MongoDB queries
- Semantic caching for similar or repeated queries using vector embeddings
- Modular agentic workflow for context detection, query generation, and enhancements
- Structured, explainable results with reasoning steps and logs
- Recent searches and autocomplete functionalities

### Target Users and Use Cases
- **Business Analysts**: Quickly extract business metrics and insights.
- **Operations Managers**: Monitor key performance indicators (KPIs) and generate reports.
- **Executives**: Access high-level summaries and trends.
- **Developers**: Integrate, maintain, and enhance the system.

### Business Impact
- Democratizes access to business data.
- Alleviates operational bottlenecks.
- Enhances data literacy and promotes self-service analytics.
- Ensures data security and compliance through query validation.

---

## System Overview

### High-Level Architecture
The system is designed as a modular, service-oriented backend application. It features a FastAPI HTTP API that accepts natural language queries. These queries are processed through an orchestrated agentic workflow managed by LangGraph, which oversees state transitions and invokes specialized AI agents for context detection, query generation, and enhancements. The architecture employs LangChain for LLM orchestration, Azure OpenAI for LLM and embeddings, and Supabase as a vector database for semantic caching, with MongoDB as the primary data store.

### System Capabilities
- Accepts natural language queries through HTTP POST requests.
- Validates and processes input for safety and intent.
- Checks for cached queries using semantic similarity and LLM validation.
- If no cached results are found, identifies relevant collections and fields using LLMs.
- Generates MongoDB queries or aggregation pipelines through prompt engineering.
- Executes queries against MongoDB and returns structured results.
- Stores new queries and embeddings for future caching.
- Provides recent searches and autocomplete suggestions.
- Logs all actions and integrates with Langfuse for observability.

### Conceptual Workflow
1. User submits a question via the API.
2. The system validates the input and checks for cached results.
3. If no cache hit occurs, the system detects context and generates a query using LLMs.
4. The generated query is validated and executed against MongoDB.
5. Results are returned, and new queries are cached.
6. All actions are logged for traceability.

### Key Components
- **FastAPI HTTP API**: Serves as the entry point for user queries.
- **Agentic Workflow (LangGraph)**: Orchestrates query processing.
- **Embedding and Semantic Cache**: Manages caching and retrieval of similar queries.
- **AI Agents**: Specialized modules for context detection, query generation, and enhancements.
- **MongoDB Data Access Layer**: Executes queries and returns results.
- **Logging and Observability**: Tracks and traces all operations.

---

## Detailed Architecture

### Architecture Pattern
The architecture adheres to a modular, layered pattern emphasizing a clear separation of concerns:
- **API Layer**: FastAPI routes manage HTTP requests, input validation, and response formatting.
- **Orchestration Layer**: LangGraph's state machine directs the workflow, invoking agents and managing state transitions.
- **Agent Layer**: Modular AI agents encapsulate LLM logic and prompt engineering for context detection, query generation, and improvements.
- **Caching Layer**: Manages vector searches and LLM-based intent validation.
- **Database Layer**: Employs MongoEngine for safe query execution and result parsing.
- **Observability Layer**: Centralized logging and Langfuse integration provide traceability.

### Technology Stack
- **Languages**: Python
- **Frameworks**: FastAPI, LangChain, LangGraph
- **AI/ML**: Azure OpenAI (LLM and embeddings), sentence-transformers, faiss-cpu
- **Databases**: MongoDB (primary), Supabase (vector DB for embeddings)
- **Infrastructure**: Docker, Docker Compose, Azure OpenAI
- **Libraries**: Uvicorn, marshmallow, pydantic, numpy, requests, pandas, langfuse, python-dotenv

### Design Decisions
- **Agentic Workflow**: Modular agents enhance extensibility and maintain a clear separation of responsibilities.
- **Semantic Caching**: Reduces LLM calls and optimizes repeated queries through vector embeddings and LLM validation.
- **Prompt Engineering**: Custom prompt templates ensure accurate and secure query generation.
- **Observability**: Integrated logging and tracing facilitate debugging and monitoring.
- **Validation and Guardrails**: Input and query validation mechanisms prevent unsafe operations.

### Scalability and Performance
- **Caching**: Semantic cache mitigates LLM and database load for repeated queries.
- **Asynchronous Processing**: FastAPI and Uvicorn support asynchronous operations.
- **Modularity**: Agents and workflow nodes can be independently extended or updated.
- **Potential Bottlenecks**: LLM calls for every cache check may impact performance and increase costs.

### Integration Points
- **Azure OpenAI**: For LLM and embedding generation.
- **Supabase**: For vector embedding storage and retrieval.
- **MongoDB**: For business data storage and query execution.
- **Langfuse**: For observability and tracing.

---

## Component Details

### FastAPI HTTP API
- **Purpose**: Provides endpoints for query processing, recent searches, and autocomplete features.
- **Implementation**: `src/backend/routes/query.py`, `src/backend/__init__.py`
- **APIs**:
  - `POST /query`: Main endpoint for natural language queries.
  - `GET /api/recent-searches`: Returns cached recent queries.
  - `GET /autocomplete/`: Provides autocomplete suggestions.
- **Key Files**: `src/backend/routes/query.py`, `src/backend/__init__.py`
- **Data Structures**: `QuerySchema` (located in `src/backend/schema/query.py`)
- **Error Handling**: Manages input validation errors; generic exceptions are re-raised.
- **Dependencies**: FastAPI, pydantic, validators.

### Agentic Workflow (LangGraph State Machine)
- **Purpose**: Manages query processing through modular nodes.
- **Implementation**: `src/ai/graphs/graph_builder.py`, `src/ai/graphs/graph_executor.py`
- **Nodes**:
  - `check_cache` (handle_cache_check)
  - `detect_context` (ContextDetectionAgent.context_detection)
  - `generate` (QueryGenerationAgent.query_generator)
  - `execute` (MongoEngine.execute_query)
  - `improve` (ImprovementAgent.improve_query)
- **Key Files**: `src/ai/graphs/graph_builder.py`, `src/ai/graphs/graph_executor.py`
- **Data Structures**: `GenerationState` (located in `src/ai/state/generation_state.py`)
- **Error Handling**: Logs errors and retries improvements once.
- **Dependencies**: LangGraph, LangChain, AI agents.

### Embedding and Semantic Cache
- **Purpose**: Caches queries and results utilizing vector embeddings and LLM validation.
- **Implementation**: `src/backend/embedding/embedding.py`
- **Key Functions**:
  - `get_similar_with_enhanced_verification`
  - `store_embeddings`
- **Key Files**: `src/backend/embedding/embedding.py`
- **Data Structures**: Embedding records.
- **Error Handling**: Not explicitly detailed.
- **Dependencies**: sentence-transformers, faiss-cpu, Supabase.

### AI Agents
- **Purpose**: Modular agents for context detection, query generation, and improvement.
- **Implementation**: `src/ai/agents/generation/context_detection_agent.py`, `src/ai/agents/generation/query_generation_agent.py`, `src/ai/agents/generation/improvement_agent.py`
- **Key Functions**:
  - `context_detection`
  - `query_generator`
  - `improve_query`
- **Key Files**: See above.
- **Data Structures**: State dictionary, prompt templates.
- **Error Handling**: Logs errors and retries improvements once.
- **Dependencies**: LangChain, Azure OpenAI.

### MongoDB Data Access Layer
- **Purpose**: Executes MongoDB queries and aggregation pipelines.
- **Implementation**: `src/backend/db/mongodb.py`
- **Key Functions**:
  - `execute_query`
- **Key Files**: `src/backend/db/mongodb.py`
- **Data Structures**: MongoDB query dictionaries.
- **Error Handling**: Returns execution status and logs errors