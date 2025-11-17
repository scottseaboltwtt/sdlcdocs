# Software Engineering Specifications

## Executive Summary
This document outlines the software engineering specifications for the Tank Management and Safety Management modules. It details the functionalities, data models, and adherence to safety standards necessary for the successful implementation of the system. The specifications aim to ensure clear communication among stakeholders and establish a shared understanding of system capabilities and requirements.

## Modules

### Tank Management Module
The Tank Management Module is responsible for simulating the filling and management of tank parameters, including substrate mixing and enzyme addition.

#### Functions

1. **initializeTank**
   - **Description:** Initializes the tank with a substrate mix.
   - **Inputs:**
     - `substrateVolume`: 1100 liters
   - **Outputs:**
     - `status`: "Tank successfully initialized."

2. **addEnzyme**
   - **Description:** Adds enzyme to the tank to enhance the substrate mix.
   - **Inputs:**
     - `enzymeVolume`: 50 liters
   - **Outputs:**
     - `status`: "Enzyme added successfully."

3. **setTemperature**
   - **Description:** Sets the temperature of the tank within specified limits.
   - **Inputs:**
     - `temperature`: Range of 35-40°C
   - **Outputs:**
     - `status`: "Temperature is set."

4. **simulateCoolingProcess**
   - **Description:** Simulates the operation of the cooling jacket during temperature elevation.
   - **Inputs:**
     - `coolingJacketValve`: "Open/Closed"
     - `heatingElement`: "On/Off"
   - **Outputs:**
     - `status`: "Cooling process simulated."

### Safety Management Module
The Safety Management Module monitors safety scenarios and provides feedback based on sensor data to ensure a safe learning environment.

#### Functions

1. **monitorFlowSensor**
   - **Description:** Monitors the status of the substrate flow sensor and simulates potential failure scenarios.
   - **Inputs:**
     - `flowSensorStatus`: "Operational/Failed"
   - **Outputs:**
     - `status`: "Flow sensor status monitored."

2. **alertStatus**
   - **Description:** Alerts users of unsafe conditions as determined by sensor monitoring.
   - **Inputs:**
     - `condition`: "Unsafe/Normal"
   - **Outputs:**
     - `alertMessage`: "Conditions are unsafe, notify supervisor."

## Data Models

### Tank
- **Properties:**
  - `volume`: 1100 liters
  - `enzymeVolume`: 50 liters
  - `temperature`: 
    - `min`: 35°C 
    - `max`: 40°C
  - `status`: "Idle"

### FlowSensor
- **Properties:**
  - `status`: "Operational/Failed"
  - `maxFlowRate`: 120 liters per minute

## Standards

### Chemical Process Safety
- **Description:** Compliance with chemical safety standards is essential to ensure student safety and proper handling of chemicals during operations.

## Conclusion
This document serves as a comprehensive guide for the development and implementation of the Tank Management and Safety Management modules. Adhering to the outlined specifications will facilitate the creation of a robust, safe, and efficient system that meets educational and operational objectives. Stakeholders are encouraged to review these specifications and provide feedback to ensure alignment with project goals.