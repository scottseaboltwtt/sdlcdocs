# Code Analysis - Developer Handbook

**Analysis Date:** 2025-11-19T19:03:57.623Z  
**Branch:** Not specified  
**Total Files Analyzed:** 0  

---

# AI-Powered Natural Language Query System Handbook

## 1. Executive Summary

### Overview
This document presents an AI-powered natural language interface designed to facilitate querying complex MongoDB databases, specifically tailored for the oil and gas sector. It enables business users to pose questions in plain English and receive structured, explainable results, thereby eliminating the necessity for technical query writing and expediting data-driven decision-making.

### Business Value and Purpose
- **Democratizes Data Access**: Empowers non-technical users to derive insights from extensive, complex datasets without requiring knowledge of SQL or MongoDB.
- **Accelerates Decision Making**: Reduces the time needed for data requests, enabling prompt business decisions.
- **Reduces IT Burden**: Diminishes the need for custom report development and ad-hoc query support from technical teams.
- **Ensures Data Security**: Validates queries to prevent destructive operations.

### Key Features and Capabilities
- Natural language query interface (via HTTP API)
- AI-powered translation of user intent into MongoDB queries
- Semantic caching to prevent redundant LLM/database calls
- Explainable, step-by-step reasoning for each query
- Robust error handling and query improvement mechanisms
- Recent search history and autocomplete support

### Target Users and Use Cases
- **Business Users**: Quick access to operational data (e.g., job assignments, production statistics)
- **Data Analysts**: Validation and refinement of queries for deeper insights
- **Developers**: Extension of system capabilities and integration of new data sources
- **Architects**: Integration with enterprise data platforms

### Business Impact and Outcomes
- Enhanced speed and accuracy of business insights
- Decreased reliance on technical staff for data access
- Improved data governance and auditability

---

## 2. System Overview

### High-Level Architecture
The system is designed as a modular, service-oriented platform with a clear separation between API routing, agent orchestration, embedding/caching, and database access. It utilizes:
- **FastAPI** for HTTP API exposure
- **LangChain** and **LangGraph** for agent orchestration and LLM integration
- **Azure OpenAI** for LLM and embedding generation
- **Supabase** for vector storage and semantic caching
- **MongoDB** for operational data storage

### System Capabilities and Functionality
- Accepts natural language queries via HTTP POST
- Validates input for security
- Checks semantic cache for similar queries
- Orchestrates multi-step workflows (context detection, query generation, execution, improvement)
- Returns structured results, reasoning steps, and logs
- Supports recent searches and autocomplete functionality

### Conceptual Workflow
1. User submits a query (e.g., "Show me jobs assigned to Anthony Adkins")
2. API validates input and initializes workflow state
3. System checks semantic cache for similar queries
4. If cache miss occurs, orchestrates the agent workflow:
   - Detects context (relevant collections)
   - Generates MongoDB query using LLM
   - Executes query
   - If execution fails, attempts improvement
5. Stores new queries and embeddings for future cache hits
6. Returns results and reasoning to the user

### Key Components
- **API Layer**: Manages HTTP requests, validation, and response formatting
- **Agent Orchestration (LangGraph)**: Handles workflow as a state machine
- **Agents**: Specialized LLM-powered modules for context detection, query generation, and improvement
- **Embedding & Semantic Cache**: Stores and retrieves query embeddings for cache hits
- **MongoDB Data Access**: Executes validated queries
- **Prompt Templates**: Provides LLM prompts
- **Validation Layer**: Prevents forbidden operations
- **Logging & Observability**: Tracks workflow steps and errors

---

## 3. Detailed Architecture

### Architecture Pattern
The system follows a **modular, agentic, state-machine workflow** pattern characterized by:
- **Service-Oriented Architecture**: Separates major functions (API, agent orchestration, embedding, and database access) into distinct modules.
- **Agentic Workflow**: Specialized agents manage context detection, query generation, and improvement.
- **State Machine Orchestration**: LangGraph defines the workflow as a state machine, allowing for robust, explainable, and retryable processes.
- **Semantic Caching**: Utilizes embeddings and LLM-based verification to avoid redundant computations.

### Technology Stack Breakdown
- **Languages**: Python
- **Frameworks**: FastAPI, LangChain, LangGraph
- **AI/ML**: Azure OpenAI (LLM, embeddings), SentenceTransformers, faiss-cpu
- **Databases**: MongoDB (operational data), Supabase (vector storage)
- **Infrastructure**: Docker, Docker Compose, Azure OpenAI
- **Libraries**: pymongo, marshmallow, pydantic, python-dotenv, tiktoken, langfuse

### System-Level Design Decisions
- **Agentic Modularity**: Each agent is encapsulated within a class with a single responsibility, facilitating easy extension and maintenance.
- **State Machine Workflow**: LangGraph enables explicit, explainable transitions and retry logic for improved reliability.
- **Semantic Caching**: Enhances performance and reduces costs by minimizing LLM and database loads.
- **Prompt Engineering**: Centralized prompt templates ensure consistency across queries.
- **Observability**: Integrated logging and langfuse for enhanced traceability.

### Scalability and Performance Considerations
- **Semantic Cache**: Limits LLM/database calls for repeated queries.
- **Asynchronous Workflow**: FastAPI and async workflow execution allow for concurrent requests.
- **Externalized State**: Embeddings and cache stored in Supabase enhance scalability.
- **Potential Bottlenecks**: LLM usage for every cache check poses performance and cost risks.

### Integration Points
- **Azure OpenAI**: Facilitates LLM and embedding generation.
- **Supabase**: Manages vector storage for semantic caching.
- **MongoDB**: Serves as the data storage solution and query executor.
- **Langfuse**: Provides observability and logging capabilities.

---

## 4. Component Details

### API Layer
- **Purpose**: Exposes HTTP endpoints for queries, recent searches, and autocomplete functionality.
- **Implementation**: FastAPI app located at `src/backend/routes/query.py`.
- **Key Functions**:
  - `query`: POST /query (main entry point)
  - `get_recent_searches`: GET /api/recent-searches
  - `get_autocomplete_suggestions`: GET /autocomplete/
- **Validation**: Uses `validate_user_input` (src/backend/validators/validators.py).
- **Error Handling**: Employs try/except blocks to return HTTP errors.
- **Dependencies**: Interacts with workflow executor and embedding service.

### Agent Orchestration (LangGraph Workflow)
- **Purpose**: Coordinates agentic workflows as a state machine.
- **Implementation**: Defined in `GraphBuilder` (src/ai/graphs/graph_builder.py) and executed by `execute_workflow` (src/ai/graphs/graph_executor.py).
- **Nodes**:
  - `check_cache`: Validates semantic cache.
  - `detect_context`: ContextDetectionAgent.
  - `generate`: QueryGenerationAgent.
  - `execute`: MongoEngine.
  - `improve`: ImprovementAgent.
- **State Schema**: `GenerationState` (src/ai/state/generation_state.py).
- **Error Handling**: Supports retries on execution failures.

### Context Detection Agent
- **Purpose**: Identifies relevant collections and reasoning steps.
- **Implementation**: `ContextDetectionAgent.context_detection` (src/ai/agents/generation/context_detection_agent.py).
- **Technologies**: Utilizes LangChain, Azure OpenAI, SentenceTransformers, and faiss-cpu.
- **Dependencies**: Utilizes prompt templates and Supabase.
- **Error Handling**: Logs errors to state.

### Query Generation Agent
- **Purpose**: Translates user intent into MongoDB queries.
- **Implementation**: `QueryGenerationAgent.query_generator` (src/ai/agents/generation/query_generation_agent.py).
- **Technologies**: Employs LangChain and Azure OpenAI.
- **Dependencies**: Relies on prompt templates.
- **Error Handling**: Logs errors to state.

### Improvement Agent
- **Purpose**: Refines failed queries using LLM and feedback.
- **Implementation**: `ImprovementAgent.improve_query` (src/ai/agents/generation/improvement_agent.py).
- **Technologies**: Utilizes LangChain and Azure OpenAI.
- **Dependencies**: Relies on prompt templates.
- **Error Handling**: Known issue with key checks in erroneous dictionaries.

### Embedding & Semantic Cache
- **Purpose**: Manages semantic caching of queries.
- **Implementation**: `Embedding.check_query_cache_with_llm_verification`, `Embedding.store_embeddings` (src/backend/embedding/embedding.py).
- **Technologies**: Utilizes Azure OpenAI, Supabase, SentenceTransformers, and faiss-cpu.
- **Error Handling**: LLM usage for every cache check poses performance and cost risks.

### MongoDB Data Access
- **Purpose**: Executes MongoDB queries.
- **Implementation**: `MongoEngine.execute_query` (src/backend/db/mongodb.py).
