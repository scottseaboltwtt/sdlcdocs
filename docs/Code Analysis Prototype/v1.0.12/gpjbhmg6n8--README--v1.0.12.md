# undefined/undefined - Developer Handbook

**Analysis Date:** 2025-11-19T19:03:57.623Z
**Branch:** undefined
**Total Files Analyzed:** 0

---

# AI-Powered Natural Language Query System Handbook

## 1. Executive Summary (Business Level)

### What the Software Does
This software provides an AI-powered natural language interface for querying complex MongoDB databases, specifically tailored for the oil & gas domain. It allows business users to ask questions in plain English and receive structured, explainable results, eliminating the need for technical query writing and accelerating data-driven decision making.

### Business Value and Purpose
- **Democratizes Data Access**: Empowers non-technical users to extract insights from large, complex datasets without SQL or MongoDB expertise.
- **Accelerates Decision Making**: Reduces turnaround time for data requests, enabling faster business decisions.
- **Reduces IT Burden**: Minimizes the need for custom report development and ad-hoc query support from technical teams.
- **Ensures Data Security**: Validates queries to prevent destructive operations.

### Key Features and Capabilities
- Natural language query interface (via HTTP API)
- AI-powered translation of user intent to MongoDB queries
- Semantic caching to avoid redundant LLM/database calls
- Explainable, step-by-step reasoning for each query
- Robust error handling and query improvement
- Recent search and autocomplete support

### Target Users and Use Cases
- **Business Users**: Quickly access operational data (e.g., job assignments, production stats)
- **Data Analysts**: Validate and refine queries for deeper insights
- **Developers**: Extend system capabilities, add new data sources
- **Architects**: Integrate with enterprise data platforms

### Business Impact and Outcomes
- Faster, more accurate business insights
- Reduced dependency on technical staff for data access
- Improved data governance and auditability

---

## 2. System Overview (All Levels)

### High-Level Architecture Explanation
The system is architected as a modular, service-oriented platform with clear separation between API routing, agent orchestration, embedding/caching, and database access. It leverages:
- **FastAPI** for HTTP API exposure
- **LangChain** and **LangGraph** for agent orchestration and LLM integration
- **Azure OpenAI** for LLM and embedding generation
- **Supabase** for vector storage and semantic cache
- **MongoDB** for operational data storage

### System Capabilities and Functionality
- Accepts natural language queries via HTTP POST
- Validates input for security
- Checks semantic cache for similar queries
- Orchestrates multi-step agentic workflow (context detection, query generation, execution, improvement)
- Returns structured results, reasoning steps, and logs
- Supports recent searches and autocomplete

### How the System Works (Conceptual Flow)
1. User submits a query (e.g., "Show me jobs assigned to Anthony Adkins")
2. API validates input and initializes workflow state
3. System checks semantic cache for similar queries
4. If cache miss, orchestrates agentic workflow:
   - Detects context (relevant collections)
   - Generates MongoDB query using LLM
   - Executes query
   - If failure, attempts improvement
5. Stores new queries/embeddings for future cache hits
6. Returns results and reasoning to user

### Key Components and Their Roles
- **API Layer**: Handles HTTP requests, validation, and response formatting
- **Agent Orchestration (LangGraph)**: Manages workflow as a state machine
- **Agents (Context Detection, Query Generation, Improvement)**: Specialized LLM-powered modules for each step
- **Embedding & Semantic Cache**: Stores and retrieves query embeddings for cache hits
- **MongoDB Data Access**: Executes validated queries
- **Prompt Templates**: Provides LLM prompts
- **Validation Layer**: Prevents forbidden operations
- **Logging & Observability**: Tracks workflow steps and errors

---

## 3. Detailed Architecture (Architect & Developer Level)

### Complete Architecture Pattern Explanation
The system follows a **modular, agentic, state-machine workflow** pattern:
- **Service-Oriented**: Each major function (API, agent orchestration, embedding, DB access) is a separate module
- **Agentic Workflow**: Specialized agents handle context detection, query generation, and improvement
- **State Machine Orchestration**: LangGraph defines the workflow as a state machine, enabling robust, explainable, and retryable flows
- **Semantic Caching**: Embeddings and LLM-based verification avoid redundant computation

### Technology Stack Breakdown
- **Languages**: Python
- **Frameworks**: FastAPI, LangChain, LangGraph
- **AI/ML**: Azure OpenAI (LLM, embeddings), SentenceTransformers, faiss-cpu
- **Databases**: MongoDB (operational data), Supabase (vector storage)
- **Infrastructure**: Docker, Docker Compose, Azure OpenAI
- **Libraries**: pymongo, marshmallow, pydantic, python-dotenv, tiktoken, langfuse

### System-Level Design Decisions
- **Agentic Modularity**: Each agent is a class with a single responsibility, enabling easy extension and maintenance
- **State Machine Workflow**: LangGraph enables explicit, explainable transitions and retry logic
- **Semantic Caching**: Reduces LLM and DB load, improving performance and cost
- **Prompt Engineering**: Centralized prompt templates for consistency
- **Observability**: Integrated logging and langfuse for traceability

### Scalability and Performance Considerations
- **Semantic Cache**: Reduces LLM/database calls for repeated queries
- **Asynchronous Workflow**: FastAPI and async workflow execution support concurrent requests
- **Externalized State**: Embeddings and cache stored in Supabase for scalability
- **Potential Bottlenecks**: LLM used for every cache check (performance/cost risk)

### Integration Points
- **Azure OpenAI**: LLM and embedding generation
- **Supabase**: Vector storage for semantic cache
- **MongoDB**: Data storage and query execution
- **langfuse**: Observability and logging

---

## 4. Component Details (Developer & Architect Level)

### API Layer
- **Purpose**: Exposes HTTP endpoints for queries, recent searches, and autocomplete
- **Implementation**: FastAPI app (src/backend/routes/query.py)
- **Key Functions**:
  - `query`: POST /query (main entry point)
  - `get_recent_searches`: GET /api/recent-searches
  - `get_autocomplete_suggestions`: GET /autocomplete/
- **Validation**: Uses `validate_user_input` (src/backend/validators/validators.py)
- **Error Handling**: try/except blocks, returns HTTP errors
- **Dependencies**: Calls workflow executor, embedding service

### Agent Orchestration (LangGraph Workflow)
- **Purpose**: Orchestrates agentic workflow as a state machine
- **Implementation**: Defined in `GraphBuilder` (src/ai/graphs/graph_builder.py), executed by `execute_workflow` (src/ai/graphs/graph_executor.py)
- **Nodes**:
  - `check_cache`: Checks semantic cache
  - `detect_context`: ContextDetectionAgent
  - `generate`: QueryGenerationAgent
  - `execute`: MongoEngine
  - `improve`: ImprovementAgent
- **State Schema**: `GenerationState` (src/ai/state/generation_state.py)
- **Error Handling**: Supports retry on execution failure

### Context Detection Agent
- **Purpose**: Identifies relevant collections and reasoning steps
- **Implementation**: `ContextDetectionAgent.context_detection` (src/ai/agents/generation/context_detection_agent.py)
- **Technologies**: LangChain, Azure OpenAI, SentenceTransformers, faiss-cpu
- **Dependencies**: Prompt templates, Supabase
- **Error Handling**: Logs errors to state

### Query Generation Agent
- **Purpose**: Generates MongoDB queries from user intent
- **Implementation**: `QueryGenerationAgent.query_generator` (src/ai/agents/generation/query_generation_agent.py)
- **Technologies**: LangChain, Azure OpenAI
- **Dependencies**: Prompt templates
- **Error Handling**: Logs errors to state

### Improvement Agent
- **Purpose**: Repairs failed queries using LLM and error feedback
- **Implementation**: `ImprovementAgent.improve_query` (src/ai/agents/generation/improvement_agent.py)
- **Technologies**: LangChain, Azure OpenAI
- **Dependencies**: Prompt templates
- **Error Handling**: Known bug (checks keys in wrong dict)

### Embedding & Semantic Cache
- **Purpose**: Manages semantic caching of queries
- **Implementation**: `Embedding.check_query_cache_with_llm_verification`, `Embedding.store_embeddings` (src/backend/embedding/embedding.py)
- **Technologies**: Azure OpenAI, Supabase, SentenceTransformers, faiss-cpu
- **Error Handling**: LLM used for every cache check (performance/cost risk)

### MongoDB Data Access
- **Purpose**: Executes MongoDB queries
- **Implementation**: `MongoEngine.execute_query` (src/backend/db/mongodb.py)
- **Technologies**: pymongo
- **Error Handling**: Validates queries for safety

### Prompt Templates
- **Purpose**: Provides prompt templates for LLM calls
- **Implementation**: src/ai/agents/prompts/prompts.py

### Validation Layer
- **Purpose**: Validates user input for forbidden operations
- **Implementation**: `validate_user_input` (src/backend/validators/validators.py)

### Logging & Observability
- **Purpose**: Logs workflow steps and errors
- **Implementation**: logger.py, langfuse integration

---

## 5. How It Works (All Levels) - CRITICAL SECTION

### 5.1 User Request Flow (Step-by-Step)
1. **User submits query**: POST /query with QuerySchema (src/backend/routes/query.py:query)
2. **Input validation**: `validate_user_input` checks for forbidden keywords (src/backend/validators/validators.py)
3. **State initialization**: State dict created with user_query, org_id, skip, limit, retry_count=0, logs, reasoning_steps
4. **Workflow execution**: `execute_workflow(state)` called (src/ai/graphs/graph_executor.py)
5. **Cache check**: `Embedding.check_query_cache_with_llm_verification` (src/backend/embedding/embedding.py)
   - If cache hit (LLM-verified), state updated with cached mongo_query, collection_name, aggregate, from_cache=True
   - If cache miss, state updated with reasoning_steps from cache result
6. **Conditional edge**:
   - If cache hit: go to execute
   - If cache miss: go to detect_context
7. **Context detection**: `ContextDetectionAgent.context_detection` analyzes user_query (src/ai/agents/generation/context_detection_agent.py)
8. **Query generation**: `QueryGenerationAgent.query_generator` uses LLM and prompt (src/ai/agents/generation/query_generation_agent.py)
9. **Query execution**: `MongoEngine.execute_query` runs query (src/backend/db/mongodb.py)
10. **Conditional edge**:
    - If execution_status True or retry_count >= 1: END
    - Else, increment retry_count, go to improve
11. **Query improvement**: `ImprovementAgent.improve_query` uses LLM to repair query (src/ai/agents/generation/improvement_agent.py)
12. **After workflow**: If not from_cache, store query and embedding (Embedding.store_embeddings)
13. **Response**: Return with query, data, logs, reasoning_steps

### 5.2 LangGraph/Agent State Machine Flow
- **Nodes**:
  - `check_cache`: handle_cache_check
  - `detect_context`: ContextDetectionAgent.context_detection
  - `generate`: QueryGenerationAgent.query_generator
  - `execute`: MongoEngine.execute_query
  - `improve`: ImprovementAgent.improve_query
- **Edge Conditions**:
  - From `check_cache`: if cache hit → `execute`; else → `detect_context`
  - From `execute`: if execution_status True or retry_count >= 1 → END; else → `improve`
  - From `improve`: always → `execute`
- **State Updates**:
  - Each node updates relevant fields in state dict (e.g., mongo_query, collection_name, data, execution_status, logs)
- **Tool Calls**:
  - LLM called in context detection, query generation, improvement
  - Embedding service called in cache check
- **Example Flow**:
  - User asks: "Show me jobs assigned to Anthony Adkins"
  - `check_cache`: no LLM-verified match
  - `detect_context`: identifies job-summary-data as relevant
  - `generate`: LLM generates MongoDB aggregation query
  - `execute`: runs query, gets results
  - `store_embeddings`: stores query/embedding
  - Return results
  - If query fails: `improve` repairs query, `execute` tries again

### 5.3 Decision Logic
- **Cache Lookup**: Always checked first; LLM verifies semantic similarity
- **LLM Invocation**: Used for context detection, query generation, improvement
- **Database Query Generation**: Only after context detection and query generation
- **Agent Selection**: Workflow determines which agent runs based on state
- **Error Handling**: One retry via improvement agent; errors logged and returned

### 5.4 Request/Response Patterns
- **POST /query**: Accepts QuerySchema, returns JSON with query, data, logs, reasoning_steps
- **GET /api/recent-searches**: Returns recent queries
- **GET /autocomplete/**: Returns suggestions

### 5.5 Data Flow Through System
- User query → API → State dict → Workflow → (Cache/Agents/DB) → Response
- State dict updated at each step

### 5.6 Business Workflows
- Natural language query → explainable, step-by-step reasoning → structured results

### 5.7 Error Handling Flows
- Input validation errors: HTTP error returned
- Query execution errors: One retry via improvement agent, then error returned
- All errors logged to state and file

### 5.8 State Management
- State dict (GenerationState) passed through workflow, updated at each node
- Logs and reasoning steps appended to state

---

## 6. API Documentation (Developer Level)

### Endpoints
- **POST /query** (src/backend/routes/query.py:query)
  - **Input**: QuerySchema (query: str, skip: int, limit: int, is_voice: bool, organization_id: str)
  - **Output**: JSON {query, data, total, skip, limit, primary_collection, logs, reasoning_steps}
- **GET /api/recent-searches** (src/backend/routes/query.py:get_recent_searches)
  - **Input**: None
  - **Output**: JSON {recent_searches: [...]}
- **GET /autocomplete/** (src/backend/routes/query.py:get_autocomplete_suggestions)
  - **Input**: query: str, limit: int
  - **Output**: List of suggestions

### Authentication Mechanisms
- **Not found in code**: No authentication or authorization implemented

### Rate Limiting
- **Not found in code**

### Error Responses
- Returns HTTP errors on validation or workflow failure

### API Versioning
- **Not found in code**

---

## 7. Data Architecture (Architect & Developer Level)

### Database Schemas and Relationships
- **MongoDB**: Operational data; schema details not found in code
- **Supabase**: Vector storage for semantic cache

### Data Access Patterns
- Queries generated by LLM, executed via MongoEngine
- Semantic cache checked before DB query

### Caching Strategies
- Embeddings and LLM-based verification for cache hits
- New queries/embeddings stored after workflow

### Data Validation Rules
- Input validated for forbidden/destructive operations

### Migration Strategies
- **Not found in code**

### Backup and Recovery
- **Not found in code**

---

## 8. Design Patterns (Architect Level)

### Patterns Used and Why
- **State Machine/Workflow Orchestration**: Explicit, explainable, retryable flows (LangGraph)
- **Agentic Workflow**: Modular, extensible agent classes
- **Semantic Caching**: Reduces redundant computation
- **Prompt Engineering**: Centralized, consistent prompts
- **Repository/DAO**: Encapsulates DB access
- **Dependency Injection**: Composable, testable agents
- **Logging/Observability**: Traceability and debugging

### Architectural Decisions and Trade-Offs
- **LLM for every cache check**: Improves accuracy but increases cost
- **Open CORS policy**: Eases development but is a security risk
- **No authentication**: Simpler, but insecure

### Pattern Interactions
- State machine orchestrates agentic workflow, integrating semantic cache and DB access

---

## 9. Security (All Levels)

### Security Mechanisms
- **Input Validation**: Prevents forbidden/destructive queries
- **CORS Policy**: Open (allow_origins=['*']); security risk
- **No Authentication/Authorization**: Anyone can query API
- **Secret Management**: Secrets printed to stdout (risk)

### Data Protection
- **Not found in code**

### Vulnerability Mitigations
- **Not found in code**

### Compliance Considerations
- **Not found in code**

---

## 10. Development Guide (Developer Level)

### Setup and Installation
- **Prerequisites**: Python, Docker, Docker Compose
- **Dependencies**: requirements.txt
- **Installation**:
  1. Clone repository
  2. Install dependencies: `pip install -r requirements.txt`
  3. Set up environment variables (see .env.example)
  4. Start services: `docker-compose up`

### Configuration
- **Environment Variables**: See .env.example
- **Schema Configuration**: src/ai/config/config.py:SCHEMA_DICT (hard-coded)

### Running Tests
- **Not implemented**: tests/tests.py is empty

### Debugging Tips
- Check logs in logger.py
- Use langfuse for observability

### Common Issues
- Open CORS policy (security risk)
- No authentication (security risk)
- LLM cost due to cache checks
- Hard-coded schema (maintainability)

---

## 11. File Map (All Levels)

### Complete Repository Structure
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

### Key Files Organized by Component
- **API Layer**: src/backend/routes/query.py
- **Agent Orchestration**: src/ai/graphs/graph_builder.py, src/ai/graphs/graph_executor.py
- **Agents**: src/ai/agents/generation/context_detection_agent.py, src/ai/agents/generation/query_generation_agent.py, src/ai/agents/generation/improvement_agent.py
- **Embedding/Cache**: src/backend/embedding/embedding.py
- **DB Access**: src/backend/db/mongodb.py
- **Prompt Templates**: src/ai/agents/prompts/prompts.py
- **Validation**: src/backend/validators/validators.py
- **Logging**: logger.py
- **Configuration**: src/ai/config/config.py, .env.example
- **Tests**: tests/tests.py (empty)

### Entry Points
- **API**: src/backend/routes/query.py
- **Workflow**: src/ai/graphs/graph_executor.py

### Configuration Files
- .env.example, src/ai/config/config.py, src/backend/config/config.py

### Test Files
- tests/tests.py (empty)

### Documentation Files
- README.md, Code-Analysis-Review.md

---

# End of Handbook
