# Code Analysis - Developer Handbook

**Analysis Date:** 2025-11-18  
**Branch:** Not Specified  
**Total Files Analyzed:** 0

---

# AI-Powered Natural Language Query System for MongoDB in the Oil & Gas Domain

## 1. Executive Summary

### Overview
The AI-Powered Natural Language Query System is designed to provide a user-friendly interface for querying complex MongoDB databases, specifically tailored for the oil and gas domain. Through this software, users can pose questions in natural language, and the system interprets these queries, generates safe MongoDB queries, executes them, and returns results—all without the need for users to understand database query languages.

### Business Value
- **Democratizes Data Access**: Empowers business users, analysts, and managers to analyze data without requiring technical expertise.
- **Accelerates Decision-Making**: Significantly reduces time-to-insight by automating the query generation and execution process.
- **Improves Efficiency**: Utilizes semantic caching to reuse previous queries, minimizing redundant computations and costs.
- **Enhances Data Security**: Ensures query safety by validating queries and blocking unauthorized operations.

### Key Features
- Natural language query interface via HTTP API
- AI-driven context detection and query generation
- Semantic caching utilizing vector embeddings and LLM-based verification
- Automated query improvement in case of execution failure
- Comprehensive logs and reasoning steps included in each response
- Recent searches and autocomplete functionality

### Target Users
- **Business Analysts**: Extract insights rapidly from operational data.
- **Data Scientists**: Validate hypotheses and prototype models.
- **Operations Managers**: Generate reports and monitor key performance indicators (KPIs).
- **Developers**: Integrate the API into custom applications or dashboards.

### Anticipated Outcomes
- Faster, more accurate data-driven decision-making.
- Reduced reliance on technical staff for data access.
- Decreased operational costs through query reuse and automation.

---

## 2. System Overview

### High-Level Architecture
The system operates as a modular pipeline orchestrated by a state machine (LangGraph), exposing a FastAPI-based HTTP API for user interaction. The workflow incorporates multiple AI agents responsible for context detection, query generation, and query improvement, transforming natural language queries into safe and executable MongoDB queries. Semantic caching optimizes repeated queries, while all operations are logged for traceability.

### Functionality
- Accepts natural language queries via API.
- Detects relevant database contexts such as collections and fields.
- Generates MongoDB queries utilizing Large Language Models (LLMs).
- Executes queries and formats results for user consumption.
- Automatically improves failed queries.
- Caches and reuses previous queries.
- Provides recent searches and autocomplete suggestions.

### Operational Workflow
1. User submits a query via the API.
2. The system checks for similar cached queries using embeddings and LLM verification.
3. If a cache hit occurs, cached results are returned.
4. If no cache hit occurs, AI agents detect context and generate a new query.
5. The generated query is executed against MongoDB.
6. In the event of execution failure, the improvement agent refines the query.
7. Results, logs, and reasoning steps are returned to the user.

### Key Components
- **API Layer (FastAPI)**: Serves as the entry point for user queries.
- **LangGraph State Machine**: Manages the workflow of AI agents.
- **AI Agents**: Responsible for context detection, query generation, and improvement.
- **Embedding & Cache**: Facilitates semantic search and LLM-based cache verification.
- **MongoEngine**: Executes MongoDB queries.
- **Logging**: Provides centralized logging and state management.

---

## 3. Detailed Architecture

### Architecture Pattern
This architecture adheres to an agentic workflow pattern, managed by a LangGraph state machine. Each agent is tasked with a specific step in the query pipeline, promoting modularity for easier extension and modification. Semantic caching is integrated to enhance performance and reduce LLM usage. The API layer is stateless, with all state information passed through a state dictionary (GenerationState) during execution.

### Technology Stack
- **Languages**: Python
- **Frameworks**: FastAPI (for API), LangChain (for LLM orchestration), LangGraph (for state management)
- **AI/ML Libraries**: OpenAI/Azure OpenAI (for LLM), SentenceTransformers (for embeddings), faiss-cpu (for vector search)
- **Databases**: MongoDB (data storage), Supabase (for vector cache)
- **Infrastructure**: Docker, Docker Compose
- **Tools**: Langfuse (for observability/tracing), python-dotenv (for environment configuration)

### Design Decisions
- **Agentic Modularity**: Each step is encapsulated within an independent agent to enhance maintainability.
- **State Machine Orchestration**: LangGraph facilitates transitions and state updates throughout the workflow.
- **Semantic Caching**: Minimizes redundant LLM calls and enhances response times.
- **Centralized Logging**: All steps are logged and included in API responses.
- **No Authentication**: The API is open, posing a potential security risk.

### Scalability Considerations
- **Semantic Caching**: Reduces the load on LLMs and the database for repeated queries.
- **Asynchronous Execution**: The workflow is executed asynchronously to improve performance.
- **LLM Usage**: LLMs are invoked for every cache check, which may incur costs.
- **No Rate Limiting**: Lack of rate limiting may affect scalability.

### Integration Points
- **MongoDB**: For data storage and query execution.
- **Supabase**: Acts as a vector cache for embeddings.
- **OpenAI/Azure OpenAI**: Used for reasoning and query generation.
- **Langfuse**: Provides observability and tracing capabilities.

---

## 4. Component Details

### FastAPI API Layer
- **Purpose**: Exposes endpoints for query processing, recent searches, and autocomplete functionality.
- **Implementation**: `src/backend/routes/query.py`
- **Key Functions**:
  - `@query_router.post('/query')`: Manages incoming query requests.
  - `@query_router.get('/api/recent-searches')`: Retrieves recent searches.
  - `@query_router.get('/autocomplete/')`: Offers autocomplete suggestions.
- **Data Structures**: Utilizes `QuerySchema` located in `src/backend/schema/query.py`.
- **Error Handling**: Integrates input validation (`src/backend/validators/validators.py`) and generic exception handling.
- **Dependencies**: Interfaces with `execute_workflow`, `Embedding`, and `MongoEngine`.

### LangGraph Agentic Workflow
- **Purpose**: Orchestrates the execution of agents within a state machine framework.
- **Implementation**: `src/ai/graphs/graph_builder.py`
- **Key Classes/Functions**:
  - `GraphBuilder`: Defines nodes and transitions within the state machine.
  - `execute_workflow(state)`: Executes the workflow (`src/ai/graphs/graph_executor.py`).
- **Data Structures**: Uses `GenerationState` from `src/ai/state/generation_state.py`.
- **Error Handling**: Implements error logging within state during node execution.

### Context Detection Agent
- **Purpose**: Identifies relevant collections, fields, and reasoning steps.
- **Implementation**: `src/ai/agents/generation/context_detection_agent.py`
- **Key Methods**: `context_detection(state: dict) -> dict`
- **Dependencies**: Utilizes prompts and configuration settings.
- **Error Handling**: Logs LLM errors in the state.

### Query Generation Agent
- **Purpose**: Constructs MongoDB queries based on reasoning steps.
- **Implementation**: `src/ai/agents/generation/query_generation_agent.py`
- **Key Methods**: `query_generator(state: dict) -> dict`
- **Dependencies**: Relies on prompts and configuration settings.
- **Error Handling**: Logs LLM errors in the state.

### Improvement Agent
- **Purpose**: Enhances failed queries using LLM feedback.
- **Implementation**: `src/ai/agents/generation/improvement_agent.py`
- **Key Methods**: `improve_query(state: dict) -> dict`
- **Error Handling**: Logs LLM errors in the state; noted bugs in the code.

### Embedding and Semantic Cache
- **Purpose**: Generates embeddings, performs semantic searches, and verifies caches using LLMs.
- **Implementation**: `src/backend/embedding/embedding.py`
- **Key Methods**:
  - `generate_embeddings(data: str) -> list`
  - `get_similar_with_enhanced_verification(user_input: str, org_id: str, top_k: int, similarity_threshold: float) -> dict`
  - `check_query_cache_with_llm_verification(user_query: str, org_id: str) -> dict`
  - `store_embeddings(data: dict) -> dict`
- **Error Handling**: Logs LLM errors in the state.

### MongoEngine
- **Purpose**: Executes MongoDB queries.
- **Implementation**: `src/backend/db/mongodb.py`
- **Key Methods**: `execute_query(state: dict) -> dict`
- **Error Handling**: Implements generic exception handling; logs errors in the state.

### Logging
- **Purpose**: Centralizes logging and state management.
- **Implementation**: `logger.py`
- **Key Functions**: `log_to_state`

---

## 5. Operational Workflow

### 5.1 User Request Flow
