# Code Analysis - Developer Handbook

**Analysis Date:** 2025-11-18T23:43:45.282Z  
**Branch:** Not specified  
**Total Files Analyzed:** 0  

---

# AI-Powered Natural Language to MongoDB Query Service Handbook

## 1. Executive Summary

### Overview of the Software
This software provides users with the ability to query complex operational data stored within a MongoDB database using natural language. By utilizing advanced AI agents and large language models (LLMs), it converts plain English questions into structured MongoDB queries, executes them, and presents the results in an easily digestible format. The system is accessible via a RESTful API, facilitating seamless integration with dashboards, reporting tools, or custom applications.

### Business Value
- **Democratizes Data Access:** Empowers non-technical users to retrieve and analyze data without requiring code or database query expertise.
- **Increases Efficiency:** Streamlines the process of accessing operational data, enhancing decision-making speed.
- **Reduces IT Burden:** Decreases reliance on technical staff for routine data inquiries.
- **Optimizes Operations:** Provides real-time insights into jobs, chemicals, proposals, and more, thereby improving operational oversight.

### Key Features
- Natural language query interface via API
- AI-driven translation of English to MongoDB queries
- Semantic caching for repeated queries (vector search + LLM validation)
- Automated query repair for failed queries
- Detailed logs and reasoning steps included in every response
- Recent searches and autocomplete APIs

### Target Users and Use Cases
- **Business Analysts:** Facilitates self-service data access for reporting and analysis.
- **Operations Managers:** Enables real-time monitoring of jobs, chemicals, and proposals.
- **Non-Technical Staff:** Provides straightforward access to data for informed decision-making.

### Business Impact
- Accelerated decision-making processes
- Reduced operational costs
- Enhanced data accessibility and transparency

---

## 2. System Overview

### High-Level Architecture
The system is designed as a modular, service-oriented architecture with distinct separation of concerns:
- **API Layer:** Exposes RESTful endpoints for query submission, recent searches, and autocomplete.
- **Agentic Orchestration Layer:** Manages a multi-step workflow using a state machine (LangGraph) to invoke specialized AI agents.
- **AI Agent Layer:** Comprises agents responsible for context detection, query generation, improvement, and cache relevance checking.
- **Embedding/Caching Layer:** Utilizes vector embeddings and semantic search (Supabase) for caching and retrieving previous queries.
- **Data Layer:** Executes validated MongoDB queries and processes the results.
- **Validation/Logging Layer:** Ensures input/query safety while logging all workflow steps.

### System Capabilities
- Accepts natural language queries via API
- Translates queries to MongoDB using LLMs
- Checks for cached results based on semantic similarity
- Executes queries and returns results with logs
- Automatically repairs failed queries
- Provides recent searches and autocomplete options

### Conceptual Workflow
1. User submits a query via API.
2. The system checks for a cached, semantically similar query.
3. If a cache hit occurs, it returns the cached results.
4. If a cache miss occurs, it invokes AI agents to detect context, generate, and execute a MongoDB query.
5. If the query fails, it attempts an automated repair.
6. Results, logs, and reasoning steps are returned.

### Key Components and Their Roles
- **FastAPI HTTP API:** The entry point for all requests.
- **LangGraph State Machine:** Orchestrates the workflow.
- **AI Agents:** Facilitate specialized reasoning and query generation.
- **Embedding/Semantic Cache:** Enhances performance for repeated queries.
- **MongoDB Data Access:** Executes queries.
- **Validation/Logging:** Ensures safety and traceability.

---

## 3. Detailed Architecture

### Complete Architecture Pattern
The system employs a **state machine orchestration pattern** (LangGraph StateGraph) to coordinate a modular, agent-based workflow. Each workflow step is managed by specialized agents for context detection, query generation, improvement, and cache relevance checking. The workflow is activated through FastAPI endpoints and is designed for extensibility, observability, and modularity.

### Technology Stack
- **Languages:** Python
- **Frameworks:** FastAPI (API), LangChain (AI orchestration), LangGraph (state machine)
- **Libraries:** Uvicorn, pymongo, sentence-transformers, faiss-cpu, Supabase Python client, marshmallow, pydantic, langfuse, pandas, numpy, python-dotenv
- **Databases:** MongoDB (operational data), Supabase (vector DB for semantic cache)
- **Infrastructure:** Docker, Docker Compose, Azure OpenAI
- **AI/ML Frameworks:** LangChain, LangGraph, Azure OpenAI, sentence-transformers, faiss-cpu

### Design Decisions
- **Agentic Workflow:** Modular agents facilitate stepwise reasoning and easy extensibility.
- **State Machine Orchestration:** Ensures robust, traceable, and recoverable workflows.
- **Semantic Caching:** Reduces LLM calls and enhances performance for repeated queries.
- **Prompt Engineering:** Centralized prompt templates ensure consistent LLM behavior.
- **Observability:** Logs and reasoning steps are included in every response.

### Scalability and Performance
- **Caching:** Semantic cache reduces LLM and database load for repeated queries.
- **Asynchronous Execution:** The workflow supports asynchronous operations.
- **Containerization:** Utilizes Docker/Docker Compose for scalable deployments.
- **Bottlenecks:** LLM calls for every cache validation may impact performance and cost.

### Integration Points
- **Azure OpenAI:** For LLM reasoning and query generation.
- **Supabase:** For semantic caching.
- **MongoDB:** As the operational data store.

---

## 4. Component Details

### FastAPI HTTP API
- **Purpose:** Provides endpoints for query submission, recent searches, and autocomplete.
- **Implementation:**
  - `src/backend/routes/query.py:query` (POST /query)
  - `src/backend/routes/query.py:get_recent_searches` (GET /api/recent-searches)
  - `src/backend/routes/query.py:get_autocomplete_suggestions` (GET /autocomplete/)
- **Key Files:** 
  - `src/backend/routes/query.py`
  - `src/backend/schema/query.py` (QuerySchema)
- **Data Structures:**
  - QuerySchema (Pydantic): `{ query: str, skip: int, limit: int, is_voice: bool, organization_id: str }`
- **Error Handling:**
  - Returns 500 on workflow failure, 404 if no results, and includes logs and reasoning steps in the response.
- **Dependencies:**
  - Pydantic for validation, FastAPI for routing.

### Agentic Workflow Orchestration
- **Purpose:** Coordinates the multi-step agent workflow using LangGraph's StateGraph.
- **Implementation:**
  - `src/ai/graphs/graph_builder.py` (GraphBuilder)
  - `src/ai/graphs/graph_executor.py` (execute_workflow)
- **Key Files:**
  - `src/ai/graphs/graph_builder.py`
  - `src/ai/graphs/graph_executor.py`
  - `src/ai/state/generation_state.py` (state dict schema)
- **Data Structures:**
  - State dict: `{ user_query, mongo_query, collection_name, aggregate, execution_status, data, retry_count, reasoning_steps, error, org_id, logs }`
- **Error Handling:**
  - Logs errors, sets execution_status to False, and triggers the improvement agent on failure.
- **Dependencies:**
  - LangGraph, LangChain.

### AI Agents
- **Purpose:** Implements specialized agents for context detection, query generation, improvement, and relevance checking.
- **Implementation:**
  - `src/ai/agents/generation/context_detection_agent.py` (ContextDetectionAgent)
  - `src/ai/agents/generation/query_generation_agent.py` (QueryGenerationAgent)
  - `src/ai/agents/generation/improvement_agent.py` (ImprovementAgent)
  - `src/ai/agents/misc/check_relevance.py` (Relevance Checker)
- **Key Files:**
  - `src/ai/agents/generation/context_detection_agent.py`
  - `src/ai/agents/generation/query_generation_agent.py`
  - `src/ai/agents/generation/improvement_agent.py`
  - `src/ai/agents/misc/check_relevance.py`
- **Data Structures:**
  - State dict, prompt templates.
- **Error Handling:**
  - The ImprovementAgent is invoked on execution failure, logs errors, and retries once.
- **Dependencies:**
  - LangChain, Azure OpenAI.

### Embedding and Semantic Cache
- **Purpose:** Implements semantic caching using vector embeddings stored in Supabase.
- **Implementation:**
  - `src/backend/embedding/embedding.py` (Embedding class)
- **Key Files:**
  - `src/backend/embedding/embedding.py`
- **Data Structures:**
  - Embedding vectors, Supabase records.
- **Error Handling:**
  - If cache lookup fails, proceeds to agentic workflow; logs errors.
- **Dependencies:**
  - sentence-transformers, faiss-cpu, Supabase.

### MongoDB Data Access
- **Purpose:** Executes generated MongoDB queries, validates queries for safety, and parses results.
- **Implementation:**
  - `src/backend/db/mongodb.py` (MongoEngine class)
- **Key Files: