# undefined/undefined - Developer Handbook

**Analysis Date:** 2025-11-18T22:42:19.042Z
**Branch:** undefined
**Total Files Analyzed:** 0

---

# Developer Handbook: AI-Powered Natural Language to MongoDB Query Service

---

## 1. Executive Summary (Business Level)

### What the Software Does
This software provides an AI-powered service that allows users to ask business questions in plain English and receive structured answers directly from a MongoDB database. Users do not need to know how to write database queries; instead, they interact with a simple API endpoint that interprets their intent, generates safe database queries, and returns the results.

### Business Value and Purpose
- **Accessibility**: Empowers non-technical users to access and analyze business data without technical barriers.
- **Efficiency**: Reduces the need for technical staff to write custom queries for every business question.
- **Speed**: Accelerates data-driven decision making by providing instant answers to business questions.
- **Consistency**: Ensures queries are safe, validated, and follow business rules.

### Key Features and Capabilities
- Natural language query interface (via HTTP API)
- AI-powered translation of questions to MongoDB queries
- Semantic caching for repeated or similar queries (using vector embeddings)
- Modular agentic workflow for context detection, query generation, and improvement
- Structured, explainable results with reasoning steps and logs
- Recent searches and autocomplete features

### Target Users and Use Cases
- **Business Analysts**: Quickly retrieve business metrics and insights
- **Operations Managers**: Monitor KPIs and generate reports
- **Executives**: Access high-level summaries and trends
- **Developers**: Integrate, maintain, and extend the system

### Business Impact and Outcomes
- Democratizes access to business data
- Reduces operational bottlenecks
- Improves data literacy and self-service analytics
- Ensures data security and compliance through query validation

---

## 2. System Overview (All Levels)

### High-Level Architecture Explanation
The system is built as a modular, service-oriented backend application. At its core, it exposes a FastAPI HTTP API that receives natural language queries. These queries are processed by an agentic workflow orchestrated by LangGraph, which manages state transitions and invokes specialized AI agents for context detection, query generation, and improvement. The system uses LangChain for LLM orchestration, Azure OpenAI for LLM and embeddings, and Supabase as a vector database for semantic caching. MongoDB serves as the primary data store.

### System Capabilities and Functionality
- Accepts natural language queries via HTTP POST
- Validates and processes input for safety and intent
- Checks for cached queries using semantic similarity and LLM validation
- If no cache hit, detects relevant collections and fields using LLMs
- Generates MongoDB queries or aggregation pipelines using prompt engineering
- Executes queries against MongoDB and returns structured results
- Stores new queries and embeddings for future cache hits
- Provides recent searches and autocomplete suggestions
- Logs all steps and integrates with langfuse for observability

### How the System Works (Conceptual Flow)
1. User submits a question via API
2. System validates input and checks for cached results
3. If cache miss, system detects context and generates a query using LLMs
4. Query is validated and executed against MongoDB
5. Results are returned, and new queries are cached
6. All steps are logged and traced

### Key Components and Their Roles
- **FastAPI HTTP API**: Entry point for user queries
- **Agentic Workflow (LangGraph)**: Orchestrates query processing
- **Embedding and Semantic Cache**: Handles caching and retrieval of similar queries
- **AI Agents**: Specialized modules for context detection, query generation, and improvement
- **MongoDB Data Access Layer**: Executes queries and returns results
- **Logging and Observability**: Tracks and traces all operations

---

## 3. Detailed Architecture (Architect & Developer Level)

### Complete Architecture Pattern Explanation
The architecture follows a modular, layered pattern with clear separation of concerns:
- **API Layer**: FastAPI routes handle HTTP requests, input validation, and response formatting.
- **Orchestration Layer**: LangGraph state machine manages the workflow, invoking agents and handling state transitions.
- **Agent Layer**: Modular AI agents (context detection, query generation, improvement) encapsulate LLM logic and prompt engineering.
- **Caching Layer**: Embedding and semantic cache module manages vector search and LLM-based intent validation.
- **Database Layer**: MongoEngine encapsulates MongoDB access, ensuring queries are safe and results are parsed.
- **Observability Layer**: Centralized logging and langfuse integration provide traceability and debugging support.

### Technology Stack Breakdown
- **Languages**: Python
- **Frameworks**: FastAPI, LangChain, LangGraph
- **AI/ML**: Azure OpenAI (LLM and embeddings), sentence-transformers, faiss-cpu
- **Databases**: MongoDB (primary data), Supabase (vector DB for embeddings)
- **Infrastructure**: Docker, Docker Compose, Azure OpenAI
- **Libraries**: Uvicorn, marshmallow, pydantic, numpy, requests, pandas, langfuse, python-dotenv

### System-Level Design Decisions
- **Agentic Workflow**: Modular agents allow for extensibility and clear separation of responsibilities.
- **Semantic Caching**: Vector embeddings and LLM validation optimize for repeated queries and reduce LLM calls.
- **Prompt Engineering**: Custom prompt templates ensure accurate and safe query generation.
- **Observability**: Logging and tracing are integrated at every step for debugging and monitoring.
- **Validation and Guardrails**: Input and query validation prevent destructive or unsafe operations.

### Scalability and Performance Considerations
- **Caching**: Semantic cache reduces LLM and database load for repeated queries.
- **Asynchronous Processing**: FastAPI and Uvicorn support async operations (though not explicitly detailed in code).
- **Modularity**: Agents and workflow nodes can be extended or replaced independently.
- **Potential Bottlenecks**: LLM calls for every cache check may impact performance and cost (see anti-patterns).

### Integration Points with External Systems
- **Azure OpenAI**: For LLM and embedding generation
- **Supabase**: For vector embedding storage and retrieval
- **MongoDB**: For business data storage and query execution
- **langfuse**: For observability and tracing

---

## 4. Component Details (Developer & Architect Level)

### FastAPI HTTP API
- **Purpose**: Exposes endpoints for query, recent searches, and autocomplete
- **Implementation**: src/backend/routes/query.py, src/backend/__init__.py
- **APIs**:
  - POST /query: Main entry point for natural language queries
  - GET /api/recent-searches: Returns recent queries from cache
  - GET /autocomplete/: Provides autocomplete suggestions
- **Key Files**: src/backend/routes/query.py, src/backend/__init__.py
- **Data Structures**: QuerySchema (src/backend/schema/query.py)
- **Error Handling**: Input validation errors handled; generic exceptions re-raised
- **Dependencies**: FastAPI, pydantic, validators

### Agentic Workflow (LangGraph State Machine)
- **Purpose**: Orchestrates query processing using modular nodes
- **Implementation**: src/ai/graphs/graph_builder.py, src/ai/graphs/graph_executor.py
- **Nodes**:
  - check_cache (handle_cache_check)
  - detect_context (ContextDetectionAgent.context_detection)
  - generate (QueryGenerationAgent.query_generator)
  - execute (MongoEngine.execute_query)
  - improve (ImprovementAgent.improve_query)
- **Key Files**: src/ai/graphs/graph_builder.py, src/ai/graphs/graph_executor.py
- **Data Structures**: GenerationState (src/ai/state/generation_state.py)
- **Error Handling**: Logs errors, retries improvement once
- **Dependencies**: LangGraph, LangChain, AI agents

### Embedding and Semantic Cache
- **Purpose**: Caches queries and results using vector embeddings and LLM validation
- **Implementation**: src/backend/embedding/embedding.py
- **Key Functions**:
  - get_similar_with_enhanced_verification
  - store_embeddings
- **Key Files**: src/backend/embedding/embedding.py
- **Data Structures**: Embedding records
- **Error Handling**: Not explicitly detailed
- **Dependencies**: sentence-transformers, faiss-cpu, Supabase

### AI Agents (Context Detection, Query Generation, Improvement)
- **Purpose**: Modular agents for context detection, query generation, and improvement
- **Implementation**: src/ai/agents/generation/context_detection_agent.py, src/ai/agents/generation/query_generation_agent.py, src/ai/agents/generation/improvement_agent.py
- **Key Functions**:
  - context_detection
  - query_generator
  - improve_query
- **Key Files**: See above
- **Data Structures**: State dict, prompt templates
- **Error Handling**: Logs errors, retries improvement once
- **Dependencies**: LangChain, Azure OpenAI

### MongoDB Data Access Layer
- **Purpose**: Executes MongoDB queries and aggregation pipelines
- **Implementation**: src/backend/db/mongodb.py
- **Key Functions**:
  - execute_query
- **Key Files**: src/backend/db/mongodb.py
- **Data Structures**: MongoDB query dicts
- **Error Handling**: Returns execution_status, logs errors
- **Dependencies**: pymongo

### Logging and Observability
- **Purpose**: Logs all steps and integrates with langfuse
- **Implementation**: logger.py, src/ai/graphs/graph_executor.py
- **Key Files**: logger.py
- **Data Structures**: Log records
- **Error Handling**: Logs errors and includes them in workflow state
- **Dependencies**: langfuse

---

## 5. How It Works (All Levels) - CRITICAL SECTION

### 5.1 User Request Flow (Step-by-Step)
1. **User submits query/request**: User sends a POST request to /query with a natural language question (src/backend/routes/query.py:query).
2. **API receives request, validates input**: Input is validated for forbidden/destructive keywords (src/backend/validators/validators.py:validate_user_input).
3. **State is initialized**: State dict is created with user_query, skip, limit, org_id, etc. (src/ai/state/generation_state.py).
4. **Route to agentic workflow**: execute_workflow(state) is called (src/ai/graphs/graph_executor.py).
5. **Check cache**: check_cache node (handle_cache_check) uses Embedding.get_similar_with_enhanced_verification to check for a cached query using vector similarity and LLM intent validation (src/backend/embedding/embedding.py).
6. **Decision point - Cache hit?**
   - If cache hit and LLM confirms semantic equivalence, cached query/result is used (state updated, flow jumps to execute node).
   - If cache miss, flow proceeds to context detection.
7. **Context detection**: detect_context node (ContextDetectionAgent.context_detection) uses LLM and schema metadata to identify relevant collections/fields (src/ai/agents/generation/context_detection_agent.py).
8. **Query generation**: generate node (QueryGenerationAgent.query_generator) uses LLM and prompt engineering to generate a MongoDB query or aggregation pipeline (src/ai/agents/generation/query_generation_agent.py).
9. **Query validation**: Generated query is validated for safety (src/backend/validators/validators.py:validate_generated_mongo_query, is_safe_aggregation_pipeline).
10. **Query execution**: execute node (MongoEngine.execute_query) runs the query against MongoDB, parses results, and updates state (src/backend/db/mongodb.py).
11. **Decision point - Execution success?**
    - If execution_status is True, workflow ends and results are returned.
    - If execution_status is False and retry_count < 1, flow goes to improve node.
    - If retry_count >= 1, workflow ends and error is returned.
12. **Query improvement**: improve node (ImprovementAgent.improve_query) uses LLM to refine the query and loops back to execute (src/ai/agents/generation/improvement_agent.py).
13. **Cache update**: If not from cache, query and embedding are stored for future cache hits (Embedding.store_embeddings).
14. **Response returned**: Results, reasoning steps, and logs are returned to the user.
15. **Logging and tracing**: All steps are logged (logger.py) and traced (langfuse).

### 5.2 LangGraph/Agent State Machine Flow
- **Nodes**:
  - check_cache (handle_cache_check)
  - detect_context (ContextDetectionAgent.context_detection)
  - generate (QueryGenerationAgent.query_generator)
  - execute (MongoEngine.execute_query)
  - improve (ImprovementAgent.improve_query)
- **Edge Conditions**:
  - check_cache → execute (if cache hit)
  - check_cache → detect_context (if cache miss)
  - detect_context → generate
  - generate → execute
  - execute → END (if execution_status True or retry_count >= 1)
  - execute → improve (if execution_status False and retry_count < 1)
  - improve → execute
- **State Updates**: Each node updates the state dict with relevant fields (e.g., mongo_query, collection_name, data, execution_status, retry_count, logs, etc.)
- **Tool Calls**: Embedding.get_similar_with_enhanced_verification, ContextDetectionAgent.context_detection, QueryGenerationAgent.query_generator, MongoEngine.execute_query, ImprovementAgent.improve_query
- **Example Flow**:
  - User asks "Show me jobs assigned to Anthony Adkins"
  - check_cache: no cache hit
  - detect_context: identifies 'job-summary-data' as relevant
  - generate: LLM generates MongoDB aggregation pipeline
  - execute: runs query, gets results
  - store_embeddings: saves query and embedding for future cache
  - returns results to user

### 5.3 Decision Logic
- **Cache Lookup Logic**: Always check cache first using vector similarity and LLM validation. If hit, use cached query/result.
- **LLM Invocation Logic**: If cache miss, invoke LLM for context detection and query generation. LLM also used for cache validation and query improvement.
- **Database Query Generation**: Generated by QueryGenerationAgent using prompt templates and LLM.
- **Agent Selection**: ContextDetectionAgent for context, QueryGenerationAgent for query, ImprovementAgent for retries.
- **Error Handling**: If execution fails, retry once with improvement agent. If still fails, return error.

### 5.4 Request/Response Patterns
- **POST /query**: Receives QuerySchema, returns structured JSON with query, data, total, skip, limit, primary_collection, logs, reasoning_steps.
- **GET /api/recent-searches**: Returns recent queries from cache.
- **GET /autocomplete/**: Returns autocomplete suggestions from cached queries.

### 5.5 Data Flow Through System
- User input → API → State dict → Agentic workflow → Embedding cache → LLM agents → MongoDB → Results → Response
- State dict is updated at each workflow node with new data/results

### 5.6 Business Workflows
- Natural language query to data retrieval (see functionalFlows in businessAnalysis)

### 5.7 Error Handling Flows
- Input validation errors handled at API layer
- Query validation errors handled before execution
- Execution errors trigger improvement agent (one retry)
- All errors logged and included in response/logs

### 5.8 State Management
- State is maintained as a dict (GenerationState) passed through workflow nodes
- State includes user_query, mongo_query, collection_name, data, execution_status, retry_count, logs, etc.
- No session management; state is per-request

---

## 6. API Documentation (Developer Level)

### POST /query
- **Path**: /query
- **Method**: POST
- **Input**: QuerySchema { query: str, skip: int, limit: int, is_voice: bool, organization_id: str }
- **Output**: { query, data, total, skip, limit, primary_collection, logs, reasoning_steps }
- **Purpose**: Main endpoint for natural language to MongoDB query
- **Authentication**: Not implemented
- **Rate Limiting**: Not implemented
- **Error Responses**: Input validation errors, generic exceptions

### GET /api/recent-searches
- **Path**: /api/recent-searches
- **Method**: GET
- **Input**: None
- **Output**: { recent_searches: [...] }
- **Purpose**: Returns recent queries from cache

### GET /autocomplete/
- **Path**: /autocomplete/
- **Method**: GET
- **Input**: query: str, limit: int
- **Output**: [{ text: str, is_voice: bool }]
- **Purpose**: Autocomplete suggestions from cached queries

---

## 7. Data Architecture (Architect & Developer Level)

### Database Schemas and Relationships
- **MongoDB**: Stores business data; schema details not explicitly found in code (hard-coded schema in src/ai/config/config.py:SCHEMA_DICT)
- **Supabase**: Stores query embeddings and metadata for semantic cache

### Data Access Patterns
- MongoEngine executes queries and aggregation pipelines
- Embedding module performs vector search and LLM validation

### Caching Strategies
- Semantic cache using vector embeddings and LLM-based intent validation
- Cache lookup before invoking LLM for query generation
- New queries and embeddings stored after successful execution

### Data Validation Rules
- Input validated for forbidden/destructive keywords
- Generated queries validated for safety (read-only, collection restrictions)

### Migration Strategies
- Not found in code

### Backup and Recovery
- Not found in code

---

## 8. Design Patterns (Architect Level)

### Agentic Workflow (State Machine)
- **Pattern**: Modular state machine with nodes for each processing step
- **Implementation**: LangGraph (src/ai/graphs/graph_builder.py)
- **Benefits**: Extensibility, clear separation of concerns
- **Trade-offs**: Complexity, potential performance overhead

### Prompt Engineering
- **Pattern**: Custom prompt templates for LLM agents
- **Implementation**: src/ai/agents/prompts/prompts.py
- **Benefits**: Accurate and safe query generation
- **Trade-offs**: Maintenance of prompt templates

### Repository Pattern
- **Pattern**: Encapsulate MongoDB access in MongoEngine
- **Implementation**: src/backend/db/mongodb.py
- **Benefits**: Centralized data access logic
- **Trade-offs**: Tight coupling to MongoDB

### Semantic Caching (Vector Search)
- **Pattern**: Store and retrieve queries using vector embeddings
- **Implementation**: src/backend/embedding/embedding.py
- **Benefits**: Reduces LLM and DB load for repeated queries
- **Trade-offs**: Embedding storage cost, LLM cost for validation

### Validation and Guardrails
- **Pattern**: Input and query validation for safety
- **Implementation**: src/backend/validators/validators.py
- **Benefits**: Prevents destructive or unsafe operations
- **Trade-offs**: Potential false positives/negatives

### Logging and Observability
- **Pattern**: Centralized logging and tracing
- **Implementation**: logger.py, langfuse integration
- **Benefits**: Debugging, monitoring, auditability
- **Trade-offs**: Log volume, performance impact

---

## 9. Security (All Levels)

### Security Mechanisms
- **Input Validation**: Validates user input for forbidden/destructive keywords (src/backend/validators/validators.py)
- **Query Validation**: Ensures only safe, read-only queries are executed (src/backend/validators/validators.py)
- **CORS Policy**: Open CORS policy (allow_origins=['*']) (src/backend/__init__.py)
- **Authentication/Authorization**: Not implemented (no checks in FastAPI routes)
- **Secrets Handling**: Secrets printed to stdout (potential vulnerability) (src/backend/config/config.py)

### Data Protection
- No explicit encryption or data protection mechanisms found in code

### Vulnerability Mitigations
- Input and query validation provide some protection against injection/destructive queries
- Lack of authentication/authorization is a critical gap

### Compliance Considerations
- Not found in code

---

## 10. Development Guide (Developer Level)

### Setup and Installation
- **Prerequisites**: Python, Docker, Docker Compose
- **Dependencies**: requirements.txt
- **Installation**:
  1. Clone repository
  2. Install dependencies: `pip install -r requirements.txt`
  3. Set up environment variables (see .env.example)
  4. Start services: `docker-compose up` (if using Docker)

### Configuration
- **Environment Variables**: See .env.example
- **Supabase Credentials**: Required for embedding cache
- **Azure OpenAI Credentials**: Required for LLM and embeddings
- **MongoDB Connection**: Configure in src/backend/config/config.py

### Running Tests
- **Test Files**: tests/tests.py (empty)
- **Test Execution**: Not implemented

### Debugging Tips
- **Logs**: Check logs in logger.py output
- **Tracing**: Use langfuse integration for workflow tracing
- **Common Issues**: Missing credentials, open CORS, lack of authentication

### Common Issues
- **No authentication/authorization**: All endpoints are open
- **Secrets printed to stdout**: Potential security risk
- **LLM cost/performance**: LLM used for every cache check
- **No tests implemented**: tests/tests.py is empty

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
- **API Layer**: src/backend/routes/query.py, src/backend/__init__.py
- **Agentic Workflow**: src/ai/graphs/graph_builder.py, src/ai/graphs/graph_executor.py
- **Embedding/Cache**: src/backend/embedding/embedding.py
- **AI Agents**: src/ai/agents/generation/context_detection_agent.py, src/ai/agents/generation/query_generation_agent.py, src/ai/agents/generation/improvement_agent.py
- **Database Layer**: src/backend/db/mongodb.py
- **Logging**: logger.py
- **Configuration**: src/backend/config/config.py, src/ai/config/config.py
- **Validation**: src/backend/validators/validators.py
- **Prompt Templates**: src/ai/agents/prompts/prompts.py
- **State Schema**: src/ai/state/generation_state.py
- **Tests**: tests/tests.py (empty)

### Entry Points
- **API**: src/backend/routes/query.py:query
- **Workflow**: src/ai/graphs/graph_executor.py:execute_workflow

### Configuration Files
- .env.example
- requirements.txt
- docker-compose.yml
- Dockerfile

### Documentation Files
- README.md
- Code-Analysis-Review.md

---

# End of Handbook
