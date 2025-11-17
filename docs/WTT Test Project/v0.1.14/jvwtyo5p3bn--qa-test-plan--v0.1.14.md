# QA Test Plan

## Executive Summary
The QA Test Plan outlines the testing strategy for the simulation of a chemical processing system. This document details the test scenarios, test cases, and quality checks to ensure that the system functions correctly and safely. The goal is to provide a comprehensive framework for validating the functionality and safety of the simulation, ensuring it meets the educational objectives for students interacting with the system.

## Test Scenarios

### Scenario 1: Normal Process
- **Scenario ID:** 1
- **Description:** The tank successfully fills with substrate mix and enzyme, maintains temperature, and allows for controlled drainage.
- **Expected Outcome:** The tank should fill successfully while maintaining the specified temperature range, facilitating a safe drainage process.

### Scenario 2: Trending Out of Spec - Cooling Jacket Valve Initially Closed
- **Scenario ID:** 2
- **Description:** The temperature increases out of specification due to the cooling jacket valve being initially closed.
- **Expected Outcome:** The situation is resolved by opening the cooling jacket valve, providing students with practical experience on when to activate the cooling system.

### Scenario 3: Dangerous Scenario - Substrate Flow Sensor Failure
- **Scenario ID:** 3
- **Description:** A failure of the substrate flow sensor leads to unsafe conditions.
- **Expected Outcome:** Students must identify the unsafe conditions and respond appropriately by notifying a supervisor or shutting down the pump and valve.

## Test Cases

### Test Case 1: Normal Process
- **Test Case ID:** 1
- **Scenario ID:** 1
- **Steps:**
  1. Start the simulation with an empty tank.
  2. Fill the tank with 1100 liters of substrate mix.
  3. Add 50 liters of enzyme.
  4. Maintain the temperature between 35-40 ºC for 4 hours.
  5. Use air pressure to drain the tank.
- **Expected Result:** The tank should fill successfully and maintain the correct temperature for the duration, allowing for a safe drainage process.

### Test Case 2: Cooling Jacket Valve Interaction
- **Test Case ID:** 2
- **Scenario ID:** 2
- **Steps:**
  1. Start the simulation.
  2. Initiate the cooling jacket valve in the closed position.
  3. Monitor the temperature as it trends upwards.
  4. Open the valve to the cooling jacket when the temperature exceeds 40 ºC.
- **Expected Result:** The temperature should stabilize upon opening the cooling jacket valve, illustrating the need for corrective action in real scenarios.

### Test Case 3: Substrate Flow Sensor Failure
- **Test Case ID:** 3
- **Scenario ID:** 3
- **Steps:**
  1. Start the simulation with the flow sensor (FIT-101) disabled.
  2. Observe the operation of the substrate pump (P-101) flowing at maximum speed (120 L/min).
  3. Monitor the tank level rise and identify unsafe conditions.
  4. Notify the supervisor or turn off the pump and valve.
- **Expected Result:** Students should correctly identify the risk and react appropriately, thereby reinforcing safety protocols.

## Quality Checks

### Quality Check 1: Simulation Accuracy
- **Check ID:** 1
- **Description:** Ensure that the simulation accurately reflects the chemical processes described in the project's goals.
- **Criteria:** All components should function as designed without critical failures during the normal process.

### Quality Check 2: Response to Out-of-Spec Conditions
- **Check ID:** 2
- **Description:** Validate that the system responds correctly to out-of-spec conditions.
- **Criteria:** Responses to temperature or pressure changes must align with real-world operating procedures.

### Quality Check 3: User Understanding in Dangerous Scenarios
- **Check ID:** 3
- **Description:** Assess user understanding during dangerous scenarios.
- **Criteria:** Students should demonstrate knowledge of safety practices and how to manage equipment failures effectively. 

## Conclusion
The QA Test Plan aims to ensure that the simulation not only meets technical requirements but also effectively educates students on safe practices in chemical processing. By adhering to the structured testing scenarios and quality checks outlined in this document, we will enhance the learning experience and reinforce critical safety protocols within the educational framework.