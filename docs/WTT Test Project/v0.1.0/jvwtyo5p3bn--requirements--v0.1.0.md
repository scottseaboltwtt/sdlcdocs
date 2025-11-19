# Requirements

## Executive Summary
This document outlines the requirements for a simulation designed to enhance the learning experience of chemistry students. The simulation focuses on the processes involved in mixing chemical substrates and enzymes while prioritizing safety and temperature management. The intent is to provide an immersive educational tool that allows students to understand chemical interactions and the importance of adhering to safety protocols in a laboratory environment.

## User Stories
### User Story 1
**Title:** Simulation of Tank Filling  
**As a:** Chemistry student  
**I want to:** Simulate the filling of a tank with a substrate mix and enzyme  
**So that I can:** Understand the chemical processes involved.

**Acceptance Criteria:**
- The simulation allows students to fill the tank with 1100 liters of substrate mix.
- The simulation requires adding 50 liters of enzyme to the mixture.
- The temperature must be maintained between 35-40 ºC during the simulation.

### User Story 2
**Title:** Effective Temperature Management  
**As a:** Chemistry student  
**I want to:** Manage the temperature in the tank effectively  
**So that I can:** Avoid enzyme denaturation.

**Acceptance Criteria:**
- The system alerts students if the temperature approaches 50 ºC.
- Students can open the cooling jacket valve to regulate temperature.
- Students receive feedback on the effectiveness of their temperature management.

### User Story 3
**Title:** Recognizing Sensor Failures  
**As a:** Chemistry student  
**I want to:** Recognize and respond to substrate flow sensor failures  
**So that I can:** Ensure safety.

**Acceptance Criteria:**
- The simulation detects a failure in the FIT-101 flow sensor.
- The substrate pump operates at maximum speed, simulating unsafe conditions.
- Students are prompted with questions about recognizing hazards and notifying supervisors.

### User Story 4
**Title:** Emergency Action Guidance  
**As a:** Chemistry student  
**I want to:** Receive guidance on what actions to take in an emergency situation  
**So that I can:** Enhance my practical understanding.

**Acceptance Criteria:**
- The simulation provides scenarios where students must identify potential hazards.
- Guidance is available on turning off the pump and substrate valve.
- Students can identify the key components involved in managing an unsafe condition.

## Business Rules
- The simulation must accurately represent the chemical processes involved in the mixing and reaction of substances.
- Safety protocols must be integrated into the simulation scenario to ensure students learn the importance of handling chemicals safely.
- Students’ decisions must have consequences in the simulation for an immersive learning experience.

## Functional Requirements
| ID      | Description                                                                                   | Type       |
|---------|-----------------------------------------------------------------------------------------------|------------|
| FR-001  | The system shall simulate the tank filling process with 1100 liters of substrate mix and 50 liters of enzyme. | Functional |
| FR-002  | The system shall maintain a temperature between 35-40 ºC during the simulation.             | Functional |
| FR-003  | The system shall alert students if the temperature exceeds 50 ºC.                           | Functional |
| FR-004  | The system shall include a failure scenario for the substrate flow sensor (FIT-101).        | Functional |
| FR-005  | The simulation must provide feedback and consequences based on students' decisions.          | Functional |

## Conclusion
The requirements outlined in this document serve as a foundation for developing a simulation that enhances the educational experience for chemistry students. By focusing on the accurate representation of chemical processes and the integration of safety protocols, the simulation aims to prepare students for real-world laboratory scenarios.