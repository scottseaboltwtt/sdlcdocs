# QA Test Plan

## Executive Summary
This QA Test Plan outlines the testing procedures and quality assurance measures for the tank filling and monitoring process. It encompasses various test scenarios aimed at ensuring the proper functioning and safety of the system, particularly in situations involving temperature regulation and sensor integrity. The document serves as a comprehensive guide for stakeholders and team members involved in the testing process, providing clarity and direction for achieving quality outcomes.

## Test Scenarios

### Scenario 1: Normal Process of Filling the Tank
- **Description:** This scenario involves the standard operation of filling the tank and maintaining optimal conditions.
- **Steps:**
  1. Begin with an empty tank.
  2. Fill the tank with 1100 liters of substrate mix.
  3. Add 50 liters of enzyme (feed).
  4. Maintain the temperature between 35-40 ºC for 4 hours.
  5. Utilize low air pressure to pressurize the vessel for drainage.
- **Expected Outcome:** The tank is filled correctly with the substrate and enzyme, the temperature is maintained within the specified range, and the drainage process occurs smoothly.

### Scenario 2: Trending Out of Specification with Cooling Jacket Valve Initially Closed
- **Description:** This scenario examines the temperature regulation process when the cooling jacket valve is initially closed.
- **Steps:**
  1. Begin filling the tank with substrate mix and enzyme.
  2. Monitor the temperature as it trends upwards beyond 40 ºC.
  3. Confirm that the cooling jacket valve is closed.
  4. Open the cooling jacket valve.
  5. Monitor the temperature until it stabilizes back within safe limits.
- **Expected Outcome:** The temperature returns to the acceptable range after the cooling jacket valve is opened.

### Scenario 3: Dangerous Scenario with Substrate Flow Sensor (FIT-101) Failure
- **Description:** This scenario simulates a failure in the substrate flow sensor and its implications.
- **Steps:**
  1. Start the substrate pump (P-101) while monitoring the flow.
  2. Simulate a failure in the substrate flow sensor (FIT-101).
  3. Observe that the substrate flow reaches maximum speed (120 L/min) without regulation.
  4. Identify the rising substrate level in the tank.
  5. Instruct students to identify current unsafe conditions.
  6. Encourage students to notify the supervisor or take corrective actions (turn off the pump and valve for substrate).
- **Expected Outcome:** Students recognize unsafe conditions and articulate the necessary steps to ensure safety.

## Test Cases

| Test Case ID | Scenario ID | Steps                                                              | Expected Result                                                 |
|---------------|-------------|--------------------------------------------------------------------|----------------------------------------------------------------|
| TC1           | 1           | Execute normal process as outlined in test scenario 1.           | Successful completion of the mixture preparation and tank drainage. |
| TC2           | 2           | Simulate temperature rise by closing the cooling jacket valve.     | Temperature is corrected back to acceptable range after cooling jacket activation. |
| TC3           | 3           | Introduce a flow sensor failure and observe student responses.     | Students demonstrate awareness of unsafe conditions and suggest appropriate supervisory communication. |

## Quality Checks

### Quality Check 1
- **Description:** Verify that the tank fills and operates under specified conditions effectively.
- **Criteria:** The tank operates correctly with no overflow or overheating issues.

### Quality Check 2
- **Description:** Ensure that students can identify out-of-spec conditions.
- **Criteria:** Students' responses should demonstrate an understanding of procedures in non-standard operation scenarios.

### Quality Check 3
- **Description:** Assess students' ability to respond to a sensor failure.
- **Criteria:** Students articulate recognition of hazards and corrective measures clearly.

## Conclusion
This QA Test Plan ensures that all testing scenarios are thoroughly evaluated, and quality checks are implemented to maintain the highest standards of safety and operational efficiency. By adhering to the outlined procedures and outcomes, we aim to achieve a reliable and effective system for tank filling and monitoring processes.