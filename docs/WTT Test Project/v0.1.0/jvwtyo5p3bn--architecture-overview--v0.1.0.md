# Architecture Overview

## Executive Summary
This document provides an overview of an Azure-based interactive chemistry simulation tool designed to enhance student learning through controlled mixing processes. The architecture facilitates realistic tank operations, including filling, enzyme addition, temperature maintenance, and pressure-assisted draining. It supports various scenarios, such as normal operation, out-of-spec conditions, and failure events. The solution is aligned with the Azure Well-Architected Framework, ensuring controlled operations, student interaction workflows, and safety modeling through secure, cost-efficient, reliable, and observable components.

## Architecture Summary
The architecture encompasses the following key features:
- **Realistic Operations**: Simulates tank operations including filling to 1100 L substrate, adding 50 L enzyme, and maintaining a temperature range of 35–40°C.
- **Scenarios**: Models conditions such as normal operations, cooling jacket valve failures, and dangerous situations like the FIT-101 failure.
- **Framework Alignment**: Adheres to Azure Well-Architected Framework pillars, ensuring reliability, security, performance efficiency, cost optimization, and operational excellence.

## Architectural Principles
The following principles guide the architecture design:

### Reliability
- **Event Processing**: Utilizes Azure Functions with retry policies to ensure simulations mimic real-world behavior under transient failures.
- **Decoupled Simulation**: Implements Azure Service Bus to separate simulation steps from the user interface, ensuring stable operation during high user loads.

### Security
- **Access Control**: Employs Azure AD for authentication, restricting access for educators and students while applying least-privilege role-based access control (RBAC).
- **Sensitive Data Protection**: Stores sensitive configurations, including simulation thresholds, in Azure Key Vault.

### Cost Optimization
- **Serverless Computing**: Leverages serverless technologies (Functions, Static Web Apps) to reduce costs for classroom and semester usage patterns.
- **Autoscaling**: Applies autoscaling on the App Service Plan only during large multi-student labs.

### Operational Excellence
- **Centralized Monitoring**: Implements Azure Monitor, Log Analytics, and Application Insights for diagnosing simulation anomalies and maintaining performance baselines.

### Performance Efficiency
- **Event-Driven Architecture**: Uses Azure Event Grid to react promptly to simulation changes, such as temperature fluctuations caused by closed cooling jacket valves.
- **Low-Latency Caching**: Employs Azure Cache for Redis to maintain real-time tank states for interactive UI updates.

### Continuous Improvement
- **Versioned Logic**: Treats simulation state transitions as versioned logic to support iterative improvements.

## Reference Architecture

### Key Components
- Azure Static Web Apps: Student interactive UI
- Azure App Service: API backend for simulation control
- Azure Functions: Event-driven simulation processors
- Azure Service Bus: Simulation event queue
- Azure Event Grid: Scenario triggers and state change notifications
- Azure Cosmos DB: Simulation state store
- Azure Cache for Redis: Real-time tank parameter cache
- Azure Key Vault: Configuration management
- Azure AD: Identity and access management
- Azure API Management: API consumption management
- Azure Monitor and Log Analytics: Monitoring and logging
- Application Insights: Telemetry for APIs and Functions
- Azure Storage Blob: Historical logs and student submissions
- Azure Virtual Network: Network isolation for backend services
- NSGs and Azure Firewall: Traffic control and perimeter filtering
- Private Endpoints: Secure connections for database and storage
- Azure Backup: Long-term storage of simulation logs
- Azure Front Door: Global availability optimization

### Data Flow
1. Student UI sends interaction event → API Management → App Service API
2. API requests simulation logic → Azure Functions → Service Bus
3. Service Bus triggers simulation step processor (e.g., filling tank)
4. Function updates real-time state → Cosmos DB
5. Function pushes immediate state → Redis Cache
6. Event Grid broadcasts scenario changes (e.g., temperature increase)
7. Static Web App polls for updated tank state via API
8. API retrieves fast state from Redis
9. API reads historical state from Cosmos DB as necessary
10. Cosmos DB triggers alerts when thresholds are exceeded
11. Alerts routed to Monitor → notifications for instructors
12. Key Vault provides access to secure configurations
13. Simulation history stored in Blob Storage for review
14. All telemetry routed to Application Insights and Log Analytics
15. Private Endpoints secure traffic between services

## Component Mapping
### Features to Components
- Mixing simulation: Azure Functions, Cosmos DB, Event Grid, Service Bus
- Temperature control: Azure Functions, Key Vault thresholds
- Enzyme denature check: Functions, Cosmos DB, Monitor alerts
- Pressure-assisted draining: Functions, event-driven pipeline
- Failure scenario modeling: Functions, Service Bus, Event Grid
- Student interaction UI: Static Web Apps, API Management
- Real-time tank display: Redis Cache, App Service API
- Scenario logging: Blob Storage, Cosmos DB

### Constraints Impact
- No specific constraints provided; standard Well-Architected Framework best practices applied.

### Quality Assurance Coverage
- Reliability: Ensured via Service Bus, retries, and Function scaling.
- Security: Managed through Key Vault, AAD, and private endpoints.
- Performance: Maintained via Redis Cache and serverless computing.
- Cost Optimization: Achieved through serverless architecture and monitoring-based scaling.
- Operational Excellence: Enhanced through centralized monitoring and alerts.

## Framework Compliance
- Azure Well-Architected Framework: Focuses on reliability, security, operational excellence, cost optimization, and performance efficiency.

## Architectural Decision Records (ADR)
1. **ADR-001**: Utilize event-driven architecture for simulation logic to enhance decoupling and reproducibility.
2. **ADR-002**: Implement Cosmos DB for simulation state management to achieve low latency and high availability.
3. **ADR-003**: Use Redis Cache for real-time state feedback to ensure low-latency user interactions.

## Fitness Functions
- Response latency for tank state queries should remain under 100ms at peak load.
- Temperature anomaly detection must trigger alerts within 3 seconds of threshold violations.
- Consistency between Redis and Cosmos DB simulation states must exceed 99.9%.
- API availability must meet 99.9% to support classroom activities.

## Assumptions
- The architecture is based solely on user-provided context; no prior vector memory found.
- Assumes student concurrency up to moderate classroom scale (100–500 users).
- Requires full modeling of scenarios: normal, cooling jacket issue, and FIT-101 failure.

## Open Questions
- Is real-time graphing of temperature and flow over time necessary?
- Should instructors have the ability to define custom scenarios beyond the three provided?
- Is multi-language support required for international chemistry programs?
- Should simulation logs be retained for educational auditing across multiple semesters?