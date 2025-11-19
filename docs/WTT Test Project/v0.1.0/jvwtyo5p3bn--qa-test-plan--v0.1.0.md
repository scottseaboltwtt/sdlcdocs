# QA Test Plan

## Executive Summary
This QA Test Plan outlines the testing approach for the tank filling and maintenance process, ensuring functionality and safety through structured scenarios, test cases, and quality checks. The aim is to validate the operational environment's effectiveness, confirm adherence to safety regulations, and ensure that students are well-prepared to handle various operational conditions.

## Test Scenarios

### Scenario 1: Normal Process of Filling the Tank
- **Description**: This scenario involves the standard procedure for filling the tank and maintaining operational conditions.
- **Steps**:
  1. Start with an empty tank.
  2. Fill the tank with 1100 liters of substrate mix.
  3. Add 50 liters of enzyme (feed).
  4. Maintain the temperature between 35-40 ºC for a duration of 4 hours.
  5. Utilize low air pressure to pressurize the vessel for drainage.
- **Expected Outcome**: The tank should be filled correctly with the substrate and enzyme, temperature maintained, and the drainage process should occur smoothly.

### Scenario 2: Trending Out of Specification with Cooling Jacket Valve Initially Closed
- **Description**: This scenario examines the temperature control when the cooling jacket valve is closed.
- **Steps**:
  1. Begin filling the tank with substrate mix and enzyme.
  2. Monitor the temperature as it trends upwards beyond 40 ºC.
  3. Confirm that the cooling jacket valve is closed.
  4. Open the cooling jacket valve.
  5. Observe and monitor the temperature as it stabilizes back within safe limits.
- **Expected Outcome**: The temperature should return to an acceptable range after opening the cooling jacket valve.

### Scenario 3: Dangerous Scenario with Substrate Flow Sensor (FIT-101) Failure
- **Description**: This scenario simulates a failure of the substrate flow sensor to assess student responses to unsafe conditions.
- **Steps**:
  1. Start the substrate pump (P-101) while monitoring the flow.
  2. Simulate a failure in the substrate flow sensor (FIT-101).
  3. Observe the substrate flow at maximum speed (120 L/min) without regulation.
  4. Identify the rising substrate level in the tank.
  5. Instruct students to recognize the current unsafe conditions.
  6. Encourage students to notify the supervisor or take corrective actions (turn off the pump and valve for substrate).
- **Expected Outcome**: Students should recognize unsafe conditions and articulate necessary steps to ensure safety.

## Test Cases

| Test Case ID | Scenario ID | Steps | Expected Result |
|---------------|-------------|-------|------------------|
| TC1           | 1           | Execute the normal process as outlined in test scenario 1. | Successful completion of the mixture preparation and tank drainage. |
| TC2           | 2           | Simulate temperature rise by closing the cooling jacket valve during operation. | Temperature is corrected back to the acceptable range after activating the cooling jacket. |
| TC3           | 3           | Introduce a flow sensor failure and observe student responses. | Students demonstrate awareness of unsafe conditions and suggest appropriate supervisory communication. |

## Quality Checks

### Quality Check 1
- **Description**: Verify that the tank fills and operates under specified conditions effectively.
- **Criteria**: The tank must operate correctly with no overflow or overheating issues.

### Quality Check 2
- **Description**: Ensure that students are able to identify out-of-spec conditions.
- **Criteria**: Students' responses should demonstrate an understanding of procedures in non-standard operational scenarios.

### Quality Check 3
- **Description**: Assess students' ability to respond to a sensor failure.
- **Criteria**: Students must articulate recognition of hazards and corrective measures clearly.

## Conclusion
This QA Test Plan serves as a comprehensive guide to validate the operational integrity and safety protocols associated with tank filling and maintenance. By executing the outlined scenarios, test cases, and quality checks, we ensure that both the equipment and the personnel are prepared to handle various operational challenges effectively.