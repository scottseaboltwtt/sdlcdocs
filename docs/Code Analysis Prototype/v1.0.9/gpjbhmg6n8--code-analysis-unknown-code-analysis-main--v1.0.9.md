# Code Analysis - Developer Handbook

**Analysis Date:** 2025-11-19  
**Branch:** Undefined  
**Total Files Analyzed:** 0  

---

# AI-Powered Natural Language to MongoDB Query Service

## 1. Executive Summary

### Overview
This software offers an AI-powered service that enables users to query MongoDB databases using natural language. Users can input queries such as "Show me jobs assigned to Anthony Adkins," and the system interprets the request, generates the corresponding MongoDB query, executes it, and returns the results without requiring any knowledge of database query language.

### Business Value
The system democratizes access to complex data by eliminating technical barriers. It empowers business analysts, managers, and support staff to quickly retrieve and analyze data, thereby reducing reliance on technical teams and facilitating faster decision-making. By automating query generation and utilizing AI for contextual understanding, the software enhances productivity and supports data-driven agility.

### Key Features
- **Natural Language Querying**: Users can interact with data using plain English.
- **AI-Powered Query Generation**: Utilizes large language models (LLMs) through Azure OpenAI and LangChain to create and refine queries.
- **Semantic Caching**: Remembers similar queries to provide quicker responses using vector embeddings with Supabase.
- **Context Detection**: Automatically identifies relevant collections and fields.
- **Error Correction**: Enhances failing queries through feedback from LLMs.
- **Recent Searches and Autocomplete**: Improves user experience with search history and suggestions.
- **Detailed Logging**: Ensures transparency and traceability across all actions.

### Target Users
- **Business Analysts**: Extract and analyze data for reporting purposes.
- **Operations Managers**: Monitor key metrics and generate insights.
- **Customer Support**: Quickly access customer and job-related data.
- **Data Scientists**: Conduct exploratory data analysis.

### Business Impact
- **Reduced Time-to-Insight**: Immediate access to data without technical bottlenecks.
- **Lower Support Costs**: Decreased reliance on technical teams for data access.
- **Increased Data Literacy**: Broader organizational access to data.
- **Improved Decision-Making**: Facilitates faster, informed business actions.

---

## 2. System Overview

### High-Level Architecture
The system is structured as a modular, service-oriented application. At its core is a FastAPI web server that exposes HTTP endpoints for natural language queries. Incoming requests are processed through a multi-step, agentic workflow orchestrated by a LangGraph state machine. This workflow includes semantic caching, context detection, query generation, execution, and error correction, all powered by AI agents utilizing LangChain and Azure OpenAI. The system interacts with MongoDB for data storage and employs Supabase for vector-based semantic caching.

### Functional Capabilities
- Accepts natural language queries via an HTTP API.
- Validates and processes incoming input.
- Checks for similar cached queries using embeddings and LLM verification.
- If no cache hit occurs, detects context and generates MongoDB queries with LLMs.
- Executes queries on MongoDB and improves queries if execution fails.
- Returns results, logs, and reasoning steps to users.
- Provides endpoints for recent searches and autocomplete suggestions.

### Conceptual Workflow
1. User submits a query via API.
2. The system validates the input and checks for cached queries.
3. If a cache miss occurs, it detects context and generates a new query.
4. The query is executed on MongoDB.
5. If execution fails, the query is improved using LLM feedback.
6. Results and logs are returned to the user.
7. New queries and embeddings are stored for future cache hits.

### Key Components
- **API Layer**: Handles HTTP requests and responses.
- **Workflow Layer**: Orchestrates the multi-step AI workflow.
- **AI Agents**: Perform context detection, query generation, and refinement.
- **Semantic Caching Layer**: Manages query embeddings.
- **Database Access Layer**: Executes MongoDB queries.
- **Validation Layer**: Ensures query safety and input validity.
- **Logging Layer**: Maintains logs and reasoning steps.

---

## 3. Detailed Architecture

### Architecture Pattern
The architecture follows an agentic, service-oriented pattern with a LangGraph state machine orchestrating the workflow. Each workflow step is handled by a specialized agent or function, with state passed and updated throughout the process. The system maintains a clear separation of concerns across its modules:
- **API Layer**: FastAPI endpoints (`src/backend/routes/query.py`)
- **Workflow Orchestration**: LangGraph state machine (`src/ai/graphs/graph_builder.py`)
- **Agents**: LLM-powered context detection, query generation, and improvement (`src/ai/agents/generation/*`)
- **Semantic Caching**: Embedding generation and storage in Supabase (`src/backend/embedding/embedding.py`)
- **Database Access**: MongoDB query execution (`src/backend/db/mongodb.py`)
- **Validation**: Input and query safety checks (`src/backend/validators/validators.py`)
- **Logging**: Observability (`logger.py`)

### Technology Stack
- **Languages**: Python
- **Frameworks**: FastAPI, LangChain, LangGraph
- **Libraries**: Uvicorn, pymongo, sentence-transformers, faiss-cpu, marshmallow, pydantic, python-dotenv
- **Databases**: MongoDB (primary data), Supabase (vector database for embeddings)
- **Infrastructure**: Docker, Docker Compose
- **AI/ML**: Azure OpenAI (LLM), LangChain (agent orchestration), sentence-transformers (embedding generation), faiss-cpu (vector search)

### Design Decisions
- **Agentic Workflow**: Modular structure enhances extensibility and maintainability via the LangGraph state machine.
- **Semantic Caching**: Reduces load on LLMs and improves performance for repeated queries.
- **Prompt Engineering**: Tailored prompts for each agent step ensure accuracy and control.
- **Repository Pattern**: Abstracts database access for flexibility.
- **Factory Pattern**: Simplifies LLM instantiation for easy configuration.
- **Detailed Logging**: Ensures traceability and facilitates debugging.

### Scalability and Performance
- **Semantic Caching**: Decreases LLM and database load for frequent queries.
- **Agent Modularity**: Agents can be independently scaled or replaced.
- **Stateless API**: Promotes horizontal scalability by ensuring each request is independent.
- **Dockerized Deployment**: Supports container orchestration.
- **Potential Bottlenecks**: LLM calls for every cache check can be resource-intensive; asynchronous database operations are not implemented.

### Integration Points
- **Supabase**: For vector embedding storage and retrieval.
- **Azure OpenAI**: For LLM-powered agents.
- **MongoDB**: For data storage and query execution.

---

## 4. Component Details

### FastAPI Application Layer
- **Purpose**: Exposes HTTP endpoints for processing queries, retrieving recent searches, and providing autocomplete suggestions.
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
  - Returns HTTP 400 for input validation errors.
  - Returns HTTP 500 for generic exceptions.
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
  - `GenerationState` (src/ai/state/generation_state.py): State dictionary passed through the workflow.
- **Error Handling**:
  - Generic exception handling is implemented, with errors logged.

### AI Agents
- **Purpose**: LLM-powered agents for context detection, query generation, and improvement.
- **Implementation**:
  - `ContextDetectionAgent` (src/ai/agents/generation/context_detection_agent.py): Detects relevant collections and reasoning steps.
  - `QueryGenerationAgent` (src/ai/agents/generation/query_generation_agent.py): Generates MongoDB queries.
  - `ImprovementAgent` (src/ai/agents/generation/improvement_agent.py): Enhances failing queries.
- **Key Files**:
  - `src/ai/agents/generation/context_detection_agent.py`
  - `src/ai/agents/generation/query_generation_agent.py`
  - `src/ai/agents/generation/improvement