# Enterprise Cloud Architecture: jvwtyo5p3bn (Azure)

## Overview
This document outlines the cloud architecture for the jvwtyo5p3bn project on the Azure platform. It details the various components involved in the system's design, their connections, and the principles guiding the architecture. This architecture is aimed at providing a robust, scalable, and secure environment for tank simulation.

## Architecture Components

### Ingestion & API Layer
- **API Management**: Secures, governs, and versions simulation APIs.

### Processing & Orchestration
- **Network Security Groups (NSGs)**: Filters traffic between different tiers.
- **Azure App Service (Front-End Web UI)**: Provides the user interface for student interaction with tank simulations.
- **Azure App Service (Simulation Engine API)**: Executes tank process logic, including flow rates, temperature changes, and sensor failures.
- **Azure Functions**: Handles background tasks such as simulation ticks, asynchronous event processing, and scoring workflows.
- **Azure Backup**: Manages backups for SQL databases and Blob storage.
- **Private Endpoints**: Ensures secure access from app services to data components.
- **Azure DevOps Pipelines**: Automates continuous integration and continuous deployment (CI/CD).
- **Bicep/Terraform (Infrastructure as Code)**: Facilitates consistent infrastructure declaration and deployment.

### Storage
- **Azure SQL Database**: Stores student submissions, scenario runs, and answer history.
- **Azure Cosmos DB**: Maintains real-time simulation state documents, including tank levels and flow temperature timelines.
- **Azure Blob Storage**: Stores learning materials, logs, and exports.

### Analytics & Machine Learning
- **Azure Monitor + Log Analytics Workspace**: Centralizes logs, metrics, and scenario trend analytics.

### Security & Identity
- **Azure Key Vault**: Safeguards secrets, API keys, and database credentials.
- **Azure Active Directory**: Provides authentication for students, administrators, and instructors.
- **Azure Firewall**: Ensures perimeter security for the simulation environment.

### Networking & Content Delivery
- **Azure Front Door**: Acts as a global entry point, handling TLS termination and routing to the web front end.
- **Azure CDN**: Serves static front-end assets with low latency.
- **Virtual Network (VNet) with Subnets**: Provides network segmentation and isolation across Web, App, and Data tiers.

### Monitoring & Observability
- **Azure Application Insights**: Offers telemetry for simulation engine performance and scenario execution times.

### Integration & Messaging
- **Azure Service Bus**: Decouples simulation events, triggers, and updates (e.g., temperature trends and sensor failure events).
- **Azure Event Grid**: Publishes scenario events to observers, including monitoring and analytics services.

## Connections
- **Student Browser** → **Azure Front Door (HTTPS)**
- **Azure Front Door** → **Web App Service (HTTPS)**
- **Web App Service** → **Simulation Engine API (HTTPS)**
- **Simulation Engine API** → **Cosmos DB (HTTPS)**
- **Simulation Engine API** → **Service Bus (HTTPS)**
- **Service Bus** → **Azure Functions (HTTPS)**
- **Azure Functions** → **Cosmos DB (HTTPS)**
- **Azure Functions** → **SQL Database (HTTPS)**
- **Event Grid** → **Monitor/Application Insights (HTTPS)**
- **Web App Service** → **SQL Database (HTTPS)**
- **All Compute** → **Key Vault (HTTPS)**
- **All Components** → **Log Analytics (HTTPS)**
- **Backup Jobs** → **Azure Backup (HTTPS)**

## Comprehensive Component Details

| Component ID                      | Type    | Description                                                                                       |
|-----------------------------------|---------|---------------------------------------------------------------------------------------------------|
| Azure Front Door                  | Service | Global entry, TLS termination, and routing to the web front end.                                 |
| Azure CDN                         | Service | Serves static front-end assets with low latency.                                                 |
| VNet with Subnets (Web, App, Data)| Service | Network segmentation and isolation.                                                                |
| Network Security Groups (NSGs)    | Service | Filters traffic between different tiers.                                                          |
| Azure App Service (Front-End UI)  | Service | Provides the user interface for student interaction with tank simulations.                       |
| Azure App Service (Simulation API) | Service | Executes tank process logic including flow rates, temperature changes, and sensor failures.     |
| Azure Functions                    | Service | Handles background tasks such as simulation ticks and asynchronous event processing.              |
| API Management                     | Service | Secures, governs, and versions simulation APIs.                                                 |
| Azure Service Bus                  | Service | Decouples simulation events, triggers, and updates.                                             |
| Azure Event Grid                   | Service | Publishes scenario events to observers, including monitoring and analytics services.             |
| Azure SQL Database                 | Service | Stores student submissions, scenario runs, and answer history.                                   |
| Azure Cosmos DB                    | Service | Maintains real-time simulation state documents.                                                  |
| Azure Blob Storage                 | Service | Stores learning materials, logs, and exports.                                                   |
| Azure Key Vault                    | Service | Safeguards secrets, API keys, and database credentials.                                         |
| Azure Active Directory             | Service | Provides authentication for students, administrators, and instructors.                           |
| Azure Application Insights         | Service | Offers telemetry for simulation engine performance.                                              |
| Azure Monitor + Log Analytics      | Service | Centralizes logs, metrics, and scenario trend analytics.                                        |
| Azure Backup                       | Service | Manages backups for SQL databases and Blob storage.                                             |
| Azure Firewall                     | Service | Ensures perimeter security for the simulation environment.                                       |
| Private Endpoints                  | Service | Ensures secure access from app services to data components.                                     |
| Azure DevOps Pipelines             | Service | Automates CI/CD deployment processes.                                                            |
| Bicep/Terraform (IaC)             | Service | Facilitates consistent infrastructure declaration and deployment.                                 |

## Architecture Decision Records

- **ADR 1**: Use Cosmos DB for real-time simulation state.
- **ADR 2**: Utilize API Management for all simulation endpoints.
- **ADR 3**: Split the simulation engine into App Service API and Functions.

## Architecture Principles

1. **Design for failure and controlled scenario simulation**  
   WAF Pillar: Reliability

2. **Least privilege and secure data boundaries**  
   WAF Pillar: Security

3. **Cost-efficient consumption-based compute**  
   WAF Pillar: Cost Optimization

4. **Operational observability**  
   WAF Pillar: Operational Excellence

5. **Performance-optimized event flow**  
   WAF Pillar: Performance Efficiency

6. **Infrastructure as code**  
   WAF Pillar: Operational Excellence

7. **Data protection at rest and in transit**  
   WAF Pillar: Security

8. **Scalable separation of simulation engine and UI**  
   WAF Pillar: Performance Efficiency

9. **Design for extensibility of new chemistry scenarios**  
   WAF Pillar: Reliability

10. **Use managed services to reduce operational burden**  
    WAF Pillar: Cost Optimization

## Feature to Component Mappings
- Simulate tank fill to 1100 L, enzyme feed 50 L
- Temperature range enforcement (35–40 ºC) and denaturation at 50 ºC
- Scenario #2: Temperature trending out of spec
- Scenario #3: Flow sensor FIT‑101 failure and pump runaway
- Student response capture
- Instructor review
- Secure access

## Fitness Functions
- FF 1: 
- FF 2: 
- FF 3: 
- FF 4: 
- FF 5: 

This document serves as a comprehensive overview of the cloud architecture for the jvwtyo5p3bn project. It highlights key components, their roles, and the guiding principles of the architecture, ensuring clarity and readiness for client stakeholders.