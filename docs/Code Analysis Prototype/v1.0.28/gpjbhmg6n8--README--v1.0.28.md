# Code Analysis - Developer Handbook

**Analysis Date:** 2025-11-21T15:39:26.538Z
**Branch:** main
**Total Files Analyzed:** 0

---

# AI-Powered Query Processing Platform: Comprehensive Handbook

## 1. Executive Summary (Business Level)

### What the Software Does
This platform delivers an AI-powered backend for advanced query processing. It leverages large language models (LLMs), prompt engineering, and vector embeddings to interpret, enhance, and respond to user queries. The system is designed to support semantic search, context-aware information retrieval, and automated query improvement, providing intelligent, contextually relevant answers to complex user requests.

### Business Value and Purpose


![Functional Flow Diagram](code-analysis-code-analysis-functional-flow-diagram.png)

The platform enables organizations to offer advanced, AI-driven search and information retrieval capabilities. By integrating LLMs and vector embeddings, it allows users to submit natural language queries and receive semantically relevant results, improving user satisfaction and operational efficiency. The modular architecture supports rapid adaptation to new business requirements and evolving AI technologies.

### Key Features and Capabilities
- **Semantic Search**: Understands and retrieves information based on meaning, not just keywords.
- **Context Detection**: Identifies the context of queries for more accurate responses.
- **Query Improvement**: Suggests enhancements to user queries for better results.
- **AI Agent Orchestration**: Modular agents handle different aspects of query processing.
- **Prompt Engineering**: Centralized management of prompt templates for LLMs.
- **Vector Embedding Integration**: Supports semantic retrieval using vector representations.
- **API-First Design**: Exposes capabilities via secure, validated API endpoints.
- **Containerized Deployment**: Uses Docker and Docker Compose for scalable, reproducible environments.

### Target Users and Use Cases
- **Business Users**: Retrieve insights and information using natural language queries.
- **Developers**: Integrate, extend, and maintain AI agents and backend services.
- **Data Scientists**: Tune prompt templates, embeddings, and agent logic for optimal performance.

### Business Impact and Outcomes
- Enhanced user experience through intelligent, context-aware responses.
- Increased efficiency in information retrieval and decision-making.
- Future-proof architecture supporting rapid integration of new AI technologies.

## 2. System Overview (All Levels)

### High-Level Architecture Explanation
The system is organized into modular components, each responsible for a distinct aspect of query processing. The architecture separates AI agent logic, prompt engineering, embedding management, backend API routing, data access, and utility functions. This modularity enables independent development, testing, and scaling of each component.

### System Capabilities and Functionality
- **AI Agents**: Handle context detection, query generation, improvement, and relevance checking.
- **Prompt Engineering**: Manages prompt templates for LLMs.
- **Embedding Service**: Generates and manages vector embeddings for semantic search.
- **Backend API**: Exposes endpoints for query submission and response retrieval.
- **Database Layer**: Manages data storage and metadata using MongoDB.
- **Utilities and Validators**: Provide helper functions and enforce data integrity.

### How the System Works (Conceptual Flow)
1. User submits a query via the API.
2. API validates and authorizes the request.
3. Request is routed to the appropriate AI agent.
4. Agent processes the query using prompt engineering and LLMs.
5. Agent may interact with the embedding service and database.
6. Agent formulates a response.
7. API returns the response to the user.

### Key Components and Their Roles
- **AI Agents**: Orchestrate LLMs and tools for query understanding and response generation.
- **Prompt Engineering**: Centralizes prompt logic for consistency and reusability.
- **Embedding Service**: Enables semantic retrieval via vector search.
- **Backend API**: Handles external communication, validation, and security.
- **Database Layer**: Stores data and metadata for retrieval and analysis.

## 3. Detailed Architecture (Architect & Developer Level)

### Complete Architecture Pattern Explanation
The platform employs a service-oriented, modular architecture. Each functional area is encapsulated in a dedicated Python module, promoting separation of concerns and ease of maintenance. The backend API acts as the entry point, delegating business logic to specialized service modules (agents, embeddings, db). Containerization via Docker ensures consistent deployment across environments.

### Technology Stack Breakdown
- **Languages**: Python
- **Frameworks**: Python/pip
- **AI/ML Frameworks**: AI Agent Framework, Prompt Engineering, Vector Embeddings
- **Databases**: MongoDB (implied by mongodb.py)
- **Infrastructure**: Docker, Docker Compose
- **Build Tools**: None specified
- **Testing Tools**: None specified

### System-Level Design Decisions
- **Modularization**: Each major function is a separate module for extensibility.
- **API-First**: All capabilities are exposed via RESTful API endpoints.
- **Containerization**: Docker and Docker Compose for reproducible, scalable deployments.
- **Security**: Authorization enforced at the API layer.

### Scalability and Performance Considerations
- **Containerization**: Supports horizontal scaling of services.
- **Separation of Concerns**: Enables independent scaling and optimization of agents, embeddings, and backend services.
- **Vector Embeddings**: Facilitates efficient semantic search for large datasets.

### Integration Points
- **LLM Providers**: Integrated via agent and prompt modules.
- **Vector Embedding Services**: Managed by embedding module.
- **MongoDB**: Used for data and metadata storage.

## 4. Component Details (Developer & Architect Level)

### AI Agents (src/ai/agents)
- **Purpose**: Implement context detection, query generation, improvement, and relevance checking.
- **Implementation**: Each agent is a Python module (e.g., context_detection_agent.py, improvement_agent.py).
- **APIs/Functions**: Expose agent-specific methods for processing queries.
- **Key Files**:
  - `context_detection_agent.py`: Detects query context.
  - `improvement_agent.py`: Suggests query improvements.
  - `query_generation_agent.py`: Generates queries for downstream processing.
  - `check_relevance.py`: Checks relevance of results.
- **Data Structures**: Agent state objects, prompt templates.
- **Error Handling**: Implements try/except blocks and logging for agent errors.
- **Dependencies**: Relies on prompt templates, tools, and configuration modules.

### Prompt Engineering (src/ai/agents/prompts)
- **Purpose**: Centralizes prompt templates and construction logic.
- **Implementation**: `prompts.py` contains prompt definitions and helper functions.
- **APIs/Functions**: Functions for retrieving and formatting prompts.
- **Key Files**: `prompts.py`
- **Data Structures**: Prompt templates (strings, dicts).
- **Error Handling**: Handles missing/malformed templates.

### Graph Builder/Executor (src/ai/graphs)
- **Purpose**: Orchestrates multi-step agent workflows.
- **Implementation**: `graph_builder.py` constructs graphs; `graph_executor.py` executes them.
- **APIs/Functions**: Functions for building and executing agent graphs.
- **Key Files**: `graph_builder.py`, `graph_executor.py`
- **Data Structures**: Graph representations, node/task objects.
- **Error Handling**: Handles invalid graphs and execution errors.

### LLM Integration (src/ai/llm)
- **Purpose**: Interfaces with LLMs and enforces output guardrails.
- **Implementation**: `llm.py` for LLM calls; `guard.py` for output validation.
- **APIs/Functions**: Functions for invoking LLMs and validating outputs.
- **Key Files**: `llm.py`, `guard.py`
- **Data Structures**: LLM request/response objects.
- **Error Handling**: Handles LLM errors and output validation.

### Backend API (src/backend/routes)
- **Purpose**: Exposes API endpoints for query submission and response retrieval.
- **Implementation**: `query.py` defines the /api/query endpoint.
- **APIs/Functions**: API route handlers for processing requests.
- **Key Files**: `query.py`, `__init__.py`
- **Data Structures**: Request/response schemas.
- **Error Handling**: Handles invalid requests, authorization failures, and internal errors.

### Embedding Service (src/backend/embedding)
- **Purpose**: Generates and manages vector embeddings.
- **Implementation**: `embedding.py` handles embedding logic.
- **APIs/Functions**: Functions for generating and retrieving embeddings.
- **Key Files**: `embedding.py`
- **Data Structures**: Embedding vectors, metadata.
- **Error Handling**: Handles embedding errors and storage issues.

### Database Layer (src/backend/db)
- **Purpose**: Manages data storage and metadata.
- **Implementation**: `mongodb.py` for MongoDB integration; `metadata.py` for metadata management.
- **APIs/Functions**: Functions for data access and manipulation.
- **Key Files**: `mongodb.py`, `metadata.py`
- **Data Structures**: MongoDB collections, metadata schemas.
- **Error Handling**: Handles database errors and data validation.

### Configuration (src/ai/config, src/backend/config)
- **Purpose**: Centralizes configuration for AI and backend components.
- **Implementation**: `config.py` files define configuration parameters.
- **APIs/Functions**: Functions for loading and validating configuration.
- **Key Files**: `config.py`
- **Data Structures**: Config objects, environment variables.
- **Error Handling**: Handles missing/invalid configuration.

### Utilities (src/ai/utils, src/backend/utils)
- **Purpose**: Provides helper functions and metadata extraction.
- **Implementation**: `helpers.py`, `role.py`, `metadata_extractor.py`, `misc.py`.
- **APIs/Functions**: Utility functions for various tasks.
- **Key Files**: `helpers.py`, `role.py`, `metadata_extractor.py`, `misc.py`
- **Data Structures**: Utility classes and functions.
- **Error Handling**: Handles utility-level errors.

### Validators (src/backend/validators)
- **Purpose**: Implements request and data validation logic.
- **Implementation**: `validators.py` defines validation functions.
- **APIs/Functions**: Functions for validating requests and data.
- **Key Files**: `validators.py`
- **Data Structures**: Validation schemas.
- **Error Handling**: Handles validation errors and returns appropriate responses.

## 5. How It Works (All Levels)

### 5.1 User Request Flow (Step-by-Step)
1. **User submits a query** via the `/api/query` endpoint (src/backend/routes/query.py).
2. **API receives the request** and validates input using schema and validator modules (src/backend/schema/query.py, src/backend/validators/validators.py).
3. **Authorization is checked** to ensure the user has access (src/backend/routes/query.py).
4. **Request is routed to the appropriate AI agent** based on the query type or context (src/ai/agents/generation/context_detection_agent.py, query_generation_agent.py).
5. **Agent processes the query** using prompt templates (src/ai/agents/prompts/prompts.py) and may invoke LLMs (src/ai/llm/llm.py).
6. **If semantic retrieval is needed**, the agent interacts with the embedding service (src/backend/embedding/embedding.py) to generate or retrieve vector embeddings.
7. **Agent may access the database** for metadata or additional information (src/backend/db/mongodb.py, metadata.py).
8. **Agent formulates a response**, possibly improving or reformatting it (src/ai/agents/generation/improvement_agent.py).
9. **API returns the response** to the user in JSON format.

### 5.2 LangGraph/Agent State Machine Flow
- **Graph Builder/Executor** (src/ai/graphs/graph_builder.py, graph_executor.py) constructs and executes agent workflows as directed acyclic graphs (DAGs).
- **State Machine Nodes**: Each agent or tool is a node; nodes perform tasks such as context detection, query generation, or embedding retrieval.
- **Edge Conditions**: Flow proceeds based on agent outputs (e.g., if context detected, proceed to query generation; else, fallback to improvement agent).
- **State Updates**: State objects are updated at each node with intermediate results.
- **Tool Calls**: Tools (src/ai/agents/tools/tools.py) are invoked as needed by agents.
- **Complete Flow Example**: User query → Context Detection Agent → If context found, Query Generation Agent → Embedding Service → Database → Response.

### 5.3 Decision Logic
- **Cache Lookup Logic**: Not explicitly found in code structure; may be implemented within agent or embedding modules.
- **LLM Invocation Logic**: Agents decide when to call LLMs based on query type and context (src/ai/agents/generation/context_detection_agent.py).
- **Database Query Generation**: Agents or embedding service generate queries for MongoDB (src/backend/db/mongodb.py).
- **Agent Selection**: Based on query context/type (src/ai/agents/generation/context_detection_agent.py).
- **Error Handling**: Try/except blocks and logging in agent and backend modules.

### 5.4 Request/Response Patterns
- **Request Format**: JSON payload with query text and optional context.
- **Response Format**: JSON with results, metadata, and possible improvement suggestions.
- **Data Transformations**: Input validation, prompt formatting, embedding vectorization, response formatting.

### 5.5 Data Flow Through System
- **User Query → API Route → AI Agent → (LLM/Embedding/DB) → Response Formatter → User**
- Data is transformed at each stage: validated, contextualized, embedded, and formatted.

### 5.6 Business Workflows
- **Semantic Search**: User query is embedded and matched against stored vectors.
- **Query Improvement**: Agent suggests reformulations for better results.
- **Context Detection**: Agent identifies context to route query appropriately.

### 5.7 Error Handling Flows
- **Validation Errors**: Return error response to user.
- **Authorization Failures**: Return unauthorized response.
- **Agent/LLM/Embedding Errors**: Log error, return fallback or error message.
- **Database Errors**: Log error, return error response.

### 5.8 State Management
- **Agent State**: Maintained within agent modules (src/ai/state/generation_state.py).
- **Session Management**: Not explicitly found; may be stateless per request.

## 6. API Documentation (Developer Level)

### Endpoints
- **POST /api/query** (src/backend/routes/query.py)
  - **Description**: Submits a query for processing.
  - **Request Body**: JSON with fields such as `query_text`, `context`, `user_id`.
  - **Response**: JSON with results, metadata, and suggestions.
  - **Authentication**: Authorization enforced (src/backend/routes/query.py).
  - **Validation**: Input validated using schema (src/backend/schema/query.py) and validators (src/backend/validators/validators.py).
  - **Error Responses**: Returns error codes/messages for invalid input, unauthorized access, or internal errors.

- **Other Endpoints**: `/api/__init__` (src/backend/routes/__init__.py) - likely for API initialization or health check.

### Request/Response Formats
- **Request Example**:
  ```json
  {
    "query_text": "Find all documents about AI agents",
    "context": "research",
    "user_id": "user123"
  }
  ```
- **Response Example**:
  ```json
  {
    "results": [...],
    "metadata": {...},
    "suggestions": ["Try refining your query with more specific terms."]
  }
  ```

### Authentication Mechanisms
- **Authorization**: Checked in API route handlers (src/backend/routes/query.py).

### Rate Limiting
- **Not found in code**: No explicit rate limiting logic detected.

### Error Responses
- **Validation Errors**: Return 400 with error message.
- **Authorization Errors**: Return 401/403 with error message.
- **Internal Errors**: Return 500 with error message.

### API Versioning


![API Flow Diagram](code-analysis-code-analysis-api-flow-diagram.png)

- **Not found in code**: No explicit versioning detected in routes.

### Code Examples
- **API Handler** (src/backend/routes/query.py):
  ```python
  @app.route('/api/query', methods=['POST'])
  def handle_query():
      data = request.get_json()
      # Validate and process query
      ...
  ```

## 7. Data Architecture (Architect & Developer Level)

### Database Schemas and Relationships
- **MongoDB**: Used for data and metadata storage (src/backend/db/mongodb.py).
- **Metadata Schema**: Defined in src/backend/db/metadata.py.
- **Collections**: Not explicitly listed; inferred from code structure.

### Data Access Patterns
- **Direct Access**: Functions in mongodb.py for CRUD operations.
- **Metadata Management**: Functions in metadata.py for handling metadata.

### Caching Strategies
- **Not found in code**: No explicit caching logic detected.

### Data Validation Rules
- **Validators**: Implemented in src/backend/validators/validators.py.
- **Schema**: Defined in src/backend/schema/query.py.

### Migration Strategies
- **Not found in code**: No migration scripts or logic detected.

### Backup and Recovery
- **Not found in code**: No backup scripts or recovery procedures detected.

### Data Flow Patterns


![Data Flow Diagram](code-analysis-code-analysis-data-flow-diagram.png)

- **ETL/Streaming/Batch**: Not explicitly found; data flows are request-driven.

## 8. Design Patterns (Architect Level)

### Patterns Used and Why
- **Modularization**: Each function is a separate module for maintainability and extensibility.
- **Service Layer**: API routes delegate logic to service modules for separation of concerns.

### Architectural Decisions and Trade-Offs
- **Modularity**: Facilitates independent development and scaling.
- **Containerization**: Ensures reproducibility and scalability.

### Pattern Interactions
- **Service Layer + Modularization**: API routes interact with modular service components.

### Code Examples
- **Service Layer** (src/backend/routes/query.py):
  ```python
  from src.ai.agents.generation.context_detection_agent import detect_context
  ...
  def handle_query():
      ...
      context = detect_context(query_text)
      ...
  ```

### Pattern Locations
- **Modularization**: All major directories (src/ai/agents/, src/backend/db/, etc.)
- **Service Layer**: src/backend/routes/query.py

## 9. Security (All Levels)

### Security Mechanisms
- **Authorization**: Enforced in API route handlers (src/backend/routes/query.py).
- **Input Validation**: Enforced via validators and schemas (src/backend/validators/validators.py, src/backend/schema/query.py).
- **Data Protection**: No explicit encryption or data protection logic detected.

## 10. Development Guide (Developer Level)

### Setup and Installation
1. **Clone the repository**.
2. **Install dependencies** using pip:
   ```bash
   pip install -r requirements.txt
   ```
3. **Set up environment variables** using `.env.example` as a template.
4. **Build and run with Docker Compose**:
   ```bash
   docker-compose up --build
   ```

### Configuration
- **Configuration files**: `src/ai/config/config.py`, `src/backend/config/config.py`, `.env.example`
- **Environment variables**: Define API keys, database URIs, and other settings.

### Running Tests
- **Test files**: `tests/tests.py` (empty), `tests/__init_.py` (empty)
- **No test logic detected**: Add tests to `tests/tests.py`.

### Debugging Tips
- **Logging**: Use `logger.py` for debugging output.
- **Error Handling**: Check logs for errors in agent, embedding, and backend modules.

### Common Issues
- **Missing configuration**: Ensure all required environment variables are set.
- **Database connection errors**: Verify MongoDB is running and accessible.
- **Dependency issues**: Reinstall dependencies with pip.

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
│   ├── __init__.py
│   ├── ai/
│   │   ├── __init__.py
│   │   ├── agents/
│   │   │   ├── __init__.py
│   │   │   ├── generation/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── context_detection_agent.py
│   │   │   │   ├── improvement_agent.py
│   │   │   │   ├── query_generation_agent.py
│   │   │   │   ├── sample_queries.py
│   │   │   ├── misc/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── check_relevance.py
│   │   │   ├── prompts/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── prompts.py
│   │   │   ├── tools/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── tools.py
│   ├── config/
│   │   ├── __init__.py
│   │   ├── config.py
│   ├── graphs/
│   │   ├── __init__.py
│   │   ├── graph_builder.py
│   │   ├── graph_executor.py
│   ├── llm/
│   │   ├── __init__.py
│   │   ├── guard.py
│   │   ├── llm.py
│   ├── state/
│   │   ├── __init__.py
│   │   ├── generation_state.py
│   ├── utils/
│   │   ├── __init__.py
│   │   ├── helpers.py
│   │   ├── role.py
├── backend/
│   ├── __init__.py
│   ├── config/
│   │   ├── __init__.py
│   │   ├── config.py
│   ├── db/
│   │   ├── __init__.py
│   │   ├── metadata.py
│   │   ├── mongodb.py
│   ├── embedding/
│   │   ├── __init__.py
│   │   ├── embedding.py
│   ├── models/
│   │   ├── __init__.py
│   ├── routes/
│   │   ├── __init__.py
│   │   ├── query.py
│   ├── schema/
│   │   ├── __init__.py
│   │   ├── query.py
│   ├── utils/
│   │   ├── __init__.py
│   │   ├── metadata_extractor.py
│   │   ├── misc.py
│   ├── validators/
│   │   ├── __init__.py
│   │   ├── validators.py
├── tests/
│   ├── __init_.py
│   ├── tests.py
```

### Key Files Organized by Component
- **AI Agents**: `src/ai/agents/generation/context_detection_agent.py`, `improvement_agent.py`, `query_generation_agent.py`, `sample_queries.py`, `misc/check_relevance.py`
- **Prompt Engineering**: `src/ai/agents/prompts/prompts.py`
- **Graph Builder/Executor**: `src/ai/graphs/graph_builder.py`, `graph_executor.py`
- **LLM Integration**: `src/ai/llm/llm.py`, `guard.py`
- **Embedding Service**: `src/backend/embedding/embedding.py`
- **Database Layer**: `src/backend/db/mongodb.py`, `metadata.py`
- **Backend API**: `src/backend/routes/query.py`, `__init__.py`
- **Validators**: `src/backend/validators/validators.py`
- **Configuration**: `src/ai/config/config.py`, `src/backend/config/config.py`, `.env.example`
- **Utilities**: `src/ai/utils/helpers.py`, `role.py`, `src/backend/utils/metadata_extractor.py`, `misc.py`

### Entry Points
- **API**: `src/backend/routes/query.py`
- **Application Runner**: `run.py`
- **Docker Entrypoint**: `Dockerfile`, `docker-compose.yml`

### Configuration Files
- `.env.example`, `src/ai/config/config.py`, `src/backend/config/config.py`

### Test Files
- `tests/tests.py`, `tests/__init_.py`

### Documentation Files
- `README.md`, `Code-Analysis-Review.md`

---

## 12. Troubleshooting and Best Practices
- **Always validate configuration before running the system.**
- **Use Docker Compose for consistent local development and deployment.**
- **Extend agents and prompts by adding new modules in the appropriate directories.**
- **Monitor logs for errors and performance bottlenecks.**
- **Add tests to `tests/tests.py` to ensure code quality.**

---

## 13. Architecture Diagram Descriptions

### architectureDiagram
"""
A modular architecture diagram showing the following components:
- User (external actor)
- API Layer (src/backend/routes)
- AI Agents (src/ai/agents)
- Prompt Engineering (src/ai/agents/prompts)
- Embedding Service (src/backend/embedding)
- Database Layer (src/backend/db)
- Utilities/Validators (src/ai/utils, src/backend/utils, src/backend/validators)
- Configuration (src/ai/config, src/backend/config)
Arrows indicate data flow: User → API → Agents → (Prompt/Embedding/DB) → API → User. Show Docker as the deployment boundary.
"""

### functionalFlowDiagram
"""
A flowchart showing:
- User submits query
- API receives and validates request
- Authorization check
- Route to appropriate agent
- Agent processes query (may call LLM, embedding, DB)
- Agent formulates response
- API returns response to user
Decision points: validation, authorization, agent selection, error handling.
"""

### sequenceDiagram


![Sequence Diagram](code-analysis-code-analysis-sequence-diagram-personas.png)

"""
sequenceDiagram
  participant User
  participant API
  participant Agent
  participant Embedding
  participant DB
  User->>API: POST /api/query
  API->>Agent: process_query()
  Agent->>Embedding: get_embedding()
  Embedding->>DB: fetch_vector()
  DB-->>Embedding: vector
  Embedding-->>Agent: embedding
  Agent-->>API: response
  API-->>User: JSON response
"""

### codeFlowDiagram
"""
A step-by-step code execution flow:
- User sends POST request to /api/query
- API handler parses and validates input
- Authorization is checked
- Handler calls agent's process_query()
- Agent uses prompt engineering and may call LLM
- Agent may call embedding service and database
- Agent assembles response
- API handler returns response to user
Include error handling branches at each step.
"""

### apiFlowDiagram
"""
A diagram showing:
- User → /api/query (POST)
- API validates and authorizes
- API calls agent
- Agent may call embedding and DB
- API returns JSON response
Show request/response formats at each stage.
"""

### dataFlowDiagram
"""
A comprehensive data flow diagram:
- User Query (JSON) → API Route (/api/query)
- API Route → AI Agent (query object)
- AI Agent → Prompt Engineering (prompt template)
- AI Agent → LLM (prompt)
- AI Agent → Embedding Service (text)
- Embedding Service → Database (vector fetch/store)
- AI Agent ← Embedding/DB (embedding/vector)
- AI Agent → API Route (response object)
- API Route → User (JSON response)
Arrows are labeled with data types (query, prompt, vector, response).
"""

### componentClassDiagram


![Component/Class Diagram](code-analysis-code-analysis-component-class-diagram.png)

"""
A class diagram showing:
- Agent classes (ContextDetectionAgent, ImprovementAgent, etc.)
- PromptManager class
- EmbeddingService class
- DatabaseManager class
- APIHandler class
Show inheritance and composition relationships.
"""

### deploymentDiagram
"""
A deployment diagram showing:
- User (external)
- API Service (Docker container)
- AI Agent Service (Docker container)
- Embedding Service (Docker container)
- MongoDB (Docker container)
- Docker Compose orchestrates all containers
Show network connections and environment variable configuration.
"""

## Additional Diagrams

*Additional diagrams generated via Eraser.io*

### Architecture Diagram

![Architecture Diagram](code-analysis-code-analysis-architecture-diagram.png)

### Code Flow Diagram

![Code Flow Diagram](code-analysis-code-analysis-code-flow-diagram.png)

