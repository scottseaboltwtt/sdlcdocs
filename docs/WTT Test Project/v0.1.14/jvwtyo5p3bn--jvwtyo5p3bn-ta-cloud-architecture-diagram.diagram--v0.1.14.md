# Enterprise Cloud Architecture: jvwtyo5p3bn (AZURE)

## Executive Summary
This document outlines the architecture of an enterprise-level cloud solution deployed on Microsoft Azure. It provides a comprehensive overview of the system components, their interconnections, and architectural principles, ensuring clarity and technical accuracy for stakeholders.

## Architecture Overview

### Ingestion & API Layer
- **API Management:** Secures, governs, and versions simulation APIs.

### Processing & Orchestration
- **Network Security Groups (NSGs):** Filters traffic between different tiers.
- **Azure App Service (Front-End Web UI):** Delivers the user interface for student interactions with the tank simulation.
- **Azure App Service (Simulation Engine API):** Executes the core simulation logic, including flow rates, temperature variations, and sensor failure handling.
- **Azure Functions:** Manages background tasks, such as simulation ticks, asynchronous event processing, and scoring workflows.
- **Azure Backup:** Provides backup solutions for SQL databases and Blob storage.
- **Private Endpoints:** Facilitates secure access from application services to data components.
- **Azure DevOps Pipelines:** Automates continuous integration and continuous deployment (CI/CD).
- **Bicep/Terraform Infrastructure as Code (IaC):** Enables consistent declaration and deployment of infrastructure.

### Storage
- **Azure SQL Database:** Stores student submissions, scenario runs, and answer history.
- **Azure Cosmos DB:** Maintains real-time simulation state documents, including tank levels and temperature timelines.
- **Azure Blob Storage:** Holds learning materials, logs, and exports.

### Analytics & Machine Learning
- **Azure Monitor + Log Analytics Workspace:** Centralizes logs and metrics while providing scenario trend analytics.

### Security & Identity
- **Azure Key Vault:** Safeguards secrets, API keys, and database credentials.
- **Azure Active Directory:** Manages authentication for students, administrators, and instructors.
- **Azure Firewall:** Ensures perimeter security for the simulation environment.

### Networking & Content Delivery Network (CDN)
- **Azure Front Door:** Acts as a global entry point, handling TLS termination and routing to the web front end.
- **Azure CDN:** Delivers static front-end assets with low latency.
- **Virtual Network (VNet) with Subnets (Web, App, Data):** Provides network segmentation and isolation.

### Monitoring & Observability
- **Azure Application Insights:** Offers telemetry for simulation engine performance and scenario execution times.

### Integration & Messaging
- **Azure Service Bus:** Decouples simulation events, triggers, and updates, such as temperature trends and sensor failures.
- **Azure Event Grid:** Publishes scenario events to observers, including monitoring and analytics services.

## Component Connections
- **Student Browser** ➔ **Azure Front Door (HTTPS)**
- **Front Door** ➔ **Web App Service (HTTPS)**
- **Web App Service** ➔ **Simulation Engine API (HTTPS)**
- **Simulation Engine API** ➔ **Cosmos DB (HTTPS)**
- **Simulation Engine API** ➔ **Service Bus (HTTPS)**
- **Service Bus** ➔ **Azure Functions (HTTPS)**
- **Azure Functions** ➔ **Cosmos DB (HTTPS)**
- **Azure Functions** ➔ **SQL Database (HTTPS)**
- **Event Grid** ➔ **Monitor/App Insights (HTTPS)**
- **Web App Service** ➔ **SQL Database (HTTPS)**
- **All Compute** ➔ **Key Vault (HTTPS)**
- **All Components** ➔ **Log Analytics (HTTPS)**
- **Backup Jobs** ➔ **Azure Backup (HTTPS)**

## Comprehensive Component Details
### Component 1: Azure Front Door
- **ID:** Azure Front Door
- **Type:** Service
- **Description:** Global entry point, TLS termination, and routing for the web front end.

### Component 2: Azure CDN
- **ID:** Azure CDN
- **Type:** Service
- **Description:** Serves static front-end assets with low latency.

### Component 3: VNet with Subnets (Web, App, Data)
- **ID:** VNet with Subnets
- **Type:** Service
- **Description:** Provides network segmentation and isolation.

### Component 4: NSGs
- **ID:** NSGs
- **Type:** Service
- **Description:** Filters traffic between tiers.

### Component 5: Azure App Service (Front-End Web UI)
- **ID:** Azure App Service (Front-End Web UI)
- **Type:** Service
- **Description:** User interface for student interactions with the tank simulation.

### Component 6: Azure App Service (Simulation Engine API)
- **ID:** Azure App Service (Simulation Engine API)
- **Type:** Service
- **Description:** Executes core simulation logic, including flow rates and temperature changes.

### Component 7: Azure Functions
- **ID:** Azure Functions
- **Type:** Service
- **Description:** Handles background tasks, including simulation ticks and asynchronous event processing.

### Component 8: API Management
- **ID:** API Management
- **Type:** Service
- **Description:** Secures and governs simulation APIs.

### Component 9: Azure Service Bus
- **ID:** Azure Service Bus
- **Type:** Service
- **Description:** Decouples simulation events and updates.

### Component 10: Azure Event Grid
- **ID:** Azure Event Grid
- **Type:** Service
- **Description:** Publishes scenario events to observers.

### Component 11: Azure SQL Database
- **ID:** Azure SQL Database
- **Type:** Service
- **Description:** Stores student submissions and scenario data.

### Component 12: Azure Cosmos DB
- **ID:** Azure Cosmos DB
- **Type:** Service
- **Description:** Stores real-time simulation state documents.

### Component 13: Azure Blob Storage
- **ID:** Azure Blob Storage
- **Type:** Service
- **Description:** Holds learning materials and logs.

### Component 14: Azure Key Vault
- **ID:** Azure Key Vault
- **Type:** Service
- **Description:** Secures secrets and credentials.

### Component 15: Azure Active Directory
- **ID:** Azure Active Directory
- **Type:** Service
- **Description:** Manages user authentication.

### Component 16: Azure Application Insights
- **ID:** Azure Application Insights
- **Type:** Service
- **Description:** Provides telemetry for performance monitoring.

### Component 17: Azure Monitor + Log Analytics Workspace
- **ID:** Azure Monitor + Log Analytics Workspace
- **Type:** Service
- **Description:** Centralizes logging and metrics.

### Component 18: Azure Backup
- **ID:** Azure Backup
- **Type:** Service
- **Description:** Manages backups for SQL and Blob storage.

### Component 19: Azure Firewall
- **ID:** Azure Firewall
- **Type:** Service
- **Description:** Ensures perimeter security.

### Component 20: Private Endpoints
- **ID:** Private Endpoints
- **Type:** Service
- **Description:** Provides secure access to data components.

### Component 21: Azure DevOps Pipelines
- **ID:** Azure DevOps Pipelines
- **Type:** Service
- **Description:** Automates CI/CD processes.

### Component 22: Bicep/Terraform IaC
- **ID:** Bicep/Terraform IaC
- **Type:** Service
- **Description:** Facilitates consistent infrastructure deployment.

## Architecture Decision Records
- **ADR 1:** Use Cosmos DB for real-time simulation state.
- **ADR 2:** Implement API Management for all simulation endpoints.
- **ADR 3:** Divide the simulation engine into App Service API and Functions.

## Architecture Principles
1. **Design for failure and controlled scenario simulation**  
   - *WAF Pillar:* Reliability
2. **Least privilege and secure data boundaries**  
   - *WAF Pillar:* Security
3. **Cost-efficient consumption-based compute**  
   - *WAF Pillar:* Cost Optimization
4. **Operational observability**  
   - *WAF Pillar:* Operational Excellence
5. **Performance-optimized event flow**  
   - *WAF Pillar:* Performance Efficiency
6. **Infrastructure as code**  
   - *WAF Pillar:* Operational Excellence
7. **Data protection at rest and in transit**  
   - *WAF Pillar:* Security
8. **Scalable separation of simulation engine and UI**  
   - *WAF Pillar:* Performance Efficiency
9. **Design for extensibility of new chemistry scenarios**  
   - *WAF Pillar:* Reliability
10. **Utilize managed services to reduce operational burden**  
    - *WAF Pillar:* Cost Optimization

## Feature to Component Mappings
- Simulate tank fill to 1100 L and enzyme feed of 50 L.
- Enforce temperature range (35–40 ºC) and handle denaturation at 50 ºC.
- Monitor scenario #2 for temperature trending out of spec.
- Manage scenario #3 for flow sensor FIT‑101 failure and pump runaway.
- Capture student responses for instructor review.
- Ensure secure access to data components.

## Fitness Functions
- **FF 1:** 
- **FF 2:** 
- **FF 3:** 
- **FF 4:** 
- **FF 5:** 

This refined document presents a structured and polished overview of the enterprise cloud architecture, making it suitable for client delivery while preserving all technical details.