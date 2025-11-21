# undefined/undefined - Developer Handbook

**Analysis Date:** 2025-11-21T11:16:24.016Z
**Branch:** undefined
**Total Files Analyzed:** 0

---

# AI Agent Backend Platform Handbook

## 1. Executive Summary (Business Level)

### What the Software Does
This platform provides a robust backend for advanced AI-driven query processing. It enables users to submit queries via a RESTful API, which are then processed by a series of intelligent AI agents. These agents perform context detection, generate or improve queries, and leverage vector embeddings to deliver context-aware, semantically rich responses. The system is designed for applications such as knowledge retrieval, semantic search, and conversational AI, offering a modular and extensible foundation for AI-powered backend services.

### Business Value and Purpose
The platform empowers organizations to build intelligent applications that require deep understanding of user queries and context. By orchestrating multiple AI agents and leveraging prompt engineering and vector embeddings, the system delivers high-quality, relevant responses. This enhances user experience, increases automation, and enables new business models in areas like customer support, enterprise search, and digital assistants.

### Key Features and Capabilities
- **Agent-based AI orchestration**: Modular agents for context detection, query generation, and improvement
- **Prompt engineering**: Centralized management of prompt templates for LLMs
- **Vector embeddings**: Semantic representation of data for advanced retrieval and matching
- **RESTful API**: Exposes endpoints for query submission and response retrieval
- **Extensible architecture**: Easily add new agents, tools, or embedding strategies
- **Dockerized deployment**: Simplifies setup and scaling
- **Authorization**: Ensures secure access to API endpoints

### Target Users and Use Cases
- **Developers** building AI-powered applications
- **AI engineers** extending agent logic or prompt templates
- **Organizations** seeking to automate knowledge retrieval or conversational interfaces

### Business Impact and Outcomes
- Accelerates development of intelligent backend services
- Improves response quality and relevance through AI agent orchestration
- Reduces operational complexity with modular, containerized deployment

## 2. System Overview (All Levels)

### High-Level Architecture Explanation
The system is organized into modular domains: AI agents (generation, misc, prompts, tools), backend API routing, embedding management, database access, configuration, and utility modules. Each domain is implemented as a Python package, with clear separation of concerns. The API layer exposes endpoints for query processing, while the AI agent layer handles the core logic for context detection, query generation, and improvement. Embedding modules manage vector representations, and utility modules provide shared functionality.

### System Capabilities and Functionality
- Accepts user queries via RESTful API
- Validates and authorizes incoming requests
- Processes queries using a pipeline of AI agents
- Generates or improves queries using prompt engineering and LLMs
- Computes vector embeddings for semantic processing
- Returns context-aware, AI-generated responses

### How the System Works (Conceptual Flow)
1. User submits a query to the API
2. API validates and authorizes the request
3. Query is routed to the appropriate AI agent(s)
4. Agents perform context detection, query generation, or improvement
5. Embeddings are generated as needed
6. Results are aggregated and returned to the user

### Key Components and Their Roles
- **AI Agents**: Core logic for context detection, query generation, and improvement
- **Prompts**: Centralized prompt templates for LLMs
- **Tools**: Abstractions for agent-accessible tools
- **API Routes**: Expose endpoints for query processing
- **Embedding**: Manages vector representations
- **Database**: Handles metadata and persistent storage
- **Config/Utils**: Configuration and shared utilities

## 3. Detailed Architecture (Architect & Developer Level)

### Complete Architecture Pattern Explanation
The architecture follows a modular, service-oriented pattern, with each domain encapsulated as a Python package. The system is designed for extensibility, allowing new agents, tools, or embedding strategies to be added with minimal impact on existing code. Docker Compose is used for deployment, ensuring consistent environments across development and production. The API layer serves as the entry point, delegating requests to the appropriate agent workflows.

### Technology Stack Breakdown
- **Languages**: Python
- **Frameworks**: Python/pip
- **AI/ML Frameworks**: AI Agent Framework, Prompt Engineering, Vector Embeddings
- **Infrastructure**: Docker, Docker Compose

### System-Level Design Decisions
- **Separation of concerns**: Each domain (agents, prompts, tools, embedding, API, etc.) is isolated in its own package
- **Extensibility**: Modular design allows for easy addition of new agents or tools
- **Containerization**: Docker ensures reproducible environments
- **Security**: Authorization enforced at API layer

### Scalability and Performance Considerations
- **Dockerized deployment** allows for horizontal scaling of API and agent services
- **Agent modularity** enables parallel processing of queries
- **Embedding computation** can be offloaded or distributed as needed

### Integration Points
- **API endpoints**: /api/query for query submission
- **LLM integration**: Abstracted via AI agent modules
- **Embedding storage**: Managed by embedding modules

## 4. Component Details (Developer & Architect Level)

### AI Agents - Generation
- **Purpose**: Implements context detection, query generation, and improvement logic
- **Implementation**: Python modules with agent classes/functions (e.g., `context_detection_agent.py`, `query_generation_agent.py`, `improvement_agent.py`)
- **Key Files**:
  - `src/ai/agents/generation/context_detection_agent.py`: Detects context from input
  - `src/ai/agents/generation/query_generation_agent.py`: Generates queries
  - `src/ai/agents/generation/improvement_agent.py`: Improves queries
- **Data Structures**: Agent classes, prompt templates
- **Error Handling**: Try/except blocks around LLM calls; fallback logic
- **Dependencies**: Prompts, tools, LLM modules

### AI Agents - Misc
- **Purpose**: Auxiliary agent utilities (e.g., relevance checking)
- **Implementation**: `check_relevance.py` provides relevance scoring
- **Key Files**: `src/ai/agents/misc/check_relevance.py`
- **Data Structures**: Scoring functions
- **Error Handling**: Exception handling in scoring logic

### AI Agents - Prompts
- **Purpose**: Centralizes prompt templates and construction logic
- **Implementation**: `prompts.py` defines templates and prompt builders
- **Key Files**: `src/ai/agents/prompts/prompts.py`
- **Data Structures**: Template classes, dictionaries
- **Error Handling**: Handles missing/malformed templates

### AI Agents - Tools
- **Purpose**: Implements agent-accessible tools
- **Implementation**: `tools.py` defines tool interfaces
- **Key Files**: `src/ai/agents/tools/tools.py`
- **Data Structures**: Tool classes/functions
- **Error Handling**: Tool invocation errors

### AI Config
- **Purpose**: Manages configuration for AI modules
- **Implementation**: `config.py` loads and validates config
- **Key Files**: `src/ai/config/config.py`
- **Data Structures**: Config classes/dicts
- **Error Handling**: Handles missing/invalid config

### AI Graphs
- **Purpose**: Orchestrates agent workflows as graphs
- **Implementation**: `graph_builder.py`, `graph_executor.py` define nodes, edges, execution logic
- **Key Files**: `src/ai/graphs/graph_builder.py`, `src/ai/graphs/graph_executor.py`
- **Data Structures**: Node/edge classes
- **Error Handling**: Invalid graph/execution errors

### AI LLM
- **Purpose**: Abstraction for LLM interactions and guardrails
- **Implementation**: `llm.py` (LLM client), `guard.py` (output validation)
- **Key Files**: `src/ai/llm/llm.py`, `src/ai/llm/guard.py`
- **Data Structures**: LLM client classes
- **Error Handling**: LLM API errors, output validation

### AI State
- **Purpose**: Manages agent generation state
- **Implementation**: `generation_state.py` defines state classes/variables
- **Key Files**: `src/ai/state/generation_state.py`
- **Data Structures**: State classes
- **Error Handling**: State update errors

### AI Utils
- **Purpose**: Shared utilities (helpers, role management)
- **Implementation**: `helpers.py`, `role.py`
- **Key Files**: `src/ai/utils/helpers.py`, `src/ai/utils/role.py`
- **Data Structures**: Utility functions/classes
- **Error Handling**: Utility errors

### Backend API Routes
- **Purpose**: Exposes API endpoints for query processing
- **Implementation**: `query.py` defines /api/query endpoint
- **Key Files**: `src/backend/routes/query.py`, `src/backend/routes/__init__.py`
- **Data Structures**: Request/response schemas
- **Error Handling**: API errors, input validation, authorization
- **Dependencies**: Schema, validators, db, embedding modules

### Backend Config
- **Purpose**: Backend configuration management
- **Implementation**: `config.py` loads/validates config
- **Key Files**: `src/backend/config/config.py`
- **Data Structures**: Config classes
- **Error Handling**: Config errors

### Backend DB
- **Purpose**: Database access and metadata management
- **Implementation**: `mongodb.py` (DB client), `metadata.py` (metadata logic)
- **Key Files**: `src/backend/db/mongodb.py`, `src/backend/db/metadata.py`
- **Data Structures**: DB client, metadata structures
- **Error Handling**: DB connection/query errors

### Backend Embedding
- **Purpose**: Vector embedding generation/storage
- **Implementation**: `embedding.py` handles embeddings
- **Key Files**: `src/backend/embedding/embedding.py`
- **Data Structures**: Embedding vectors/models
- **Error Handling**: Embedding errors

### Backend Schema
- **Purpose**: API request/response schemas
- **Implementation**: `query.py` defines schemas
- **Key Files**: `src/backend/schema/query.py`
- **Data Structures**: Schema classes
- **Error Handling**: Schema validation errors

### Backend Utils
- **Purpose**: Backend utilities (metadata extraction, misc)
- **Implementation**: `metadata_extractor.py`, `misc.py`
- **Key Files**: `src/backend/utils/metadata_extractor.py`, `src/backend/utils/misc.py`
- **Data Structures**: Utility functions
- **Error Handling**: Utility errors

### Backend Validators
- **Purpose**: API request validation
- **Implementation**: `validators.py` implements validation logic
- **Key Files**: `src/backend/validators/validators.py`
- **Data Structures**: Validator classes/functions
- **Error Handling**: Validation errors

## 5. How It Works (All Levels)

### 5.1 User Request Flow (Step-by-Step)
1. **User submits a query** to the `/api/query` endpoint (see `src/backend/routes/query.py`).
2. **API route receives the request**, validates input using schema and validator modules (`src/backend/schema/query.py`, `src/backend/validators/validators.py`).
3. **Authorization is checked** (authorization logic present in API layer).
4. **Request is routed to the appropriate AI agent** (e.g., context detection, query generation, improvement) based on input and agent logic (`src/ai/agents/generation/`).
5. **Agent processes the request**:
   - If context detection is needed, `context_detection_agent.py` is invoked.
   - If query generation is needed, `query_generation_agent.py` is used.
   - If improvement is needed, `improvement_agent.py` is called.
6. **Agent may invoke LLM or embedding modules** as required (`src/ai/llm/llm.py`, `src/backend/embedding/embedding.py`).
7. **Results are aggregated and formatted** for response.
8. **Response is returned to the user** as JSON.

### 5.2 LangGraph/Agent State Machine Flow
- **Graph orchestration** is implemented in `src/ai/graphs/graph_builder.py` and `src/ai/graphs/graph_executor.py`.
- **Nodes**: Each agent or processing step is a node (e.g., context detection, query generation).
- **Edges**: Define transitions based on agent outputs (e.g., if context detected, proceed to query generation).
- **State updates**: State variables are updated at each node (e.g., context, query, embeddings).
- **Tool calls**: Tools are invoked as needed by agents (`src/ai/agents/tools/tools.py`).
- **Flow example**: User query → Context Detection Node → (if context found) → Query Generation Node → (if improvement needed) → Improvement Node → Response.

### 5.3 Decision Logic
- **Cache lookup**: Not explicitly found in code structure.
- **LLM invocation**: Agents decide when to call LLM based on input and context (`src/ai/agents/generation/`).
- **Database query generation**: Not explicitly found; embedding and metadata modules may interact with DB.
- **Agent selection**: Based on input and agent logic.
- **Error handling**: Try/except blocks in agent and API modules; errors are logged and appropriate responses returned.

### 5.4 Request/Response Patterns
- **Request format**: JSON payloads as defined in `src/backend/schema/query.py`.
- **Response format**: JSON responses with generated content or error messages.
- **Data transformations**: Input is validated, transformed into agent input, processed, and output is formatted for response.

### 5.5 Data Flow Through System
- **User query** → **API route** → **Agent(s)** → **LLM/Embedding** → **Response**
- Data is transformed from JSON to Python objects, processed, and returned as JSON.

### 5.6 Business Workflows
- **Query processing**: End-to-end flow from user input to AI-generated response.

### 5.7 Error Handling Flows
- **API errors**: Handled in route modules; invalid input or unauthorized requests return error responses.
- **Agent errors**: Try/except blocks; fallback logic or error messages returned.
- **LLM/Embedding errors**: Handled in respective modules; errors logged and surfaced to API layer.

### 5.8 State Management
- **Agent state**: Managed in `src/ai/state/generation_state.py`.
- **Session management**: Not explicitly found in code structure.

## 6. API Documentation (Developer Level)

### Endpoints
- **POST /api/query** (`src/backend/routes/query.py`)
  - **Parameters**: JSON body as defined in `src/backend/schema/query.py`
  - **Request Example**:
    ```json
    {
      "query": "What is the capital of France?",
      "context": "geography"
    }
    ```
  - **Response Example**:
    ```json
    {
      "result": "The capital of France is Paris.",
      "confidence": 0.98
    }
    ```
  - **Authentication**: Authorization required (checked in API route)
  - **Error Responses**: Returns error JSON on invalid input or unauthorized access
  - **API Versioning**: No explicit versioning found

### Request/Response Formats
- **Request**: JSON, validated against schema in `src/backend/schema/query.py`
- **Response**: JSON, includes result and metadata

### Authentication Mechanisms
- **Authorization**: Enforced at API layer (see `src/backend/routes/query.py`)

### Rate Limiting
- Not explicitly found in code structure

### Error Responses
- **Invalid input**: Returns error JSON with message
- **Unauthorized**: Returns error JSON with message

### Code Example (API Route)
```python
# src/backend/routes/query.py
from fastapi import APIRouter, Request
from src.backend.schema.query import QuerySchema
from src.backend.validators.validators import validate_query

@router.post("/api/query")
def handle_query(request: Request):
    data = request.json()
    validate_query(data)
    # ... agent processing ...
    return {"result": result, "confidence": confidence}
```

## 7. Data Architecture (Architect & Developer Level)

### Database Schemas and Relationships
- **Database**: MongoDB (see `src/backend/db/mongodb.py`)
- **Metadata**: Managed in `src/backend/db/metadata.py`
- **Schema**: Defined in `src/backend/schema/query.py`
- **Relationships**: Not explicitly defined; likely flat document structure

### Data Access Patterns
- **Direct access**: Via MongoDB client in `src/backend/db/mongodb.py`
- **Metadata management**: Via `metadata.py`

### Caching Strategies
- Not explicitly found in code structure

### Data Validation Rules
- **Validation**: Implemented in `src/backend/validators/validators.py` and schema modules

### Migration Strategies
- Not explicitly found in code structure

### Backup and Recovery
- **Backup**: `backupfile.yml` suggests backup configuration
- **Recovery**: Not explicitly found

### Data Flow Patterns
- **ETL/Streaming/Batch**: Not explicitly found; data flows are synchronous via API

## 8. Design Patterns (Architect Level)

### Patterns Used and Why
- **Modularization**: Each domain is a separate Python package (e.g., agents, prompts, tools)
- **Service-Oriented**: API routes delegate to service modules (agents, embedding)
- **Graph-Based Orchestration**: Agent workflows are modeled as graphs (`src/ai/graphs/`)

### Architectural Decisions and Trade-Offs
- **Extensibility**: Modular design allows for easy addition of new agents/tools
- **Separation of Concerns**: Clear boundaries between API, agent logic, embedding, and utilities

### Pattern Interactions
- **API routes** interact with agent modules, which in turn use tools and prompts
- **Graph orchestration** coordinates agent execution

### Code Examples
```python
# src/ai/agents/generation/query_generation_agent.py
class QueryGenerationAgent:
    def generate(self, context):
        # Use prompt from prompts module
        prompt = get_prompt(context)
        # Call LLM
        response = llm.generate(prompt)
        return response
```

### Pattern Locations
- **Modularization**: All `src/ai/` and `src/backend/` subdirectories
- **Graph orchestration**: `src/ai/graphs/`

## 9. Security (All Levels)

### Security Mechanisms
- **Authorization**: Enforced at API layer (`src/backend/routes/query.py`)
- **Input validation**: All requests validated against schemas and validators
- **Data protection**: Not explicitly found; no encryption logic observed

## 10. Development Guide (Developer Level)

### Setup and Installation
1. **Clone the repository**
2. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```
3. **Configure environment variables**:
   - Copy `.env.example` to `.env` and set values
4. **Run with Docker Compose**:
   ```bash
   docker-compose up --build
   ```

### Configuration
- **AI config**: `src/ai/config/config.py`
- **Backend config**: `src/backend/config/config.py`
- **Environment variables**: `.env.example`

### Running Tests
- **Test files**: `tests/tests.py`
- **Run tests**:
   ```bash
   python -m unittest discover tests
   ```

### Debugging Tips
- **Logs**: Check `logger.py` for logging configuration
- **API errors**: Inspect API responses for error messages
- **Agent errors**: Review logs for agent exceptions

### Common Issues
- **Missing dependencies**: Ensure all requirements are installed
- **Configuration errors**: Verify `.env` and config files
- **Docker issues**: Rebuild containers if dependencies change

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
│   └── backend/
│       ├── config/
│       │   ├── config.py
│       │   └── __init__.py
│       ├── db/
│       │   ├── metadata.py
│       │   ├── mongodb.py
│       │   └── __init__.py
│       ├── embedding/
│       │   ├── embedding.py
│       │   └── __init__.py
│       ├── models/
│       │   └── __init__.py
│       ├── routes/
│       │   ├── query.py
│       │   └── __init__.py
│       ├── schema/
│       │   ├── query.py
│       │   └── __init__.py
│       ├── utils/
│       │   ├── metadata_extractor.py
│       │   ├── misc.py
│       │   └── __init__.py
│       ├── validators/
│       │   ├── validators.py
│       │   └── __init__.py
│       └── __init__.py
├── tests/
│   ├── tests.py
│   └── __init_.py
└── src/__init__.py
```

### Key Files by Component
- **AI Agents**: `src/ai/agents/generation/`, `src/ai/agents/misc/`, `src/ai/agents/prompts/`, `src/ai/agents/tools/`
- **API Routes**: `src/backend/routes/query.py`
- **Embedding**: `src/backend/embedding/embedding.py`
- **Database**: `src/backend/db/mongodb.py`, `src/backend/db/metadata.py`
- **Config**: `src/ai/config/config.py`, `src/backend/config/config.py`
- **Schema/Validation**: `src/backend/schema/query.py`, `src/backend/validators/validators.py`
- **Utils**: `src/ai/utils/`, `src/backend/utils/`
- **Tests**: `tests/tests.py`
- **Documentation**: `README.md`, `Code-Analysis-Review.md`

### Entry Points
- **API**: `src/backend/routes/query.py`
- **Main runner**: `run.py`

### Configuration Files
- `.env.example`, `docker-compose.yml`, `Dockerfile`, `src/ai/config/config.py`, `src/backend/config/config.py`

### Test Files
- `tests/tests.py`

### Documentation Files
- `README.md`, `Code-Analysis-Review.md`

---

# 12. Troubleshooting and Best Practices

- **Always validate configuration files before deployment**
- **Use Docker Compose for consistent environments**
- **Check logs for detailed error messages**
- **Extend agent logic by adding new modules in `src/ai/agents/`**
- **Update prompt templates in `src/ai/agents/prompts/prompts.py` for improved LLM performance**
- **Ensure all API requests are authorized**

# 13. Architecture Diagram Descriptions

## architectureDiagram
"""
A comprehensive architecture diagram showing all major components:
- User/API Consumer
- API Layer (`src/backend/routes/`)
- AI Agent Layer (`src/ai/agents/`)
- Prompt Engineering (`src/ai/agents/prompts/`)
- Tools (`src/ai/agents/tools/`)
- Embedding Module (`src/backend/embedding/`)
- Database Layer (`src/backend/db/`)
- Configuration Modules (`src/ai/config/`, `src/backend/config/`)
- Utilities (`src/ai/utils/`, `src/backend/utils/`)
- State Management (`src/ai/state/`)
- Graph Orchestration (`src/ai/graphs/`)
Connections:
- User → API Layer
- API Layer → AI Agent Layer
- AI Agent Layer ↔ Prompt Engineering, Tools, Embedding, State Management
- Embedding ↔ Database Layer
- All modules ↔ Configuration, Utilities
"""

## functionalFlowDiagram
"""
A functional flow diagram showing:
- User submits query → API Layer
- API Layer validates and authorizes → passes to Agent Orchestration
- Agent Orchestration (Graph) decides which agent(s) to invoke
- Agents may call Prompts, Tools, Embedding
- Embedding may access Database
- Agents aggregate results → API Layer → User
Decision points:
- Authorization check
- Agent selection based on input
- LLM invocation based on context
"""

## sequenceDiagram
"""
A sequence diagram:
User → API Layer: POST /api/query
API Layer → Validator: Validate input
Validator → API Layer: Valid/Invalid
API Layer → Agent Orchestration: Process query
Agent Orchestration → Context Detection Agent: Detect context
Context Detection Agent → Query Generation Agent: Generate query
Query Generation Agent → LLM: Generate response
LLM → Query Generation Agent: Response
Query Generation Agent → Agent Orchestration: Result
Agent Orchestration → API Layer: Aggregated result
API Layer → User: JSON response
"""

## codeFlowDiagram
"""
A code execution flow:
- Entry: API route handler (`src/backend/routes/query.py`)
- Calls validator (`src/backend/validators/validators.py`)
- On success, calls agent orchestration (`src/ai/graphs/graph_executor.py`)
- Agent orchestration invokes agents (`src/ai/agents/generation/`)
- Agents use prompts (`src/ai/agents/prompts/prompts.py`), tools, and LLM (`src/ai/llm/llm.py`)
- Embedding module called if needed (`src/backend/embedding/embedding.py`)
- Results aggregated and returned
"""

## apiFlowDiagram
"""
API flow:
User → /api/query (POST)
API → Validator → Agent Orchestration → Agents → LLM/Embedding/Tools → API → User
"""

## dataFlowDiagram
"""
Data flow:
- User Query (JSON) → API Route (`src/backend/routes/query.py`)
- API Route → Validator (`src/backend/validators/validators.py`)
- Validator → Agent Orchestration (`src/ai/graphs/graph_executor.py`)
- Agent Orchestration → Agents (`src/ai/agents/generation/`)
- Agents → Prompts (`src/ai/agents/prompts/prompts.py`), Tools, LLM (`src/ai/llm/llm.py`)
- Agents → Embedding (`src/backend/embedding/embedding.py`)
- Embedding → Database (`src/backend/db/mongodb.py`)
- Agents aggregate results → API Route → User (JSON)
Arrows labeled with data types (JSON, Python objects, vectors)
"""

## componentClassDiagram
"""
Component/class relationships:
- API Route Handler class
- Validator class
- Agent classes (ContextDetectionAgent, QueryGenerationAgent, ImprovementAgent)
- PromptTemplate class
- Tool class
- LLM client class
- Embedding class
- State class
- GraphBuilder/GraphExecutor classes
Show inheritance and composition
"""

## deploymentDiagram
"""
Deployment diagram:
- User
- Dockerized API Service
- Dockerized AI Agent Service
- Dockerized Embedding Service
- MongoDB Service
- All services connected via Docker Compose network
"""
