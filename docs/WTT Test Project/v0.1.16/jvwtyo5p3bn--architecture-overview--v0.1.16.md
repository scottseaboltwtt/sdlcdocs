# Architecture Overview

## Executive Summary
This document provides an overview of the architecture for an Azure-based interactive chemistry simulation tool designed to enhance student learning through controlled mixing processes. The tool simulates realistic tank operations, including substrate filling, enzyme addition, temperature maintenance, and pressure-assisted draining. It supports various scenarios, such as normal operations, trending out of specification, and failure conditions. This solution adheres to the Azure Well-Architected Framework, ensuring secure, cost-effective, reliable, and observable components that facilitate controlled operations, interactive student workflows, and safety modeling.

## Architecture Overview

### Key Features
- **Realistic Operations**: Simulates tank operations with specific parameters such as:
  - Filling to 1100 L of substrate
  - Adding 50 L of enzyme
  - Maintaining temperatures between 35–40°C
  - Pressure-assisted draining
- **Scenario Support**: Includes multiple operational scenarios:
  - Normal operations
  - Cooling jacket valve initially closed (trending out of spec)
  - FIT-101 failure causing substrate pump (P-101) overflow

### Architectural Principles
- **Reliability**: 
  - Utilizes Azure Functions with built-in retry policies to mirror real-world behavior during transient failures.
  - Employs Azure Service Bus to decouple simulation steps (filling, heating, cooling, pressure control) from the user interface, ensuring stable delivery under high student load.
  
- **Security**: 
  - Implements Azure AD authentication to restrict access for educators and students, applying least-privilege role-based access control (RBAC) across all services.
  - Stores sensitive configuration data, including simulation thresholds, in Azure Key Vault.

- **Cost Optimization**: 
  - Leverages serverless computing (Azure Functions, Static Web Apps) to minimize costs based on classroom or semester usage patterns.
  - Applies autoscaling on App Service Plan during large multi-student labs to manage resources effectively.

- **Operational Excellence**: 
  - Centralized monitoring is implemented using Azure Monitor, Log Analytics, and Application Insights to diagnose simulation anomalies.

- **Performance Efficiency**: 
  - Adopts an event-driven architecture (Azure Event Grid) to respond promptly to simulation changes, such as sudden temperature increases.
  - Utilizes Azure Cache for Redis to maintain low-latency tank state for interactive UI updates.

### Reference Architecture Components
- **User Interface**: Azure Static Web Apps for student interaction
- **API Backend**: Azure App Service for simulation control
- **Event Processing**: Azure Functions for event-driven simulation processors (filling, temperature model, pressure, pumps)
- **Event Queue**: Azure Service Bus for simulation events
- **Scenario Triggers**: Azure Event Grid for state change notifications
- **State Store**: Azure Cosmos DB for simulation state (tank level, temperature, pump status)
- **Real-time Caching**: Azure Cache for Redis for fast UI updates
- **Configuration Management**: Azure Key Vault for simulation thresholds
- **Identity Management**: Azure Active Directory (AD)
- **API Management**: Azure API Management as the entry point for API consumption
- **Monitoring**: Azure Monitor, Log Analytics, Application Insights for system logs and performance metrics
- **Storage**: Azure Storage Blob for scenario definitions, historical logs, and student submissions
- **Network Isolation**: Azure Virtual Network for backend services
- **Traffic Control**: Network Security Groups (NSGs) and Azure Firewall for perimeter filtering
- **Private Connectivity**: Private Endpoints for Cosmos DB, Key Vault, and Storage
- **Backup Solutions**: Azure Backup for long-term simulation log storage
- **Global Optimization**: Azure Front Door for global availability

### Data Flow
1. Student UI sends interaction event → API Management → App Service API
2. API requests simulation logic → Azure Functions → Service Bus
3. Service Bus event triggers simulation step processor (e.g., filling tank with 1100 L substrate)
4. Function updates real-time state → Cosmos DB
5. Function provides immediate UI-ready state → Redis Cache
6. Event Grid broadcasts scenario change (e.g., rising temperature due to closed cooling jacket valve)
7. Static Web App polls or subscribes for updated tank state via API
8. API retrieves state from Redis for fast rendering
9. API fetches historical or complex state from Cosmos DB as needed
10. Cosmos DB alerts trigger when values exceed thresholds (e.g., >50°C denaturing risk)
11. Alerts routed to Monitor with notifications via email/Teams for instructors
12. Key Vault provides secure access to temperature thresholds, pump maximum speed (120 L/min), etc.
13. Simulation history stored in Blob Storage for future review
14. All telemetry routed to Application Insights and Log Analytics
15. Private Endpoints secure traffic between App Service, Cosmos DB, and Key Vault

### Component Mappings
| Feature | Components |
| ------- | ---------- |
| Mixing simulation | Azure Functions, Cosmos DB, Event Grid, Service Bus |
| Temperature control modeling | Azure Functions, Key Vault thresholds |
| Enzyme denature point check | Functions, Cosmos DB, Monitor alerts |
| Pressure-assisted draining | Functions, event-driven simulation pipeline |
| Failure scenario FIT-101 sensor fails | Functions, Service Bus, Event Grid |
| Student interaction UI | Static Web Apps, API Management |
| Real-time tank display | Redis Cache, App Service API |
| Scenario logging | Blob Storage, Cosmos DB |

### Constraints and Impacts
- **Constraints**: No specific constraints provided; standard Azure Well-Architected Framework best practices have been applied.

### Quality Assurance Coverage
- **Reliability**: Ensured through Service Bus, retry mechanisms, and Functions scaling.
- **Security**: Guaranteed via Key Vault, Azure AD, and private endpoints.
- **Performance**: Maintained through Redis Cache and serverless computing.
- **Cost Optimization**: Achieved through serverless architecture and monitoring-based scaling.
- **Operational Excellence**: Enhanced via monitoring tools, log analytics, and alerting systems.

### Frameworks
- **Azure Well-Architected Framework**: 
  - Reliability: Service Bus and retry logic
  - Security: Azure AD, Key Vault, private endpoints
  - Operational Excellence: Monitoring and logging solutions
  - Cost Optimization: Utilization of serverless computing
  - Performance Efficiency: Redis Cache and scalable Functions

### Architectural Decisions
1. **ADR-001**: Adopted an event-driven architecture for simulation logic to decouple simulation steps, enhance reproducibility, and align with Azure's reliability and operational excellence standards.
2. **ADR-002**: Chose Cosmos DB for simulation state due to its low latency, high availability, and flexible schema for tracking complex tank states, thereby aligning with performance efficiency goals.
3. **ADR-003**: Selected Redis Cache for real-time UI state to provide students with low-latency feedback during interactions, supporting performance efficiency and cost optimization.

### Fitness Functions
- Response latency for tank state queries must remain under 100ms at peak load.
- Temperature anomaly detection must trigger an alert within 3 seconds of threshold violation.
- Simulation state consistency between Redis and Cosmos DB must exceed 99.9%.
- API availability must meet 99.9% to support classroom activities.

### Assumptions
- The architecture is based solely on user-provided context, with no prior vector memory found.
- Student count is unspecified; assumed concurrency ranges from 100 to 500 users.
- Scenarios (normal operation, cooling jacket issue, FIT-101 failure) are fully modeled.

### Open Questions
- Does the simulation require real-time graphing of temperature and flow over time?
- Should instructors be able to define custom scenarios beyond the three provided?
- Is multi-language support necessary for international chemistry programs?
- Should simulation logs be retained for educational auditing across multiple semesters?

## Conclusion
This architecture overview presents a comprehensive framework for an interactive chemistry simulation tool that leverages Azure services to provide a reliable, secure, and cost-effective learning environment. By adhering to best practices and established architectural principles, this solution aims to enhance student engagement and understanding of chemistry through realistic simulations and scenarios.