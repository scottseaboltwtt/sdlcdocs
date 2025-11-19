# undefined/undefined - Developer Handbook

**Analysis Date:** 2025-11-19T22:43:21.774Z
**Branch:** undefined
**Total Files Analyzed:** 0

---

# Developer Handbook: AI-Powered Natural Language to MongoDB Query Service

## 1. Executive Summary (Business Level)

### What the Software Does
This software enables users—especially non-technical personnel in oilfield operations—to access and analyze complex operational data using plain English queries. Users can ask questions like "Show me jobs assigned to Anthony Adkins" and receive structured results from the underlying MongoDB database, without needing to know query languages or database schemas.

### Business Value and Purpose
- **Democratizes Data Access:** Empowers field managers, analysts, and executives to retrieve data without technical expertise.
- **Increases Efficiency:** Reduces time spent by technical staff on ad-hoc data requests.
- **Improves Decision-Making:** Provides timely access to operational insights.

### Key Features and Capabilities
- Natural language query interface (via HTTP API)
- AI-powered context detection and query generation
- Semantic caching for performance and cost optimization
- LLM-based query improvement and verification
- Detailed logs and reasoning steps for transparency
- Recent searches and autocomplete support

### Target Users and Use Cases
- Field operations managers needing quick data access
- Data analysts exploring operational datasets
- Executives seeking high-level summaries

### Business Impact and Outcomes
- Faster, more accurate data retrieval
- Reduced dependency on IT/data engineering
- Enhanced operational agility

---

## 2. System Overview (All Levels)

### High-Level Architecture Explanation
The system is architected as a modular, service-oriented backend that exposes a FastAPI HTTP API. It orchestrates a multi-step, agentic workflow using LangGraph (a state machine framework) and LangChain (for LLM orchestration). The workflow includes semantic cache lookup, context detection, query generation, execution, and improvement, each handled by specialized agents. Data is stored and queried in MongoDB, with semantic caching using vector embeddings stored in Supabase.

### System Capabilities and Functionality
- Accepts natural language queries via HTTP POST
- Validates and sanitizes user input
- Checks for similar queries using semantic embeddings and LLM verification
- If no cache hit, detects context and generates MongoDB queries using LLMs
- Executes queries and returns results
- Improves failed queries using LLMs and retries
- Stores new queries and embeddings for future cache hits
- Provides recent searches and autocomplete suggestions

### How the System Works (Conceptual Flow)
1. User submits a query via API.
2. System validates input and initializes workflow state.
3. Semantic cache is checked for similar queries.
4. If cache hit, returns cached result; else, detects context and generates query using LLM.
5. Executes query; if fails, improves and retries.
6. Returns results, logs, and reasoning steps.

### Key Components and Their Roles
- **API Service:** Handles HTTP requests and input validation.
- **LangGraph Workflow:** Orchestrates agent execution and state transitions.
- **Semantic Cache:** Stores and retrieves embeddings for query deduplication.
- **Agents:** Specialized LLM-driven components for context detection, query generation, and improvement.
- **MongoDB Data Access:** Executes generated queries and returns results.
- **Prompt Engineering:** Provides prompt templates for LLM interactions.
- **Logging/Observability:** Captures logs and workflow steps.

---

## 3. Detailed Architecture (Architect & Developer Level)

### Complete Architecture Pattern Explanation
The architecture follows a modular, agentic, state-machine-driven pattern:
- **Service-Oriented:** API, workflow, agents, and data access are decoupled.
- **State Machine Workflow:** LangGraph defines a state machine with nodes for each processing step.
- **Agentic Orchestration:** Each node is handled by a dedicated agent class, leveraging LLMs for reasoning and generation.
- **Semantic Caching:** Vector embeddings and LLM verification are used to avoid redundant query generation.
- **Prompt Engineering:** All LLM interactions are driven by structured prompt templates.

### Technology Stack Breakdown
- **Languages:** Python
- **Frameworks:** FastAPI (API), LangChain (LLM orchestration), LangGraph (state machine)
- **Libraries:** sentence-transformers, faiss-cpu, pymongo, MongoEngine, python-dotenv, langfuse
- **Databases:** MongoDB (operational data), Supabase (vector DB for embeddings)
- **Infrastructure:** Docker, Docker Compose, Azure OpenAI
- **Tools:** Uvicorn (ASGI server)

### System-Level Design Decisions
- **Agentic Workflow:** Each processing step is encapsulated in an agent for modularity and extensibility.
- **State Machine:** LangGraph is used to manage complex workflows and conditional transitions.
- **Semantic Caching:** Embeddings and LLM verification optimize performance and reduce costs.
- **Prompt Engineering:** Prompts are centrally managed for consistency and maintainability.

### Scalability and Performance Considerations
- **Early Cache Return:** Semantic cache lookup avoids unnecessary LLM calls.
- **Async Workflow Execution:** FastAPI and LangGraph support async operations for concurrency.
- **Retry Logic:** Failed queries are improved and retried once to maximize success rate.
- **Observability:** Logging and Langfuse integration support monitoring and debugging.

### Integration Points
- **Azure OpenAI:** For LLM and embedding generation.
- **Supabase:** For vector embedding storage and retrieval.
- **MongoDB:** For operational data storage and querying.

---

## 4. Component Details (Developer & Architect Level)

### API Service
- **Purpose:** Exposes HTTP endpoints for query, recent searches, and autocomplete.
- **Implementation:** FastAPI app (src/backend/routes/query.py)
- **Key Functions:**
  - `query(QuerySchema)` (POST /query): Main entry point for queries.
  - `get_recent_searches()` (GET /api/recent-searches): Returns recent queries.
  - `get_autocomplete_suggestions(query: str, limit: int)` (GET /autocomplete/): Returns autocomplete suggestions.
- **Input Validation:** Via `validate_user_input` (src/backend/validators/validators.py)
- **Error Handling:** Input validation errors; generic exception handling in workflow execution.
- **Dependencies:** FastAPI, validators, workflow executor.

### LangGraph Workflow Orchestrator
- **Purpose:** Defines and executes the state machine workflow.
- **Implementation:**
  - Workflow definition: `GraphBuilder` (src/ai/graphs/graph_builder.py)
  - Workflow execution: `execute_workflow(state)` (src/ai/graphs/graph_executor.py)
- **Nodes:** check_cache, detect_context, generate, execute, improve
- **State Schema:** `GenerationState` (src/ai/state/generation_state.py)
- **Error Handling:** try/except in workflow execution; errors logged and returned in state.

### Semantic Cache & Embedding Service
- **Purpose:** Performs semantic cache lookup and stores embeddings.
- **Implementation:** `Embedding` class (src/backend/embedding/embedding.py)
- **Key Functions:**
  - `get_similar_with_enhanced_verification(user_input, org_id, ...)`
  - `store_embeddings(...)`
- **Technologies:** sentence-transformers, Azure OpenAI, Supabase, faiss-cpu
- **Error Handling:** Not explicitly detailed.

### Context Detection Agent
- **Purpose:** Detects relevant collections and context using LLM.
- **Implementation:** `ContextDetectionAgent.context_detection(state)` (src/ai/agents/generation/context_detection_agent.py)
- **Prompt Templates:** In prompts.py
- **Error Handling:** Not explicitly detailed.

### Query Generation Agent
- **Purpose:** Generates MongoDB queries from reasoning steps using LLM.
- **Implementation:** `QueryGenerationAgent.query_generator(state)` (src/ai/agents/generation/query_generation_agent.py)
- **Prompt Templates:** In prompts.py
- **Error Handling:** Not explicitly detailed.

### MongoDB Data Access Layer
- **Purpose:** Executes MongoDB queries.
- **Implementation:** `MongoEngine.execute_query(state)` (src/backend/db/mongodb.py)
- **Error Handling:** Returns execution_status; errors logged and propagated.

### Query Improvement Agent
- **Purpose:** Improves failed queries using LLM and retries execution.
- **Implementation:** `ImprovementAgent.improve_query(state)` (src/ai/agents/generation/improvement_agent.py)
- **Error Handling:** Bug noted: checks 'collection' in mongo_query instead of improved_query_response.

### Prompt Engineering
- **Purpose:** Provides prompt templates for all LLM interactions.
- **Implementation:** `prompts.py` (src/ai/agents/prompts/prompts.py)

### Logging/Observability
- **Purpose:** Logs workflow steps and errors.
- **Implementation:** `logger.py` (log_to_state)

---

## 5. How It Works (All Levels) - CRITICAL SECTION

### 5.1 User Request Flow (Step-by-Step)
1. **User submits POST /query** with JSON: `{ query, skip, limit, is_voice, organization_id }` (src/backend/routes/query.py:query)
2. **Input validated** for forbidden keywords (validate_user_input)
3. **State dict initialized:** user_query, retry_count=0, skip, limit, org_id, logs, reasoning_steps=[]
4. **execute_workflow(state) called** (src/ai/graphs/graph_executor.py)
5. **LangGraph workflow runs:**
   - **check_cache:** `Embedding.check_query_cache_with_llm_verification(user_query, org_id)`
     - If use_cache=True and mongo_query/collection_name present, update state and go to execute
     - If use_cache=False, go to detect_context
   - **detect_context:** `ContextDetectionAgent.context_detection(state)`
     - Uses LLM to determine collections_involved, reasoning_steps, aggregate_required
     - Updates state
   - **generate:** `QueryGenerationAgent.query_generator(state)`
     - Uses LLM to generate MongoDB query from reasoning_steps, schema, sample queries
     - Updates state with mongo_query, collection_name, query_score
   - **execute:** `MongoEngine.execute_query(state)`
     - Executes query, updates state with data, execution_status
     - If execution_status=True, END
     - If execution_status=False and retry_count < 1, go to improve
     - If retry_count >= 1, END
   - **improve:** `ImprovementAgent.improve_query(state)`
     - Uses LLM to improve failed query, increments retry_count, loops back to execute
6. **After workflow, if not from_cache, store query and embedding** (Embedding.store_embeddings)
7. **Return results, logs, reasoning_steps to user**

### 5.2 LangGraph/Agent State Machine Flow
- **Nodes:**
  - check_cache: handle_cache_check (calls Embedding.check_query_cache_with_llm_verification)
  - detect_context: ContextDetectionAgent.context_detection
  - generate: QueryGenerationAgent.query_generator
  - execute: MongoEngine.execute_query
  - improve: ImprovementAgent.improve_query
- **Edges:**
  - Entry: check_cache
  - check_cache → (if cache hit) execute
  - check_cache → (if cache miss) detect_context
  - detect_context → generate
  - generate → execute
  - execute → (if execution_status=True or retry_count>=1) END
  - execute → (if execution_status=False and retry_count<1) improve
  - improve → execute
- **State Updates:** At each node, state dict is updated with new fields (collections, mongo_query, data, execution_status, retry_count, logs, etc.)
- **Tool Calls:** LLMs, embedding models, MongoDB queries
- **Example Flow:**
  - User asks: "Show me jobs assigned to Anthony Adkins"
  - check_cache: No LLM-verified cache hit
  - detect_context: LLM detects 'job-summary-data' as relevant collection
  - generate: LLM generates MongoDB query
  - execute: Query runs, results found
  - Results returned, query/embedding stored

### 5.3 Decision Logic
- **Cache Lookup:** Always checked first; if LLM-verified hit, skip LLM generation
- **LLM Invocation:** Used for context detection, query generation, improvement, and cache verification
- **Query Generation:** Only if cache miss; uses prompt templates and schema
- **Agent Selection:** Each node in workflow handled by specific agent
- **Error Handling:** If execution fails, retry once with improved query; if still fails, return error

### 5.4 Request/Response Patterns
- **Request:** POST /query with JSON body
- **Response:** JSON with query, data, total, skip, limit, primary_collection, logs, reasoning_steps

### 5.5 Data Flow Through System
- **Input:** Natural language query
- **Transformations:**
  - Natural language → embeddings (for cache)
  - Natural language → reasoning steps (LLM)
  - Reasoning steps → MongoDB query (LLM)
  - MongoDB query → DB results
- **Output:** Structured JSON with results and logs

### 5.6 Business Workflows
- **Natural Language Data Retrieval:** End-to-end flow from user query to data result
- **Recent Searches/Autocomplete:** Retrieval from cache

### 5.7 Error Handling Flows
- **Input Validation Errors:** Blocked at API level
- **Query Execution Failures:** Improved and retried once; errors logged and returned
- **Generic Errors:** Caught and returned in response

### 5.8 State Management
- **State Dict:** Passed and updated at each workflow node
- **Session Management:** Not found in code

---

## 6. API Documentation (Developer Level)

### Endpoints
- **POST /query**
  - **Input:** QuerySchema { query: str, skip: int, limit: int, is_voice: bool, organization_id: str }
  - **Output:** JSON { query, data, total, skip, limit, primary_collection, logs, reasoning_steps }
  - **Purpose:** Main endpoint for natural language queries

- **GET /api/recent-searches**
  - **Input:** None
  - **Output:** JSON { recent_searches: [...] }
  - **Purpose:** Returns recent queries from cache

- **GET /autocomplete/**
  - **Input:** query: str, limit: int
  - **Output:** List[{ text: str, is_voice: bool }]
  - **Purpose:** Autocomplete suggestions from cached queries

### Authentication Mechanisms
- **Not found in code.** No authentication or authorization implemented.

### Rate Limiting
- **Not found in code.**

### Error Responses
- Input validation errors
- Generic errors returned in response JSON

### API Versioning
- **Not found in code.**

---

## 7. Data Architecture (Architect & Developer Level)

### Database Schemas and Relationships
- **MongoDB:** Operational data; schema details not found in code
- **Supabase:** Vector DB for embeddings; schema not detailed
- **SCHEMA_DICT:** Hard-coded schema dictionary in src/ai/config/config.py

### Data Access Patterns
- **Repository/DAO:** MongoEngine used for DB access
- **Semantic Cache:** Embeddings stored and retrieved for cache lookup

### Caching Strategies
- **Semantic Cache:** Embeddings + LLM verification
- **Cache Invalidation:** Not found in code

### Data Validation Rules
- **Input Validation:** Forbidden keywords blocked
- **Schema Validation:** SCHEMA_DICT used for prompt engineering

### Migration Strategies
- **Not found in code.**

### Backup and Recovery
- **Not found in code.**

---

## 8. Design Patterns (Architect Level)

### Patterns Used and Why
- **State Machine:** Manages complex, conditional workflows (LangGraph)
- **Agentic Workflow:** Modularizes logic into agents for extensibility
- **Semantic Caching:** Optimizes performance and cost
- **Repository/DAO:** Abstracts DB access
- **Prompt Engineering:** Centralizes LLM prompt management
- **Dependency Injection:** Supports testability and modularity
- **Factory:** Encapsulates workflow creation
- **Logging/Observability:** Supports monitoring and debugging

### Architectural Decisions and Trade-Offs
- **Agentic Modularity:** Easier to extend, but increases complexity
- **State Machine:** Explicit control flow, but more boilerplate
- **Semantic Caching:** Reduces LLM calls, but adds complexity and storage requirements

### Pattern Interactions
- **Agents orchestrated by state machine**
- **Semantic cache checked before agent invocation**

---

## 9. Security (All Levels)

### Security Mechanisms
- **CORS:** Open policy (allow_origins=['*'])
- **Input Validation:** Blocks forbidden/destructive operations
- **No Authentication/Authorization:** Not implemented
- **Secret Leakage Risk:** Secrets printed in config.py
- **LLM Prompt Injection Risk:** Validators only block obvious keywords

### Data Protection
- **Not found in code.**

### Compliance Considerations
- **Not found in code.**

---

## 10. Development Guide (Developer Level)

### Setup and Installation
- **Prerequisites:** Python, Docker, Docker Compose
- **Dependencies:** requirements.txt
- **Installation:**
  - Clone repository
  - Install dependencies: `pip install -r requirements.txt`
  - Set environment variables (see .env.example)
  - Start services: `docker-compose up`

### Configuration
- **Environment Variables:** SUPABASE_KEY, MONGODB_URI, Azure OpenAI keys (see .env.example)
- **Configuration Files:** src/backend/config/config.py, src/ai/config/config.py

### Running Tests
- **tests/tests.py:** Empty; no tests implemented

### Debugging Tips
- **Logs:** See logger.py and Langfuse integration
- **Common Issues:**
  - Missing environment variables
  - MongoDB/Supabase connection errors
  - LLM API key issues

### Common Issues
- **No authentication/authorization:** Open API
- **Secret leakage risk:** Do not print secrets in production
- **LLM prompt injection risk:** Strengthen input validation

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
- **API Service:** src/backend/routes/query.py
- **Workflow Orchestrator:** src/ai/graphs/graph_builder.py, src/ai/graphs/graph_executor.py
- **Semantic Cache:** src/backend/embedding/embedding.py
- **Agents:** src/ai/agents/generation/context_detection_agent.py, query_generation_agent.py, improvement_agent.py
- **Prompt Engineering:** src/ai/agents/prompts/prompts.py
- **Data Access:** src/backend/db/mongodb.py
- **Logging:** logger.py
- **Configuration:** src/backend/config/config.py, src/ai/config/config.py
- **State Schema:** src/ai/state/generation_state.py
- **Tests:** tests/tests.py (empty)

---

# End of Handbook
