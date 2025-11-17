# Architecture Overview

## Executive Summary
This document presents an architecture overview for an interactive chemistry simulation tool hosted on Azure. The design aligns with the Azure Well-Architected Framework's pillars, including reliability, security, cost optimization, operational excellence, and performance efficiency. The simulation models various tank-filling and reaction processes, accounting for normal, trending-unsafe, and dangerous scenarios. The recommendations are based solely on the provided project requirements, as no vector memory snippets were returned.

## Architectural Principles
The following principles guide the architecture of the chemistry simulation tool:

### Reliability
- **Design for failure and controlled scenario simulation**  
  The tool must gracefully simulate sensor faults, such as FIT-101 failures and temperature out-of-spec trends.

- **Design for extensibility of new chemistry scenarios**  
  Ensure the simulation logic is flexible enough to incorporate future unsafe conditions or expanded chemical processes.

### Security
- **Least privilege and secure data boundaries**  
  Access control through Azure Active Directory (Azure AD) and Key Vault secrets is essential for simulation logic, student responses, and telemetry.

- **Data protection at rest and in transit**  
  All simulation state data and student responses stored in Azure SQL, Blob, and Cosmos DB must be encrypted with managed keys.

### Cost Optimization
- **Cost-efficient consumption-based compute**  
  Utilizing Azure Functions, App Service, and PaaS storage minimizes operational costs for the training simulation platform.

- **Use managed services to reduce operational burden**  
  Azure PaaS services eliminate the need to manage compute clusters, simplifying operations for this educational application.

### Operational Excellence
- **Operational observability**  
  Monitoring student actions, scenario branching, and simulation metrics is crucial. This will be achieved using Azure Monitor, Application Insights, and Log Analytics.

- **Infrastructure as code**  
  Implementing Bicep or Terraform enables controlled deployment and safe iteration of infrastructure.

### Performance Efficiency
- **Performance-optimized event flow**  
  Ensuring low-latency APIs and scalable event handling is vital for updating tank temperature, level rise, and flow dynamics.

- **Scalable separation of simulation engine and UI**  
  The front-end interface must not impact the throughput of simulation updates.

## Reference Architecture
### Key Components
The architecture comprises the following components:

| Component                         | Purpose                                                   |
|-----------------------------------|-----------------------------------------------------------|
| Azure Front Door                  | Global entry, TLS termination, routing to web front end   |
| Azure CDN                         | Serves static front-end assets with low latency           |
| VNet with Subnets (Web, App, Data)| Network segmentation and isolation                         |
| NSGs                              | Traffic filtering between tiers                            |
| Azure App Service (Front-End UI)  | Provides UI for student interaction with the tank simulation|
| Azure App Service (Simulation API) | Executes tank process logic, including flow rates and sensor failures |
| Azure Functions                   | Manages background tasks like simulation ticks and scoring workflows |
| API Management                    | Secures, governs, and versions simulation APIs            |
| Azure Service Bus                 | Decouples simulation events and updates                   |
| Azure Event Grid                  | Publishes scenario events to monitoring services          |
| Azure SQL Database                | Stores student submissions and scenario runs              |
| Azure Cosmos DB                   | Stores real-time simulation state documents               |
| Azure Blob Storage                | Stores learning materials, logs, and exports              |
| Azure Key Vault                   | Manages secrets, API keys, and database credentials       |
| Azure Active Directory            | Handles authentication for users                           |
| Azure Application Insights         | Telemetry for simulation engine performance                |
| Azure Monitor + Log Analytics Workspace | Centralized logs and metrics collection               |
| Azure Backup                      | Provides backups for SQL and Blob storage                 |
| Azure Firewall                    | Ensures perimeter security for the simulation environment  |
| Private Endpoints                 | Secure access from app services to data components        |
| Azure DevOps Pipelines            | Automates CI/CD deployment                                 |
| Bicep/Terraform IaC              | Declares and deploys infrastructure consistently          |

### Data Flow
The data flow among components is as follows:

| From                      | To                             | Purpose                                       |
|--------------------------|--------------------------------|-----------------------------------------------|
| Student Browser          | Azure Front Door               | HTTPS access to UI                            |
| Front Door               | Web App Service                | Route UI requests                             |
| Web App Service          | Simulation Engine API          | Send user actions (e.g., add substrate)     |
| Simulation Engine API    | Cosmos DB                      | Update simulation state documents             |
| Simulation Engine API    | Service Bus                    | Emit simulation events                        |
| Service Bus              | Azure Functions                | Trigger simulation processing                  |
| Azure Functions          | Cosmos DB                      | Update predicted state or corrective actions  |
| Azure Functions          | SQL Database                   | Record student responses and scenario scoring  |
| Event Grid               | Monitor/App Insights           | Telemetry for scenario transitions             |
| Web App Service          | SQL Database                   | Query student history and scoring              |
| All Compute              | Key Vault                      | Retrieve secrets securely                     |
| All Components           | Log Analytics                  | Centralized log and metrics collection        |
| Backup Jobs              | Azure Backup                   | Protect SQL and Blob data                     |

## Feature-Component Mapping
The following table outlines the features of the simulation and their corresponding components:

| Feature                                          | Components                                           |
|--------------------------------------------------|-----------------------------------------------------|
| Simulate tank fill to 1100 L, enzyme feed 50 L  | Simulation Engine API, Cosmos DB, Functions         |
| Temperature range enforcement (35–40 ºC)        | Simulation Engine API, Functions, Cosmos DB        |
| Scenario #2 temperature trending out of spec     | Simulation Engine API, Cosmos DB, Service Bus, Functions |
| Scenario #3 flow sensor FIT-101 failure          | Simulation Engine API, Cosmos DB, Service Bus, Functions |
| Student response capture                          | Web App, SQL Database                               |
| Instructor review                                 | Web App, SQL Database                               |
| Secure access                                    | Azure AD, Key Vault, API Management                 |

## Constraints and Impacts
### Constraints
- **None specified**  
  There are no external limitations; the architecture adheres to full best practices.

### Quality Assurance Coverage
The system's quality assurance is mapped to the following criteria:

| Quality                | Mapping                                                                                           |
|-----------------------|---------------------------------------------------------------------------------------------------|
| Reliability           | High availability via App Service, Service Bus, multi-zone SQL; failure injection supported by scenario logic |
| Security              | Azure AD authentication, private endpoints, Key Vault, encrypted storage                        |
| Performance           | Low-latency state access using Cosmos DB, scalable App Services                                   |
| Operational Excellence | Monitoring via Azure Monitor, Application Insights, Infrastructure as Code, CI/CD processes      |
| Cost Optimization     | Serverless functions and PaaS data storage options                                               |

## Framework Alignment
The architecture is aligned with the Azure Well-Architected Framework, encompassing the following pillars:

- Reliability
- Security
- Cost Optimization
- Operational Excellence
- Performance Efficiency

## Architectural Decision Records (ADRs)
### Key Decisions
- **ADR-001:** Use Cosmos DB for real-time simulation state  
  **Reason:** Provides low latency and a flexible schema for tank conditions.  
  **Framework Pillar:** Performance Efficiency / Reliability

- **ADR-002:** Implement API Management for all simulation endpoints  
  **Reason:** Enhances security, throttling, and governance.  
  **Framework Pillar:** Security / Operational Excellence

- **ADR-003:** Separate simulation engine into App Service API and Functions  
  **Reason:** Facilitates the distinction between synchronous API calls and asynchronous scenario ticks.  
  **Framework Pillar:** Performance Efficiency / Cost Optimization

## Fitness Functions
The following fitness functions are established to ensure quality attributes:

| Attribute              | Function                                                                                  |
|-----------------------|------------------------------------------------------------------------------------------|
| Reliability           | Automatically verify completion of simulation ticks under load; fail if >1% delays occur. |
| Performance           | Ensure response time for the simulation UI is <150ms at the 95th percentile.            |
| Security              | Conduct periodic penetration tests; trigger alerts if any API is exposed without authentication. |
| Cost Optimization     | Detect monthly cost anomalies exceeding 20% over the baseline.                           |
| Operational Excellence | Ensure deployments complete without downtime and maintain a rollback rate <5%.          |

## Assumptions
The following assumptions are made regarding the architecture:

- No vector memory context is available.
- Users access the simulation tool via the public internet with Azure AD authentication.
- The complexity of the simulation remains within the capabilities of PaaS services.

## Open Questions
The following questions remain unresolved:

- Should simulation physics be more detailed, incorporating thermodynamics and pressure curves?
- Do instructors require exportable reports or integration with Learning Management Systems (LMS)?
- Will future scenarios expand to include multi-reactor or multi-tank systems?