# Requirements

## Executive Summary
This Business Analysis Document outlines the requirements for a simulation tool designed for chemistry students. The tool aims to enhance students' understanding of chemical processes, manage temperature during reactions, and ensure safety in laboratory scenarios. The document includes user stories, business rules, and functional requirements to guide the development of the simulation.

## User Stories
### User Story 1
**Title:** As a chemistry student, I want to simulate the filling of a tank with a substrate mix and enzyme so that I can understand the chemical processes involved.

**Acceptance Criteria:**
- The simulation allows students to fill the tank with 1100 liters of substrate mix.
- The simulation requires adding 50 liters of enzyme to the mixture.
- The temperature must be maintained between 35-40 ºC during the simulation.

### User Story 2
**Title:** As a chemistry student, I want to manage the temperature in the tank effectively to avoid enzyme denaturation.

**Acceptance Criteria:**
- The system alerts students if the temperature approaches 50 ºC.
- Students can open the cooling jacket valve to regulate temperature.
- Students receive feedback on the effectiveness of their temperature management.

### User Story 3
**Title:** As a chemistry student, I want to recognize and respond to substrate flow sensor failures so that I can ensure safety.

**Acceptance Criteria:**
- The simulation detects a failure in the FIT-101 flow sensor.
- The substrate pump operates at maximum speed, simulating unsafe conditions.
- Students are prompted with questions about recognizing hazards and notifying supervisors.

### User Story 4
**Title:** As a chemistry student, I want to receive guidance on what actions to take in an emergency situation to enhance my practical understanding.

**Acceptance Criteria:**
- The simulation provides scenarios where students must identify potential hazards.
- Guidance is available on turning off the pump and substrate valve.
- Students can identify the key components involved in managing an unsafe condition.

## Business Rules
- The simulation must accurately represent the chemical processes involved in the mixing and reaction of substances.
- Safety protocols must be integrated into the simulation scenario to ensure students learn the importance of handling chemicals safely.
- Students’ decisions must have consequences in the simulation to provide an immersive learning experience.

## Functional Requirements
| ID     | Description                                                                 | Type      |
|--------|-----------------------------------------------------------------------------|-----------|
| FR-001 | The system shall simulate the tank filling process with 1100 liters of substrate mix and 50 liters of enzyme. | Functional |
| FR-002 | The system shall maintain a temperature between 35-40 ºC during the simulation. | Functional |
| FR-003 | The system shall alert students if the temperature exceeds 50 ºC.         | Functional |
| FR-004 | The system shall include a failure scenario for the substrate flow sensor (FIT-101). | Functional |
| FR-005 | The simulation must provide feedback and consequences based on students' decisions. | Functional |

## Conclusion
This document outlines the necessary requirements for the development of a simulation tool aimed at enhancing the educational experience of chemistry students. By adhering to the specified user stories, business rules, and functional requirements, the simulation will provide a comprehensive and safe learning environment that fosters understanding of chemical processes and safety protocols.