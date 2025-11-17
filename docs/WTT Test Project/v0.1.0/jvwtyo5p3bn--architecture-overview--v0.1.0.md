# Architecture Overview

## Executive Summary
This document presents an architecture overview of an interactive Azure-hosted chemistry simulation tool, designed in alignment with the Azure Well-Architected Framework pillars. The architecture effectively models a tank-filling and reaction process, accommodating normal, trending-unsafe, and dangerous scenarios. It is essential to note that all recommendations are based solely on the provided project requirements, as no vector memory snippets were returned.

## Architectural Principles
The following principles guide the architecture design, categorized by their alignment with the Azure Well-Architected Framework pillars:

### Reliability
- **Design for Failure and Controlled Scenario Simulation**
  - Justification: The chemistry training tool must gracefully simulate sensor faults, such as the FIT-101 failure and temperature out-of-spec trends.

- **Design for Extensibility of New Chemistry Scenarios**
  - Justification: Ensure flexible simulation logic to incorporate future unsafe conditions or expanded chemical processes.

### Security
- **Least Privilege and Secure Data Boundaries**
  - Justification: Simulation logic, student answers, and telemetry must be access-controlled through Azure Active Directory (AD) and Key Vault secrets.

- **Data Protection at Rest and in Transit**
  - Justification: Simulation state and student responses stored in Azure SQL, Blob, or Cosmos DB must be encrypted with managed keys.

- **Secure Access**
  - Justification: Ensure secure access for students, admins, and instructors through Azure AD, Key Vault, and API Management.

### Cost Optimization
- **Cost-Efficient Consumption-Based Compute**
  - Justification: Utilizing Azure Functions, App Service, and PaaS storage minimizes operational costs for the training simulation platform.

- **Use Managed Services to Reduce Operational Burden**
  - Justification: Azure PaaS services eliminate the need to manage compute clusters for a small educational application.

### Operational Excellence
- **Operational Observability**
  - Justification: Monitoring student actions, scenario branching, and simulation metrics using Azure Monitor, Application Insights, and Log Analytics is essential.

- **Infrastructure as Code**
  - Justification: Using Bicep or Terraform enables controlled deployment and safe iteration.

### Performance Efficiency
- **Performance-Optimized Event Flow**
  - Justification: State updates for tank temperature, level rise, pressure, and flow dynamics require low-latency APIs and scalable event handling.

- **Scalable Separation of Simulation Engine and UI**
  - Justification: Front-end loads must not affect simulation update throughput.

## Reference Architecture
### Components
The following components constitute the architecture:

| Component                              | Purpose                                                         |
|----------------------------------------|-----------------------------------------------------------------|
| Azure Front Door                       | Global entry, TLS termination, routing to the web front end     |
| Azure CDN                              | Serves static front-end assets with low latency                 |
| VNet with Subnets (Web, App, Data)    | Network segmentation and isolation                               |
| NSGs                                   | Traffic filtering between tiers                                  |
| Azure App Service (Front-End Web UI)  | Provides UI for student interaction with the tank simulation     |
| Azure App Service (Simulation Engine API) | Executes tank process logic including flow rates, temperature changes, and sensor failures |
| Azure Functions                        | Manages background tasks such as simulation ticks and scoring workflows |
| API Management                         | Secures, governs, and versions simulation APIs                  |
| Azure Service Bus                      | Decouples simulation events, triggers, and updates              |
| Azure Event Grid                       | Publishes scenario events to observers such as monitoring services |
| Azure SQL Database                     | Stores student submissions, scenario runs, and answer history   |
| Azure Cosmos DB                        | Stores real-time simulation state documents                      |
| Azure Blob Storage                     | Stores learning materials, logs, and exports                    |
| Azure Key Vault                        | Stores secrets, API keys, and database credentials               |
| Azure Active Directory                 | Provides authentication for all users                            |
| Azure Application Insights             | Collects telemetry for simulation engine performance             |
| Azure Monitor + Log Analytics Workspace | Centralized logs, metrics, and scenario trend analytics         |
| Azure Backup                           | Backups for SQL and Blob storage                                 |
| Azure Firewall                         | Provides perimeter security for the simulation environment       |
| Private Endpoints                      | Secure access from app services to data components              |
| Azure DevOps Pipelines                 | Automates CI/CD deployment processes                             |
| Bicep/Terraform IaC                   | Declares and deploys infrastructure consistently                |

### Data Flow
The following table outlines the data flow within the architecture:

| From                                   | To                                    | Purpose                                      |
|----------------------------------------|---------------------------------------|----------------------------------------------|
| Student Browser                        | Azure Front Door                      | HTTPS access to UI                           |
| Front Door                             | Web App Service                       | Routes UI requests                           |
| Web App Service                        | Simulation Engine API                 | Sends user actions (e.g., add substrate)    |
| Simulation Engine API                  | Cosmos DB                            | Updates simulation state documents           |
| Simulation Engine API                  | Service Bus                           | Emits simulation events                       |
| Service Bus                            | Azure Functions                       | Triggers simulation tick processing           |
| Azure Functions                        | Cosmos DB                            | Updates predicted state                       |
| Azure Functions                        | SQL Database                          | Records student responses and scenario scoring|
| Event Grid                             | Monitor/App Insights                  | Telemetry for scenario transitions            |
| Web App Service                        | SQL Database                          | Queries student history and scoring           |
| All Compute                            | Key Vault                             | Retrieves secrets securely                    |
| All Components                         | Log Analytics                         | Central log and metrics collection            |
| Backup Jobs                            | Azure Backup                          | Protects SQL and Blob data                   |

## Mappings
### Feature to Component
The following table maps features to their respective components:

| Feature                                                       | Components                                       |
|---------------------------------------------------------------|-------------------------------------------------|
| Simulate tank fill to 1100 L, enzyme feed 50 L              | Simulation Engine API, Cosmos DB, Functions     |
| Temperature range enforcement (35–40 ºC) and denaturation at 50 ºC | Simulation Engine API, Functions, Cosmos DB     |
| Scenario #2 temperature trending out of spec                  | Simulation Engine API, Cosmos DB, Service Bus, Functions |
| Scenario #3 flow sensor FIT-101 failure and pump runaway     | Simulation Engine API, Cosmos DB, Service Bus, Functions |
| Student response capture                                       | Web App, SQL Database                           |
| Instructor review                                             | Web App, SQL Database                           |
| Secure access                                                | Azure AD, Key Vault, API Management             |

### Constraint Impact
| Constraint          | Impact                                       |
|---------------------|----------------------------------------------|
| None specified      | No external limitations; architecture applies full best practices. |

### Quality Assurance Coverage
| Quality                | Mapping                                                                          |
|-----------------------|---------------------------------------------------------------------------------|
| Reliability           | High availability via App Service, Service Bus, multi-zone SQL; failure injection supported by scenario logic |
| Security              | AAD authentication, private endpoints, Key Vault, encrypted storage             |
| Performance           | Cosmos DB low-latency state access, scalable App Services                       |
| Operational Excellence | Monitoring, Application Insights, Infrastructure as Code (IaC), Continuous Integration/Continuous Delivery (CI/CD) |
| Cost Optimization     | Serverless functions, PaaS data storage choices                                 |

## Frameworks
### Azure Well-Architected Framework
The architecture is designed based on the following pillars of the Azure Well-Architected Framework:
- Reliability
- Security
- Cost Optimization
- Operational Excellence
- Performance Efficiency

## Architectural Decision Records (ADR)
| ID       | Decision                                                    | Reason                                                | Framework Pillar                             |
|----------|------------------------------------------------------------|-------------------------------------------------------|----------------------------------------------|
| ADR-001  | Use Cosmos DB for real-time simulation state               | Low latency, flexible schema for tank conditions.     | Performance Efficiency / Reliability         |
| ADR-002  | Use API Management for all simulation endpoints            | Security, throttling, governance.                      | Security / Operational Excellence            |
| ADR-003  | Split simulation engine into App Service API + Functions   | Separation of synchronous API calls and async scenario ticks. | Performance Efficiency / Cost Optimization   |

## Fitness Functions
| Attribute        | Function                                                                           |
|------------------|-----------------------------------------------------------------------------------|
| Reliability      | Automatically verify simulation tick completion under load; fail if >1% tick delays. |
| Performance      | Response time for simulation UI <150ms at the 95th percentile.                   |
| Security         | Periodic penetration tests; alerts if API exposed without authentication.        |
| Cost Optimization | Monthly cost anomaly detection >20% over baseline.                               |
| Operational Excellence | Deployments must complete with zero downtime and <5% rollback rate.          |

## Assumptions
- No vector memory context was available.
- Users access via public internet with Azure AD authentication.
- Simulation complexity remains within PaaS capability.

## Open Questions
- Should simulation physics be more detailed (thermodynamics, pressure curves)?
- Do instructors need exportable reports or LMS integration?
- Will scenarios expand to multi-reactor or multi-tank systems?