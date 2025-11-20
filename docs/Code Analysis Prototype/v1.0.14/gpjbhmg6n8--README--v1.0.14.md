# undefined/undefined - Developer Handbook

**Analysis Date:** 2025-11-20T01:11:14.339Z
**Branch:** undefined
**Total Files Analyzed:** 0

---

# AI-Powered Natural Language to MongoDB Query Service Handbook

---

## 1. Executive Summary (Business Level)

### What the Software Does
This software provides an AI-powered service that allows users to query MongoDB databases using natural language (plain English) instead of writing complex database queries. Users submit questions or requests in everyday language, and the system interprets, translates, and safely executes these as MongoDB queries, returning results, explanations, and logs.

### Business Value and Purpose
- **Democratizes Data Access**: Enables non-technical users to access and analyze data without needing to know MongoDB query syntax.
- **Accelerates Decision-Making**: Reduces dependency on technical staff for data retrieval, speeding up business processes.
- **Ensures Safety and Explainability**: Validates queries for safety, provides logs and reasoning steps for transparency.
- **Optimizes Performance**: Uses intelligent caching to reduce costs and improve response times.

### Key Features and Capabilities
- Natural language query interface (via HTTP API)
- AI-powered multi-agent workflow for query interpretation and generation
- Embedding-based and LLM-verified query caching
- Safe query validation (prevents destructive operations)
- Detailed logs and reasoning steps for each query
- Recent searches and autocomplete functionality

### Target Users and Use Cases
- **Business Analysts**: Self-service data exploration
- **Data Scientists**: Rapid prototyping and hypothesis testing
- **Product Managers**: Ad-hoc product usage analysis
- **Developers**: Integration into analytics dashboards or tools

### Business Impact and Outcomes
- Reduces time-to-insight for business users
- Lowers operational costs by minimizing technical bottlenecks
- Increases data accessibility and transparency
- Provides a foundation for advanced analytics and BI initiatives

---

## 2. System Overview (All Levels)

### High-Level Architecture Explanation
The system is built as a modular, service-oriented application with a FastAPI-based HTTP API as the main entry point. The core logic is orchestrated by a LangGraph-powered state machine, which coordinates a series of AI agents (implemented with LangChain and Azure OpenAI) to interpret, generate, and execute MongoDB queries. An embedding and vector search layer provides intelligent caching, while all operations are logged for transparency.

### System Capabilities and Functionality
- Accepts natural language queries via HTTP POST
- Validates input for safety
- Checks for similar past queries using embeddings and LLM verification
- If no cache hit, runs a multi-step agentic workflow:
  - Context detection (which collections/fields are relevant)
  - Query generation (LLM prompt to generate MongoDB query)
  - Query execution (runs query against MongoDB)
  - Query improvement (if execution fails, attempts to fix and retry)
- Stores new queries and embeddings for future cache hits
- Returns results, logs, and reasoning steps to the user

### How the System Works (Conceptual Flow)
1. User submits a query (e.g., "Show me all orders from last month")
2. System validates the input for safety
3. Checks if a similar query has been processed before (embedding/LLM cache)
4. If found, returns cached result; if not, runs agentic workflow
5. Each agent performs a specific task (context detection, query generation, etc.)
6. Executes the generated MongoDB query
7. If execution fails, attempts to improve the query and retry
8. Stores the new query and embedding in the cache
9. Returns the result, logs, and reasoning steps

### Key Components and Their Roles
- **FastAPI HTTP API**: Entry point for user queries and retrieval endpoints
- **Agentic Workflow (LangGraph)**: Orchestrates the multi-step query processing
- **AI Agents (LangChain, Azure OpenAI)**: Perform context detection, query generation, and improvement
- **Embedding and Cache Layer**: Provides semantic caching using vector search and LLM verification
- **MongoDB Data Access Layer**: Executes validated queries against MongoDB
- **Input Validation**: Ensures queries are safe
- **Logging**: Captures all steps and errors for transparency

---

## 3. Detailed Architecture (Architect & Developer Level)

### Complete Architecture Pattern Explanation
The architecture is a hybrid of service-oriented and agentic workflow patterns, with a strong emphasis on modularity and explainability. The workflow is defined as a state machine (LangGraph), where each node represents a distinct agent or operation. The system is designed for extensibility (new agents can be added), safety (input validation, query safety checks), and performance (cache-first logic, embedding-based similarity search).

### Technology Stack Breakdown
- **Languages**: Python
- **Frameworks**: FastAPI (API), LangChain (LLM orchestration), LangGraph (state machine)
- **AI/ML**: Azure OpenAI (LLM, embeddings), SentenceTransformers (embeddings), faiss (vector similarity)
- **Databases**: MongoDB (data), Supabase (vector DB/cache)
- **Infrastructure**: Docker, Docker Compose
- **Libraries**: Uvicorn, pymongo, marshmallow, pydantic, python-dotenv

### System-Level Design Decisions
- **Agentic Workflow**: Each step (cache check, context detection, query generation, execution, improvement) is modular and independently testable.
- **Cache-First**: Embedding and LLM-based cache check before invoking expensive LLM operations.
- **Explainability**: All reasoning steps and logs are returned to the user.
- **Safety**: Input validation and query safety checks prevent destructive operations.
- **Extensibility**: New agents or workflow steps can be added with minimal changes.

### Scalability and Performance Considerations
- **Caching**: Reduces LLM calls and database load by reusing similar queries.
- **Asynchronous Execution**: Workflow can be run asynchronously (see execute_workflow).
- **Vector Search**: faiss and Supabase enable fast similarity search for cache hits.
- **Potential Bottlenecks**: LLM used for every cache check (performance/cost risk).

### Integration Points
- **Azure OpenAI**: For LLM and embedding generation
- **Supabase**: For vector storage and cache
- **MongoDB**: For data storage and query execution

---

## 4. Component Details (Developer & Architect Level)

### FastAPI HTTP API
- **Purpose**: Exposes endpoints for query submission, recent searches, and autocomplete.
- **Implementation**: `src/backend/routes/query.py`, `src/backend/__init__.py`
- **APIs**:
  - `POST /query`: Main entry point for natural language queries
  - `GET /api/recent-searches`: Returns recent queries
  - `GET /autocomplete/`: Returns autocomplete suggestions
- **Key Files**: `src/backend/routes/query.py`, `src/backend/schema/query.py`
- **Data Structures**: `QuerySchema` (Pydantic/Marshmallow)
- **Error Handling**: try/except in route handlers, returns HTTP 400/500
- **Dependencies**: FastAPI, Pydantic, Marshmallow

### Agentic Workflow (LangGraph State Machine)
- **Purpose**: Orchestrates the multi-step query processing
- **Implementation**: `src/ai/graphs/graph_builder.py`, `src/ai/graphs/graph_executor.py`
- **Key Classes/Functions**:
  - `execute_workflow(state)` (src/ai/graphs/graph_executor.py)
  - StateGraph definition (src/ai/graphs/graph_builder.py)
- **Data Structures**: `GenerationState` (src/ai/state/generation_state.py)
- **Error Handling**: Errors at each node update state with error details
- **Dependencies**: LangGraph, LangChain

### AI Agents
- **Purpose**: Modular agents for context detection, query generation, and improvement
- **Implementation**: `src/ai/agents/generation/`
- **Key Classes/Functions**:
  - `ContextDetectionAgent.context_detection(state)`
  - `QueryGenerationAgent.query_generator(state)`
  - `ImprovementAgent.improve_query(state)`
- **Key Files**: `src/ai/agents/generation/context_detection_agent.py`, `query_generation_agent.py`, `improvement_agent.py`, `prompts/prompts.py`
- **Data Structures**: State dict (varies by agent)
- **Error Handling**: Each agent updates state with error details
- **Dependencies**: LangChain, Azure OpenAI, SentenceTransformers

### Embedding and Cache Layer
- **Purpose**: Embedding generation, vector similarity search, cache storage
- **Implementation**: `src/backend/embedding/embedding.py`
- **Key Functions**:
  - `Embedding.get_similar_with_enhanced_verification(user_input, org_id, ...)`
  - `Embedding.store_embeddings(data)`
- **Data Structures**: Embedding records (query, embedding vector, metadata)
- **Error Handling**: Handles Supabase and embedding errors
- **Dependencies**: SentenceTransformers, Azure OpenAI, faiss, Supabase

### MongoDB Data Access Layer
- **Purpose**: Executes MongoDB queries and aggregation pipelines
- **Implementation**: `src/backend/db/mongodb.py`
- **Key Functions**:
  - `MongoEngine.execute_query(state)`
- **Data Structures**: MongoDB query dicts
- **Error Handling**: Catches and logs MongoDB errors
- **Dependencies**: pymongo

### Input Validation
- **Purpose**: Validates user input for forbidden/destructive keywords
- **Implementation**: `src/backend/validators/validators.py`
- **Key Functions**:
  - `validate_user_input(query)`
- **Data Structures**: List of forbidden keywords
- **Error Handling**: Returns error message if forbidden keyword found

### Logging
- **Purpose**: Logs workflow steps, errors, and reasoning
- **Implementation**: `logger.py`
- **Key Functions**:
  - `log_to_state(state, message)`
- **Data Structures**: Log entries in state dict

---

## 5. How It Works (All Levels) - CRITICAL SECTION

### 5.1 User Request Flow (Step-by-Step)
1. **User submits query**: Sends POST request to `/query` with a natural language query (see `src/backend/routes/query.py`)
2. **Input validation**: `validate_user_input` checks for forbidden keywords (see `src/backend/validators/validators.py`)
3. **State initialization**: State object is created (see `src/ai/state/generation_state.py`)
4. **Workflow execution**: `execute_workflow(state)` is called (see `src/ai/graphs/graph_executor.py`)
5. **Cache check**: `Embedding.get_similar_with_enhanced_verification` checks for similar queries (see `src/backend/embedding/embedding.py`)
6. **Decision point**: If cache hit, state is updated with cached query and goes to execution; if miss, proceeds to context detection
7. **Context detection**: `ContextDetectionAgent.context_detection` uses LLM to determine relevant collections (see `src/ai/agents/generation/context_detection_agent.py`)
8. **Query generation**: `QueryGenerationAgent.query_generator` uses LLM to generate MongoDB query (see `src/ai/agents/generation/query_generation_agent.py`)
9. **Query execution**: `MongoEngine.execute_query` runs the query (see `src/backend/db/mongodb.py`)
10. **Error handling**: If execution fails and retry count < 1, `ImprovementAgent.improve_query` attempts to fix and retry (see `src/ai/agents/generation/improvement_agent.py`)
11. **Cache update**: If not from cache and results found, `Embedding.store_embeddings` saves the new query and embedding
12. **Response**: Returns data, query, logs, and reasoning steps to user

### 5.2 LangGraph/Agent State Machine Flow
- **Nodes**:
  - `check_cache` (handle_cache_check): Checks for similar queries in cache
  - `detect_context` (ContextDetectionAgent.context_detection): Determines relevant collections
  - `generate` (QueryGenerationAgent.query_generator): Generates MongoDB query
  - `execute` (MongoEngine.execute_query): Executes query
  - `improve` (ImprovementAgent.improve_query): Attempts to fix failed queries
- **Edge Conditions**:
  - From `check_cache`: If cache hit → `execute`; if miss → `detect_context`
  - From `execute`: If execution_status True or retry_count >= 1 → END; else → `improve`
  - From `improve`: Always returns to `execute`
- **State Updates**:
  - At each node, state dict is updated with new fields (e.g., `mongo_query`, `collection_name`, `data`, `error`)
- **Tool Calls**:
  - Embedding similarity search, LLM calls for context detection, query generation, and improvement
- **Complete Flow Example**:
  - User asks "Show me all orders from last month"
  - System checks cache (no hit)
  - ContextDetectionAgent identifies `orders` collection
  - QueryGenerationAgent generates MongoDB query
  - MongoEngine executes query
  - If fails, ImprovementAgent attempts fix and retries
  - On success, result is returned and cached

### 5.3 Decision Logic
- **Cache lookup**: Always checked before LLM invocation
- **LLM invocation**: Used for context detection, query generation, improvement, and cache verification
- **Database query generation**: Only after context and reasoning steps are established
- **Agent selection**: Based on workflow node and state
- **Error handling**: If execution fails, improvement agent is invoked; if still fails, error is returned

### 5.4 Request/Response Patterns
- **Request**: POST `/query` with JSON body `{query: str, skip: int, limit: int, is_voice: bool, organization_id: str}`
- **Response**: JSON `{query, data, total, skip, limit, primary_collection, logs, reasoning_steps}`

### 5.5 Data Flow Through System
- User query → Input validation → State initialization → Workflow execution (cache check → context detection → query generation → execution → improvement) → Response
- State dict is updated at each step

### 5.6 Business Workflows
- Ad-hoc data exploration via natural language
- Recent search recall and autocomplete

### 5.7 Error Handling Flows
- Input validation errors: Return HTTP 400 with error message
- Query execution errors: Retry with improvement agent; if still fails, return error
- Supabase/embedding errors: Logged and returned in response

### 5.8 State Management
- State is maintained as a dict (see `GenerationState`)
- Passed between agents and updated at each node
- No session management; state is per-request

---

## 6. API Documentation (Developer Level)

### Endpoints
- **POST /query**
  - **Location**: `src/backend/routes/query.py: @query_router.post('/query')`
  - **Input**: `QuerySchema` (query: str, skip: int, limit: int, is_voice: bool, organization_id: str)
  - **Output**: JSON `{query, data, total, skip, limit, primary_collection, logs, reasoning_steps}`
  - **Behavior**: Processes user query through agentic workflow

- **GET /api/recent-searches**
  - **Location**: `src/backend/routes/query.py: @query_router.get('/api/recent-searches')`
  - **Input**: None
  - **Output**: JSON `{recent_searches: [...]}`
  - **Behavior**: Returns recent queries from cache

- **GET /autocomplete/**
  - **Location**: `src/backend/routes/query.py: @query_router.get('/autocomplete/')`
  - **Input**: `query: str, limit: int`
  - **Output**: List of suggestions
  - **Behavior**: Returns autocomplete suggestions from cached queries

### Authentication Mechanisms
- **Not implemented** (see anti-patterns)

### Rate Limiting
- **Not found in code**

### Error Responses
- HTTP 400/500 with error details in JSON

### API Versioning
- **Not found in code**

---

## 7. Data Architecture (Architect & Developer Level)

### Database Schemas and Relationships
- **MongoDB**: Collections and fields inferred by context detection agent; schema not explicitly defined in code
- **Supabase**: Stores query embeddings and metadata for cache; schema not detailed in code

### Data Access Patterns
- Queries generated by LLM agents, executed via pymongo
- Embedding similarity search via faiss and Supabase

### Caching Strategies
- Embedding-based similarity search for cache hits
- LLM verification for cache similarity
- New queries and embeddings stored after successful execution

### Data Validation Rules
- Input validated for forbidden/destructive keywords
- Query safety checks before execution

### Migration Strategies
- **Not found in code**

### Backup and Recovery
- **Not found in code**

---

## 8. Design Patterns (Architect Level)

### Patterns Used and Why
- **Agentic Workflow**: Modular, extensible, and explainable query processing
- **Chain-of-Responsibility**: Each agent modifies and passes state
- **Cache-First**: Optimizes performance and cost
- **Prompt Engineering**: Guides LLM behavior for each agent
- **Repository Pattern**: Abstracts MongoDB access
- **Dependency Injection**: Facilitates testing and modularity
- **State Machine**: Explicit workflow definition and conditional routing

### Architectural Decisions and Trade-Offs
- **Modularity vs. Complexity**: Agentic workflow increases modularity but adds complexity
- **Performance vs. Cost**: LLM used for every cache check can be costly
- **Safety vs. Flexibility**: Strict input validation prevents destructive queries

### Pattern Interactions
- Agentic workflow and state machine patterns are tightly coupled
- Cache-first pattern interacts with agentic workflow to optimize performance

---

## 9. Security (All Levels)

### Security Mechanisms
- **Input Validation**: Prevents unsafe/destructive queries
- **CORS Policy**: Open (origins = ['*']), security risk in production
- **No Authentication/Authorization**: Anyone can access API (anti-pattern)
- **Secrets in Logs**: Supabase URL and key printed to stdout (anti-pattern)

### Authentication and Authorization
- **Not implemented**

### Data Protection
- **Not found in code**

---

## 10. Development Guide (Developer Level)

### Setup and Installation
1. **Clone repository**
2. **Install dependencies**: `pip install -r requirements.txt`
3. **Set environment variables**: Copy `.env.example` to `.env` and fill in required values (Supabase, MongoDB, Azure OpenAI)
4. **Run with Docker**: `docker-compose up` (if using Docker)
5. **Run locally**: `uvicorn src/backend/__init__:app --reload`

### Configuration
- **Supabase**: URL and key in `.env`
- **MongoDB**: Connection string in `.env`
- **Azure OpenAI**: API key and endpoint in `.env`

### Running Tests
- `tests/tests.py` is empty; no automated tests implemented

### Debugging Tips
- Check logs in `logger.py`
- Inspect state dict at each workflow step
- Review error messages in API responses

### Common Issues
- **No authentication**: API is open; secure before production
- **Secrets in logs**: Remove print statements for Supabase credentials
- **Performance bottleneck**: LLM used for every cache check

---

## 11. File Map (All Levels)

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
- **API**: `src/backend/routes/query.py`, `src/backend/__init__.py`
- **Workflow**: `src/ai/graphs/graph_builder.py`, `src/ai/graphs/graph_executor.py`
- **Agents**: `src/ai/agents/generation/context_detection_agent.py`, `query_generation_agent.py`, `improvement_agent.py`
- **Prompts**: `src/ai/agents/prompts/prompts.py`
- **Embedding/Cache**: `src/backend/embedding/embedding.py`
- **DB Access**: `src/backend/db/mongodb.py`
- **Validation**: `src/backend/validators/validators.py`
- **Logging**: `logger.py`
- **Configuration**: `.env.example`, `src/backend/config/config.py`, `src/ai/config/config.py`
- **Tests**: `tests/tests.py` (empty)
- **Documentation**: `README.md`, `Code-Analysis-Review.md`

---

# End of Handbook
