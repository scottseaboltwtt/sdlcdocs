# scottseaboltwtt/xops-nlp-search - Developer Handbook

**Analysis Date:** 2025-11-18T21:30:04.963Z  
**Branch:** api-internal  
**Total Files Analyzed:** 0  

---

# AI-Powered Natural Language to MongoDB Query Engine Handbook

## 1. Executive Summary

### Overview
The AI-Powered Natural Language to MongoDB Query Engine enables users to interact with complex MongoDB databases using natural language queries. Instead of requiring users to write traditional database queries, the system translates requests like "Show me jobs assigned to Anthony Adkins" into safe, executable MongoDB queries. It processes these queries and returns the results via a streamlined web API.

### Business Value
- **Democratizes Data Access**: Empowers non-technical users to retrieve and analyze data without needing to learn complex query languages.
- **Increases Efficiency**: Minimizes the time and expertise required to access critical business data.
- **Reduces IT Bottlenecks**: Eliminates the dependency on technical staff for query writing, enabling faster decision-making.
- **Enhances Data Security**: Incorporates validation mechanisms that prevent unsafe or destructive queries.
- **Ensures Traceability**: Logs each query along with reasoning steps for audit and compliance purposes.

### Key Features
- Natural language to MongoDB query translation powered by AI agents
- FastAPI web service with endpoints for querying, recent searches, and autocomplete suggestions
- Multi-step workflow including context detection, query generation, and query improvement
- Embedding-based caching to optimize repeated or similar queries using Supabase and OpenAI embeddings
- Comprehensive logging and reasoning steps included in every response
- Strict validation of inputs and queries for enhanced safety

### Target Users
- **Business Analysts**: Access reports and insights independently.
- **Operations Managers**: Monitor key business metrics in real-time.
- **Support Staff**: Quickly respond to customer and internal inquiries.
- **Developers**: Extend the system and integrate new data sources or logic.

### Expected Outcomes
- Accelerated, secure, and more accessible data retrieval
- Reduced operational costs by alleviating the IT workload
- Improved decision-making through timely access to information
- Enhanced compliance and auditability through comprehensive logging

---

## 2. System Overview

### High-Level Architecture
The system operates as a modular, agent-driven AI service that provides a REST API for natural language queries. It orchestrates a multi-step workflow utilizing LangGraph for state machine management and LangChain for large language model (LLM) orchestration. The architecture distinctly separates the API, agent logic, database access, embedding/cache systems, and validation mechanisms, which enhances maintainability and extensibility.

### System Capabilities
- Accepts natural language queries via HTTP POST requests.
- Validates and sanitizes inputs to ensure security.
- Checks for similar or cached queries using embeddings and LLM verification.
- In the absence of a cache hit, initiates an agent-driven workflow:
  - Detects relevant collections and fields.
  - Generates MongoDB queries utilizing LLMs and predefined prompt templates.
  - Improves queries if execution fails.
- Executes queries against MongoDB.
- Caches new queries and results for future use.
- Returns results, logs, and reasoning steps to users.

### Conceptual Flow
1. User submits a query via API.
2. The system validates the input and checks the cache.
3. If no cache hit occurs, the agentic workflow executes (context detection → query generation → execution → improvement if necessary).
4. The query is executed, results are cached, and a response is returned.

### Key Components
- **API Layer**: Manages HTTP requests, input validation, and response formatting.
- **Agentic Workflow**: Coordinates agents for context detection, query generation, and improvement.
- **AI Agents**: Specialized LLM-powered agents for each step of the workflow.
- **Embedding/Cache Layer**: Optimizes repeated queries using vector search and LLM verification.
- **Database Layer**: Executes MongoDB queries and processes results.
- **Validation Layer**: Ensures the safety of inputs and queries.
- **Logging Layer**: Captures all workflow steps and errors.

---

## 3. Detailed Architecture

### Architectural Pattern
The system employs a modular, layered architecture with an agentic workflow. The primary workflow functions as a state machine (LangGraph), where each node represents a distinct agent or operation (cache check, context detection, query generation, execution, improvement). The architecture allows independent development, testing, and scaling of each component by decoupling the API layer from the agentic workflow, which is further separated from the database and embedding/cache layers.

### Technology Stack
- **Programming Language**: Python
- **Frameworks**: FastAPI (API), LangChain (LLM orchestration), LangGraph (state machine)
- **Libraries**: Uvicorn, pymongo, sentence-transformers, faiss, marshmallow, pydantic, python-dotenv
- **Databases**: MongoDB (data), Supabase (vector storage for embeddings)
- **Infrastructure**: Docker, Docker Compose
- **AI/ML Technologies**: OpenAI/Azure OpenAI (LLMs, embeddings), LangChain, LangGraph

### System Design Decisions
- **Agentic Workflow**: Each workflow step (cache check, context detection, query generation, improvement) is managed by a dedicated agent, enhancing modularity and traceability.
- **State Machine Management**: LangGraph oversees workflow states and transitions, facilitating conditional routing and retries.
- **Embedding-Based Caching**: Vector search with LLM verification ensures only semantically equivalent queries are cached and reused.
- **Strict Input Validation**: All user inputs and generated queries undergo rigorous validation to ensure safety before execution.
- **Detailed Logging**: All steps and errors are recorded and included in API responses for transparency.

### Scalability and Performance
- **Cache Optimization**: Embedding-based caching significantly reduces repeated LLM and database calls for similar queries.
- **Asynchronous Execution**: FastAPI and the agentic workflow support asynchronous processing for high concurrency.
- **Bottlenecks**: The reliance on LLM for every cache check can be resource-intensive; optimization opportunities exist.
- **Extensibility**: The modular design allows for the addition of new agents, data sources, or caching strategies.

### Integration Points
- **OpenAI/Azure OpenAI**: For LLM and embedding generation.
- **Supabase**: For vector storage and search.
- **MongoDB**: For data storage and query execution.

---

## 4. Component Details

### 4.1 FastAPI Web Service
- **Purpose**: Exposes endpoints for querying, recent searches, and autocomplete functionalities.
- **Implementation**: `src/backend/routes/query.py`, `src/backend/__init__.py`
- **APIs**:
  - `POST /query`: Main query endpoint.
  - `GET /api/recent-searches`: Returns recent queries.
  - `GET /autocomplete/`: Provides autocomplete suggestions.
- **Key Files**:
  - `src/backend/routes/query.py`: Logic for endpoint handling.
  - `src/backend/schema/query.py`: Input schema definition.
  - `src/backend/validators/validators.py`: Input validation logic.
- **Data Structures**:
  - `QuerySchema`: Pydantic/marshmallow model for query validation.
- **Error Handling**:
  - Input validation errors trigger HTTP 400 responses.
  - Workflow errors trigger HTTP 500 responses.
- **Dependencies**:
  - FastAPI, Uvicorn, Pydantic, Marshmallow.

### 4.2 Agentic Workflow (LangGraph State Machine)
- **Purpose**: Orchestrates the multi-step workflow for query processing.
- **Implementation**: `src/ai/graphs/graph_builder.py`, `src/ai/graphs/graph_executor.py`
- **Nodes**:
  - `check_cache`: Checks for cached queries.
  - `detect_context`: Identifies relevant collections and fields.
  - `generate`: Produces the MongoDB query.
  - `execute`: Executes the query.
  - `improve`: Enhances a failing query.
- **Key Files**:
  - `src/ai/graphs/graph_builder.py`: Defines the state machine.
  - `src/ai/graphs/graph_executor.py`: Executes the workflow.
  - `src/ai/state/generation_state.py`: Manages state schema.
- **Data Structures**:
  - `GenerationState`: State dictionary for workflow tracking.
- **Error Handling**:
  - Implement retry logic for failed queries (maximum of one retry).
- **Dependencies**:
  - LangGraph, LangChain.

### 4.3 AI Agents
- **Purpose**: Specialized LLM-powered agents for each workflow step.
- **Implementation**:
  - `src/ai/agents/generation/context_detection_agent.py`: Context detection agent.
  - `src/ai/agents/generation/query_generation_agent.py`: Query generation agent.
  - `src/ai/agents/generation/improvement_agent.py`: Improvement agent.
- **Key Files**:
  - `src/ai/agents/prompts/prompts.py`: Contains prompt templates.
  - `src/ai/llm/llm.py`: Wrappers for LLM interactions.
- **Data Structures**:
  - Input/output dictionaries for agents.
- **Error Handling**:
  - Validation of LLM outputs.
  - Known issues with ImprovementAgent's key check.
- **Dependencies**:
  - LangChain, OpenAI/Azure OpenAI.

### 4.