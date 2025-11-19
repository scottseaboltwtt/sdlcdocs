# undefined/undefined - Developer Handbook

**Analysis Date:** 2025-11-19T16:38:53.419Z
**Branch:** undefined
**Total Files Analyzed:** 0

---

# AI-Powered Natural Language to MongoDB Query Service

## 1. Executive Summary (Business Level)

### What the Software Does
This software provides an AI-powered service that allows users to query MongoDB databases using natural language. Users can type or speak queries such as "Show me jobs assigned to Anthony Adkins," and the system will interpret the request, generate the appropriate MongoDB query, execute it, and return the results—all without requiring the user to know any database query language.

### Business Value and Purpose
The system democratizes access to complex data by removing technical barriers. It enables business analysts, managers, and support staff to retrieve and analyze data quickly, reducing dependency on technical teams and accelerating decision-making. By automating query generation and leveraging AI for context understanding, the software increases productivity and data-driven agility.

### Key Features and Capabilities
- **Natural Language Querying**: Users interact with data using plain English.
- **AI-Powered Query Generation**: Uses LLMs (Azure OpenAI via LangChain) to generate and improve queries.
- **Semantic Caching**: Remembers similar queries for faster future responses using vector embeddings (Supabase).
- **Context Detection**: Identifies relevant collections and fields automatically.
- **Error Correction**: Automatically improves failing queries using LLM feedback.
- **Recent Searches and Autocomplete**: Enhances user experience with history and suggestions.
- **Detailed Logging**: Provides transparency and traceability for all actions.

### Target Users and Use Cases
- **Business Analysts**: Retrieve and analyze data for reporting.
- **Operations Managers**: Monitor metrics and generate operational insights.
- **Customer Support**: Quickly access customer/job data.
- **Data Scientists**: Perform exploratory data analysis.

### Business Impact and Outcomes
- **Reduced Time-to-Insight**: Immediate access to data without technical bottlenecks.
- **Lower Support Costs**: Fewer requests to technical teams for data access.
- **Increased Data Literacy**: Broader organizational access to data.
- **Improved Decision-Making**: Faster, more informed business actions.

---

## 2. System Overview (All Levels)

### High-Level Architecture Explanation
The system is built as a modular, service-oriented application. At its core is a FastAPI web server that exposes HTTP endpoints for natural language queries. Incoming requests are processed through a multi-step, agentic workflow orchestrated by a LangGraph state machine. This workflow includes semantic caching, context detection, query generation, execution, and error correction, all powered by AI agents using LangChain and Azure OpenAI. The system stores and retrieves data from MongoDB and uses Supabase for vector-based semantic caching.

### System Capabilities and Functionality
- Accepts natural language queries via HTTP API
- Validates and processes input
- Checks for similar cached queries using embeddings and LLM verification
- If no cache hit, detects context and generates MongoDB queries using LLMs
- Executes queries on MongoDB
- Improves queries if execution fails
- Returns results, logs, and reasoning steps
- Provides recent searches and autocomplete endpoints

### How the System Works (Conceptual Flow)
1. User submits a query via API.
2. System validates input and checks for similar cached queries.
3. If cache miss, detects context and generates a new query using LLM.
4. Executes the query on MongoDB.
5. If execution fails, improves the query using LLM feedback.
6. Returns results and logs to the user.
7. Stores new queries and embeddings for future cache hits.

### Key Components and Their Roles
- **API Layer**: Handles HTTP requests and responses.
- **Agentic Workflow Layer**: Orchestrates the multi-step AI workflow.
- **AI Agents**: Perform context detection, query generation, and improvement.
- **Semantic Caching Layer**: Stores and retrieves query embeddings.
- **Database Access Layer**: Executes MongoDB queries.
- **Validation Layer**: Ensures input and query safety.
- **Logging Layer**: Maintains logs and reasoning steps.

---

## 3. Detailed Architecture (Architect & Developer Level)

### Complete Architecture Pattern Explanation
The architecture follows an agentic, service-oriented pattern with a state machine (LangGraph) orchestrating the workflow. Each step in the workflow is handled by a specialized agent or function, and state is passed and updated throughout the process. The system is modular, with clear separation of concerns:
- **API Layer**: FastAPI endpoints (src/backend/routes/query.py)
- **Workflow Orchestration**: LangGraph state machine (src/ai/graphs/graph_builder.py)
- **Agents**: LLM-powered context detection, query generation, and improvement (src/ai/agents/generation/*)
- **Semantic Caching**: Embedding generation and Supabase storage (src/backend/embedding/embedding.py)
- **Database Access**: MongoDB execution (src/backend/db/mongodb.py)
- **Validation**: Input and query safety (src/backend/validators/validators.py)
- **Logging**: Observability (logger.py)

### Technology Stack Breakdown
- **Languages**: Python
- **Frameworks**: FastAPI, LangChain, LangGraph
- **Libraries**: Uvicorn, pymongo, sentence-transformers, faiss-cpu, marshmallow, pydantic, python-dotenv
- **Databases**: MongoDB (data), Supabase (vector DB for embeddings)
- **Infrastructure**: Docker, Docker Compose
- **AI/ML**: Azure OpenAI (LLM), LangChain (agent orchestration), sentence-transformers (embeddings), faiss-cpu (vector search)

### System-Level Design Decisions
- **Agentic Workflow**: Modular, extensible, and maintainable via LangGraph state machine.
- **Semantic Caching**: Reduces LLM calls and improves performance for repeated/similar queries.
- **Prompt Engineering**: Custom prompts for each agent step for accuracy and control.
- **Repository Pattern**: Abstracts database access for flexibility.
- **Factory Pattern**: LLM instantiation for easy swapping/configuration.
- **Detailed Logging**: Ensures traceability and debuggability.

### Scalability and Performance Considerations
- **Semantic Caching**: Reduces LLM and DB load for repeated queries.
- **Agent Modularity**: Agents can be scaled or replaced independently.
- **Stateless API**: Each request is independent, enabling horizontal scaling.
- **Dockerized Deployment**: Supports container orchestration.
- **Potential Bottlenecks**: LLM calls for every cache check (expensive), no async DB operations (not found in code).

### Integration Points
- **Supabase**: For vector embedding storage and retrieval.
- **Azure OpenAI**: For LLM-powered agents.
- **MongoDB**: For data storage and query execution.

---

## 4. Component Details (Developer & Architect Level)

### FastAPI Application Layer
- **Purpose**: Exposes HTTP endpoints for query processing, recent searches, and autocomplete.
- **Implementation**: Defined in `src/backend/routes/query.py` and initialized in `src/backend/__init__.py`.
- **Key APIs**:
  - `POST /query`: Main endpoint for natural language queries.
  - `GET /api/recent-searches`: Returns recent queries.
  - `GET /autocomplete/`: Provides autocomplete suggestions.
- **Key Files**:
  - `src/backend/routes/query.py`: Endpoint definitions.
  - `src/backend/__init__.py`: FastAPI app initialization.
  - `run.py`: Entrypoint for running the app.
- **Data Structures**:
  - `QuerySchema` (src/backend/schema/query.py): Defines request schema.
- **Error Handling**:
  - Input validation errors return HTTP 400.
  - Generic exceptions return HTTP 500.
  - Errors logged via `logger.py`.

### Agentic Workflow Layer
- **Purpose**: Orchestrates the multi-step workflow using LangGraph.
- **Implementation**: Defined in `src/ai/graphs/graph_builder.py` (workflow definition) and `src/ai/graphs/graph_executor.py` (execution).
- **Workflow Nodes**:
  - `check_cache`: Embedding-based cache check.
  - `detect_context`: Context detection agent.
  - `generate`: Query generation agent.
  - `execute`: MongoDB query execution.
  - `improve`: Query improvement agent.
- **Key Files**:
  - `src/ai/graphs/graph_builder.py`: State machine definition.
  - `src/ai/graphs/graph_executor.py`: Workflow execution logic.
- **Data Structures**:
  - `GenerationState` (src/ai/state/generation_state.py): State dict passed through workflow.
- **Error Handling**:
  - Generic exception handling, errors logged.

### AI Agents
- **Purpose**: LLM-powered agents for context detection, query generation, and improvement.
- **Implementation**:
  - `ContextDetectionAgent` (src/ai/agents/generation/context_detection_agent.py): Detects relevant collections and reasoning steps.
  - `QueryGenerationAgent` (src/ai/agents/generation/query_generation_agent.py): Generates MongoDB queries.
  - `ImprovementAgent` (src/ai/agents/generation/improvement_agent.py): Improves failing queries.
- **Key Files**:
  - `src/ai/agents/generation/context_detection_agent.py`
  - `src/ai/agents/generation/query_generation_agent.py`
  - `src/ai/agents/generation/improvement_agent.py`
  - `src/ai/agents/prompts/prompts.py`: Prompt templates.
- **Data Structures**:
  - State dict (GenerationState).
- **Error Handling**:
  - Error feedback loop for query improvement.
  - Logs errors to state and logger.

### Semantic Caching Layer
- **Purpose**: Checks for semantically similar queries using vector embeddings and LLM verification. Stores new queries and embeddings in Supabase.
- **Implementation**: `src/backend/embedding/embedding.py`.
- **Key Functions**:
  - `check_query_cache_with_llm_verification(user_query, org_id)`: Checks for similar cached queries.
  - `store_embeddings(data)`: Stores new query and embedding.
- **Data Structures**:
  - Embedding vectors, Supabase storage schema.
- **Error Handling**:
  - Logs errors to state and logger.

### Database Access Layer
- **Purpose**: Executes validated MongoDB queries, formats results, and enforces query safety.
- **Implementation**: `src/backend/db/mongodb.py`.
- **Key Functions**:
  - `execute_query(state)`: Executes MongoDB query and formats results.
- **Data Structures**:
  - MongoDB query documents, result sets.
- **Error Handling**:
  - Returns execution_status in state, logs errors.

### Validation Layer
- **Purpose**: Validates user input and enforces query safety.
- **Implementation**: `src/backend/validators/validators.py`.
- **Key Functions**:
  - `validate_user_input`: Validates input for forbidden keywords.
  - `validate_generated_mongo_query`: Validates generated MongoDB queries.
  - `is_safe_aggregation_pipeline`: Checks aggregation pipeline safety.
- **Error Handling**:
  - Raises exceptions on invalid input, logs errors.

### Logging Layer
- **Purpose**: Logs all steps, errors, and reasoning steps.
- **Implementation**: `logger.py`.
- **Key Functions**:
  - `log_to_state`: Appends log messages to state and file.
- **Data Structures**:
  - Log entries in state['logs'].
- **Error Handling**:
  - Logs errors for debugging.

---

## 5. How It Works (All Levels) - CRITICAL SECTION

### 5.1 User Request Flow (Step-by-Step)
1. **User submits query**: User sends a POST request to `/query` with a natural language query (see `src/backend/routes/query.py`).
2. **Input validation**: `validate_user_input` checks for forbidden/destructive keywords (see `src/backend/validators/validators.py`).
3. **State initialization**: State dict is initialized with user_query, skip, limit, org_id, etc. (see `src/ai/state/generation_state.py`).
4. **Workflow execution**: `execute_workflow(state)` is called (see `src/ai/graphs/graph_executor.py`).
5. **Cache check**: `check_query_cache_with_llm_verification` checks for similar cached queries using embeddings and LLM (see `src/backend/embedding/embedding.py`).
   - If cache hit: state updated with cached query, collection_name, aggregate, from_cache=True; go to execute.
   - If cache miss: state updated with reasoning_steps; go to detect_context.
6. **Context detection**: `ContextDetectionAgent.context_detection` identifies relevant collections and reasoning steps (see `src/ai/agents/generation/context_detection_agent.py`).
7. **Query generation**: `QueryGenerationAgent.query_generator` generates MongoDB query using LLM and prompt (see `src/ai/agents/generation/query_generation_agent.py`).
8. **Query execution**: `MongoEngine.execute_query` runs query, updates state with data, execution_status (see `src/backend/db/mongodb.py`).
   - If execution_status is False and retry_count < 1: go to improve.
   - If execution_status True or retry_count >= 1: end.
9. **Query improvement**: `ImprovementAgent.improve_query` tries to fix query using LLM and error feedback (see `src/ai/agents/generation/improvement_agent.py`).
   - Increments retry_count, loops back to execute.
10. **Embedding storage**: If not from_cache and results found, `store_embeddings` saves query/embedding in Supabase (see `src/backend/embedding/embedding.py`).
11. **Response**: Returns results, logs, reasoning_steps to user.

### 5.2 LangGraph/Agent State Machine Flow
- **Nodes**:
  - `check_cache`: Embedding/LLM cache check.
  - `detect_context`: Context detection agent.
  - `generate`: Query generation agent.
  - `execute`: MongoDB execution.
  - `improve`: Query improvement agent.
- **Edge Conditions**:
  - `check_cache` → `execute` (if cache hit)
  - `check_cache` → `detect_context` (if cache miss)
  - `detect_context` → `generate`
  - `generate` → `execute`
  - `execute` → END (if execution_status True or retry_count >= 1)
  - `execute` → `improve` (if execution_status False and retry_count < 1)
  - `improve` → `execute` (retry)
- **State Updates**:
  - At each node, state dict is updated with new fields (mongo_query, collection_name, data, logs, etc.).
- **Tool Calls**:
  - LLMs called via LangChain for context detection, query generation, and improvement.
  - Embedding generation and vector search for cache check.
- **Example Flow**:
  - User asks "Show me jobs assigned to Anthony Adkins".
  - `check_cache`: no match.
  - `detect_context`: identifies 'job-summary-data' as relevant.
  - `generate`: LLM generates MongoDB query.
  - `execute`: runs query, gets results.
  - `store_embeddings`: saves query for future cache.
  - Returns results to user.

### 5.3 Decision Logic
- **Cache Lookup**: Always checked first; if hit, skips LLM query generation.
- **LLM Invocation**: Used for context detection, query generation, improvement, and cache verification.
- **Query Execution**: Only after query is generated or retrieved from cache.
- **Agent Selection**: Based on workflow node (state machine).
- **Error Handling**: If execution fails, improvement agent is invoked once; if still fails, returns error.

### 5.4 Request/Response Patterns
- **POST /query**:
  - Request: JSON (QuerySchema)
  - Response: JSON with query, data, total, skip, limit, primary_collection, logs, reasoning_steps
- **GET /api/recent-searches**:
  - Response: JSON with recent_searches
- **GET /autocomplete/**:
  - Response: List of suggestions

### 5.5 Data Flow Through System
- User query → API → State dict → LangGraph workflow (cache check → context detection → query generation → execution → improvement) → MongoDB/Supabase → Response

### 5.6 Business Workflows
- Natural language query to data retrieval
- Recent searches retrieval
- Autocomplete suggestions

### 5.7 Error Handling Flows
- Input validation errors: HTTP 400
- Query execution errors: improvement agent invoked, then error returned if still fails
- All errors logged to state and file

### 5.8 State Management
- State dict (GenerationState) is passed and updated through the workflow
- Logs and reasoning steps are appended to state
- No session management (stateless API)

---

## 6. API Documentation (Developer Level)

### Endpoints
- **POST /query**
  - **Input**: QuerySchema (query: str, skip: int, limit: int, is_voice: bool, organization_id: str)
  - **Output**: JSON {query, data, total, skip, limit, primary_collection, logs, reasoning_steps}
  - **Purpose**: Main endpoint for natural language query to MongoDB data
- **GET /api/recent-searches**
  - **Input**: None
  - **Output**: JSON {recent_searches: list}
  - **Purpose**: Returns recent queries from Supabase cache
- **GET /autocomplete/**
  - **Input**: query: str, limit: int
  - **Output**: List of suggestions
  - **Purpose**: Autocomplete suggestions for user queries

### Authentication Mechanisms
- **Not found in code**: No authentication or authorization implemented.

### Rate Limiting
- **Not found in code**

### Error Responses
- **400**: Input validation errors
- **500**: Generic server errors

### API Versioning
- **Not found in code**

---

## 7. Data Architecture (Architect & Developer Level)

### Database Schemas and Relationships
- **MongoDB**: Used for data storage; schema details not found in code (hard-coded schema in SCHEMA_DICT in src/ai/config/config.py)
- **Supabase**: Used for storing query embeddings and metadata for semantic caching

### Data Access Patterns
- Repository pattern for MongoDB access (src/backend/db/mongodb.py)
- Embedding-based retrieval for cache (src/backend/embedding/embedding.py)

### Caching Strategies
- Semantic caching using vector embeddings and LLM verification (src/backend/embedding/embedding.py)
- Cache lookup before LLM query generation

### Data Validation Rules
- Input validation for forbidden keywords (src/backend/validators/validators.py)
- Query safety validation (src/backend/validators/validators.py)

### Migration Strategies
- **Not found in code**

### Backup and Recovery
- **Not found in code**

---

## 8. Design Patterns (Architect Level)

### Patterns Used and Why
- **Agentic Workflow (State Machine)**: Modular, extensible workflow orchestration (src/ai/graphs/graph_builder.py)
- **Prompt Engineering**: Custom prompts for LLM accuracy (src/ai/agents/prompts/prompts.py)
- **Semantic Caching with Vector Embeddings**: Performance optimization for repeated queries (src/backend/embedding/embedding.py)
- **Repository Pattern**: Abstracts DB access (src/backend/db/mongodb.py)
- **Factory Pattern**: LLM instantiation (src/ai/llm/llm.py)
- **Chain of Responsibility**: Workflow passes state through agent nodes (src/ai/graphs/graph_builder.py)
- **Callback/Observer for Logging**: Observability (logger.py)

### Architectural Decisions and Trade-Offs
- **Agentic workflow**: Extensible but may add complexity
- **Semantic caching**: Improves performance but adds storage and LLM verification cost
- **Open CORS**: Increases accessibility but reduces security

### Pattern Interactions
- State machine coordinates agent chain; each agent uses prompt engineering; semantic cache is checked before agent chain

---

## 9. Security (All Levels)

### Security Mechanisms
- **Input Validation**: Checks for forbidden/destructive keywords (src/backend/validators/validators.py)
- **Query Safety Enforcement**: Validates generated MongoDB queries (src/backend/validators/validators.py)
- **Open CORS**: All origins allowed (src/backend/__init__.py)
- **No Authentication/Authorization**: Not implemented (not found in code)
- **Secrets Exposure**: Secrets printed to stdout (src/backend/config/config.py)

### Authentication and Authorization
- **Not found in code**

### Data Protection
- **Not found in code**

---

## 10. Development Guide (Developer Level)

### Setup and Installation
1. **Clone the repository**
2. **Install dependencies**: `pip install -r requirements.txt`
3. **Set up environment variables**: Copy `.env.example` to `.env` and fill in required values
4. **Configure MongoDB and Supabase**: Ensure access credentials are set in `.env`
5. **Run the application**: `python run.py` (uses Uvicorn to serve FastAPI app)
6. **Dockerized deployment**: Use `docker-compose up` for containerized setup

### Configuration
- **Environment variables**: See `.env.example`
- **Secrets**: Set in `.env` (note: secrets are printed to stdout in code)

### Running Tests
- **Not found in code**: `tests/tests.py` is empty

### Debugging Tips
- Check logs in state and file (logger.py)
- Review logs and reasoning_steps in API responses

### Common Issues
- **No authentication**: API is open by default
- **Secrets exposure**: Ensure `.env` is not committed
- **LLM cost**: LLM used for every cache check (can be expensive)

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
- **API Layer**: `src/backend/routes/query.py`, `src/backend/__init__.py`, `run.py`
- **Agentic Workflow**: `src/ai/graphs/graph_builder.py`, `src/ai/graphs/graph_executor.py`, `src/ai/state/generation_state.py`
- **AI Agents**: `src/ai/agents/generation/context_detection_agent.py`, `src/ai/agents/generation/query_generation_agent.py`, `src/ai/agents/generation/improvement_agent.py`, `src/ai/agents/prompts/prompts.py`
- **Semantic Caching**: `src/backend/embedding/embedding.py`
- **Database Access**: `src/backend/db/mongodb.py`
- **Validation**: `src/backend/validators/validators.py`
- **Logging**: `logger.py`
- **Configuration**: `.env.example`, `src/backend/config/config.py`, `src/ai/config/config.py`
- **Tests**: `tests/tests.py` (empty)
- **Documentation**: `README.md`, `Code-Analysis-Review.md`

---

# End of Handbook
