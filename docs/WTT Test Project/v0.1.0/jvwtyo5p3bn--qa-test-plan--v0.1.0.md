# QA Test Plan

## Introduction
This Quality Assurance (QA) Test Plan outlines the testing strategies and scenarios for the chemical simulation system designed to ensure optimal performance and safety compliance. The plan encompasses various test scenarios, test cases, and quality checks that will validate the system's functionality and user understanding in operational contexts.

## Test Scenarios
### Scenario 1: Normal Process
- **Description:** The tank fills with the substrate mix and enzyme while maintaining the required temperature and allowing for controlled drainage.
- **Expected Outcome:** The tank successfully fills with the substrate mix and enzyme, maintains a temperature range of 35-40 ºC for four hours, and allows for safe drainage.

### Scenario 2: Trending Out of Specification - Cooling Jacket Valve Initially Closed
- **Description:** The temperature increases beyond the specified range due to the cooling jacket valve being closed.
- **Expected Outcome:** Temperature exceeds 40 ºC, which is resolved by opening the cooling jacket valve. This scenario highlights when students should activate the cooling jacket.

### Scenario 3: Dangerous Scenario - Substrate Flow Sensor Failure
- **Description:** The substrate flow sensor fails, potentially creating unsafe conditions.
- **Expected Outcome:** Students recognize unsafe conditions resulting from the sensor failure and take appropriate action by either notifying a supervisor or shutting down the pump and valve.

## Test Cases
### Test Case 1: Normal Process Execution
- **Test Case ID:** 1
- **Scenario ID:** 1
- **Steps:**
  1. Start the simulation with an empty tank.
  2. Fill the tank with 1100 liters of substrate mix.
  3. Add 50 liters of enzyme.
  4. Maintain the temperature between 35-40 ºC for 4 hours.
  5. Use air pressure to drain the tank.
- **Expected Result:** The tank fills successfully and maintains the correct temperature for the duration, allowing for a safe drainage process.

### Test Case 2: Response to Out-of-Spec Conditions
- **Test Case ID:** 2
- **Scenario ID:** 2
- **Steps:**
  1. Start the simulation.
  2. Initiate the cooling jacket valve in the closed position.
  3. Monitor temperature as it trends upwards.
  4. Open the valve to the cooling jacket when temperature exceeds 40 ºC.
- **Expected Result:** The temperature stabilizes upon opening the cooling jacket valve, demonstrating the necessity of corrective action in real scenarios.

### Test Case 3: Handling Sensor Failure
- **Test Case ID:** 3
- **Scenario ID:** 3
- **Steps:**
  1. Start the simulation with the flow sensor (FIT-101) disabled.
  2. Observe the operation of the substrate pump (P-101) flowing at maximum speed (120 L/min).
  3. Monitor the tank level rise and identify unsafe conditions.
  4. Notify the supervisor or turn off the pump and valve.
- **Expected Result:** Students correctly identify the risk and respond appropriately, reinforcing adherence to safety protocols.

## Quality Checks
### Quality Check 1: Simulation Accuracy
- **Check ID:** 1
- **Description:** Ensure that the simulation accurately reflects the chemical processes outlined in the project's goals.
- **Criteria:** All components must function as designed without critical failures during the normal process.

### Quality Check 2: System Response Validation
- **Check ID:** 2
- **Description:** Validate the system's response to out-of-spec conditions.
- **Criteria:** Responses to temperature or pressure changes must align with real-world operating procedures.

### Quality Check 3: User Understanding Assessment
- **Check ID:** 3
- **Description:** Assess user understanding during scenarios involving potential danger.
- **Criteria:** Students should demonstrate knowledge of safety practices and effectively manage equipment failures.

## Conclusion
This QA Test Plan is designed to ensure comprehensive testing of the chemical simulation system, focusing on both functionality and safety. By executing the outlined test scenarios and cases, we aim to validate the system's performance, enhance user understanding, and maintain alignment with industry standards.