# Code Analysis: AI-Powered Natural Language to MongoDB Query Service

**Analysis Date:** 2025-11-19  
**Branch:** Not Specified  
**Total Files Analyzed:** 0  

---

## Executive Summary

### Overview of the Software
The AI-Powered Natural Language to MongoDB Query Service empowers users to perform database queries using natural language. By simply asking questions like "Show me jobs assigned to Anthony Adkins," users can seamlessly interact with MongoDB without needing to learn any query language. The system interprets these requests, generates the corresponding MongoDB queries, executes them, and returns the results.

### Business Value and Purpose
This innovative service democratizes data access, eliminating technical barriers that typically hinder non-technical users. Business analysts, managers, and support staff can quickly retrieve and analyze data, thereby minimizing reliance on technical teams. The automation of query generation and the use of AI for contextual understanding enhance productivity and promote data-driven agility within organizations.

### Key Features and Capabilities
- **Natural Language Querying**: Enables users to interact with databases using everyday language.
- **AI-Driven Query Generation**: Utilizes Large Language Models (LLMs) via Azure OpenAI and LangChain for query creation and enhancement.
- **Semantic Caching**: Remembers past queries for quicker future responses using vector embeddings through Supabase.
- **Context Detection**: Automatically identifies relevant collections and fields.
- **Error Correction**: Enhances failing queries based on feedback from LLMs.
- **Recent Searches and Autocomplete**: Improves user experience through query history and suggestion features.
- **Comprehensive Logging**: Ensures transparency and traceability for all operations.

### Target Users and Use Cases
- **Business Analysts**: Access data for reporting and insights.
- **Operations Managers**: Monitor key metrics and generate operational analyses.
- **Customer Support Representatives**: Quickly retrieve customer-related data.
- **Data Scientists**: Perform exploratory data analysis.

### Business Impact and Outcomes
- **Accelerated Time-to-Insight**: Provides immediate data access without technical delays.
- **Reduced Support Costs**: Diminishes the volume of requests to technical teams for data retrieval.
- **Enhanced Data Literacy**: Broadens organizational access to critical data.
- **Improved Decision-Making**: Facilitates faster, informed business decisions.

---

## System Overview

### High-Level Architecture
The system employs a modular, service-oriented architecture. At its core is a FastAPI web server that provides HTTP endpoints for natural language queries. Incoming requests are processed through a multi-step workflow orchestrated by a LangGraph state machine. This workflow includes semantic caching, context detection, query generation, execution, and error correction, all supported by AI agents utilizing LangChain and Azure OpenAI. Data is managed through MongoDB, with vector-based semantic caching facilitated by Supabase.

### System Capabilities and Functionality
- Accepts natural language queries via HTTP API.
- Validates and processes user input.
- Checks for similarities with cached queries using embeddings and LLM validation.
- If no cache hit occurs, it detects context and generates MongoDB queries using LLMs.
- Executes queries on MongoDB.
- Improves queries if execution fails.
- Returns results, logs, and reasoning steps.
- Provides recent searches and autocomplete suggestions.

### Conceptual Workflow
1. User submits a query via API.
2. The system validates the input and checks for cached queries.
3. Upon a cache miss, it detects context and generates a new query using an LLM.
4. Executes the generated query on MongoDB.
5. If execution fails, it attempts to improve the query using feedback from the LLM.
6. Returns results, logs, and reasoning to the user.
7. Stores new queries and embeddings for potential future cache hits.

### Key Components
- **API Layer**: Manages HTTP request and response handling.
- **Agentic Workflow Layer**: Coordinates the multi-step AI workflow.
- **AI Agents**: Conduct context detection, query generation, and enhancement.
- **Semantic Caching Layer**: Handles storage and retrieval of query embeddings.
- **Database Access Layer**: Executes MongoDB queries.
- **Validation Layer**: Ensures the safety and correctness of input and queries.
- **Logging Layer**: Maintains detailed records and reasoning for all processes.

---

## Detailed Architecture

### Architecture Pattern
This architecture adheres to an agentic, service-oriented model, utilizing a state machine (LangGraph) to orchestrate workflows. Each workflow step is managed by a specialized agent or function, with state updates occurring throughout the process. The system maintains a clear separation of concerns across its modules:
- **API Layer**: FastAPI endpoints defined in `src/backend/routes/query.py`
- **Workflow Orchestration**: LangGraph state machine located in `src/ai/graphs/graph_builder.py`
- **Agents**: Context detection, query generation, and improvement agents in `src/ai/agents/generation/*`
- **Semantic Caching**: Embedding generation and storage via Supabase in `src/backend/embedding/embedding.py`
- **Database Access**: MongoDB query execution managed by `src/backend/db/mongodb.py`
- **Validation**: Input safety checks in `src/backend/validators/validators.py`
- **Logging**: Observability provided by `logger.py`

### Technology Stack
- **Languages**: Python
- **Frameworks**: FastAPI, LangChain, LangGraph
- **Libraries**: Uvicorn, pymongo, sentence-transformers, faiss-cpu, marshmallow, pydantic, python-dotenv
- **Databases**: MongoDB for data storage; Supabase for vector database services
- **Infrastructure**: Docker and Docker Compose
- **AI/ML Technologies**: Azure OpenAI (LLMs), LangChain (agent orchestration), sentence-transformers (embeddings), faiss-cpu (vector search)

### System-Level Design Decisions
- **Agentic Workflow**: Modular, extensible design facilitated by the LangGraph state machine.
- **Semantic Caching**: Reduces LLM calls and enhances performance for similar queries.
- **Prompt Engineering**: Tailored prompts for each agent step to ensure accuracy.
- **Repository Pattern**: Provides an abstraction layer for database access, enhancing flexibility.
- **Factory Pattern**: Simplifies LLM instantiation for configuration and swapping.
- **Detailed Logging**: Ensures thorough traceability and debuggability.

### Scalability and Performance
- **Semantic Caching**: Mitigates LLM and database load for repeated queries.
- **Agent Modularity**: Allows independent scaling or replacement of agents.
- **Stateless API**: Ensures independence of each request, enabling horizontal scaling.
- **Docker Deployment**: Supports container orchestration for efficient scaling.
- **Potential Bottlenecks**: High resource utilization for LLM calls during cache checks; lack of asynchronous database operations.

### Integration Points
- **Supabase**: For storage and retrieval of vector embeddings.
- **Azure OpenAI**: For LLM-powered agent functionalities.
- **MongoDB**: For data storage and query execution.

---

## Component Details

### FastAPI Application Layer
- **Purpose**: Provides HTTP endpoints for query processing, recent searches, and autocomplete functionality.
- **Implementation**: Defined in `src/backend/routes/query.py` and initialized in `src/backend/__init__.py`.
- **Key APIs**:
  - `POST /query`: Main endpoint for processing natural language queries.
  - `GET /api/recent-searches`: Retrieves recent queries.
  - `GET /autocomplete/`: Offers autocomplete suggestions.
- **Key Files**:
  - `src/backend/routes/query.py`: Contains endpoint definitions.
  - `src/backend/__init__.py`: Initializes the FastAPI application.
  - `run.py`: Entry point for running the application.
- **Data Structures**:
  - `QuerySchema` (src/backend/schema/query.py): Defines the request schema.
- **Error Handling**:
  - Returns HTTP 400 for input validation errors.
  - Returns HTTP 500 for generic exceptions.
  - Logs errors via `logger.py`.

### Agentic Workflow Layer
- **Purpose**: Orchestrates the multi-step workflow using LangGraph.
- **Implementation**: Defined in `src/ai/graphs/graph_builder.py` (workflow definition) and `src/ai/graphs/graph_executor.py` (execution).
- **Workflow Nodes**:
  - `check_cache`: Verifies cached queries based on embeddings.
  - `detect_context`: Engages the context detection agent.
  - `generate`: Utilizes the query generation agent.
  - `execute`: Executes the MongoDB query.
  - `improve`: Engages the query improvement agent.
- **Key Files**:
  - `src/ai/graphs/graph_builder.py`: Defines the state machine.
  - `src/ai/graphs/graph_executor.py`: Contains the workflow execution logic.
- **Data Structures**:
  - `GenerationState` (src/ai/state/generation_state.py): Contains state information passed through the workflow.
- **Error Handling**:
  - Implements generic exception handling with error logging.

### AI Agents
- **Purpose**: Execute LLM-driven tasks related to context detection, query generation, and query enhancement.
- **Implementation**:
  - `ContextDetectionAgent` (src/ai/agents/generation/context_detection_agent.py): Identifies relevant collections and logic.
  - `QueryGenerationAgent` (src/ai/agents/generation/query_generation_agent.py): Generates MongoDB queries.
  - `ImprovementAgent` (src/ai/agents