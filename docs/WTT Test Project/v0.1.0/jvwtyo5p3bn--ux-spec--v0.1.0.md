# UX Specifications

## Executive Summary
This document provides a comprehensive overview of the UX specifications for the chemical processing application. It details the key screens, components, user interactions, navigation flows, and accessibility considerations essential for an optimal user experience. The intended audience includes stakeholders and client representatives who require a clear understanding of the application's design and functionality.

## Key Screens

### 1. Mixture Submission Screen
#### Description
The Mixture Submission Screen enables users to submit substrate and enzyme mixtures for processing, ensuring that the correct quantities and conditions are met.

#### Layout
- **Structure:** Header / Content / Footer
- **Breakpoints:**
  - **Mobile:** Single-column layout with stacked inputs
  - **Tablet:** Two-column layout for inputs
  - **Desktop:** Three-column layout with main content and sidebar
- **Grid:**
  - **Columns:** 12
  - **Gap:** 16px

#### Components

##### Input: Substrate Volume (Liters)
- **Properties:**
  - **Label:** Substrate Volume (Liters)
  - **Placeholder:** Enter 1100
  - **Type:** Number
  - **Required:** Yes
  - **Range:** 0 - 2000
  - **Step:** 1
  - **Unit:** L
  - **Style:**
    - Height: 40px
    - Background: #1A1A1A
    - Text Color: #FFFFFF
    - Border: 1px solid #3B3B3B
    - Focus Border: 2px solid #3F7C37
    - Border Radius: 8px
    - Padding: 8px 12px
    - Margin: 8px 0
    - Font Size: 16px
    - Font Weight: 400

- **Interactions:**
  - **Action:** Change
  - **Triggers:** Validates input on blur
  - **Feedback:** Visual indication of valid/invalid input

- **States:**
  - Default: Input is empty with a gray border.
  - Loading: Input is disabled with a spinner.
  - Error: Input shows a red border with an error message.
  - Disabled: Input is grayed out.
  - Success: Input shows a green border with a checkmark.

- **Validation:**
  - **Rules:** Required, min: 0, max: 2000
  - **Messages:**
    - Error: Please enter a value between 0 and 2000 liters.
    - Success: Valid input.

- **Accessibility:**
  - **ARIA Label:** Input for substrate volume in liters
  - **ARIA Described By:** substrate-help-text
  - **Keyboard Navigation:** Tab to focus, Enter to submit
  - **Screen Reader Text:** Enter the substrate volume in liters.

##### Input: Enzyme Volume (Liters)
- **Properties:** (Similar structure as Substrate Volume)
- **Accessibility:** (Similar structure as Substrate Volume)

##### Button: Submit Mixture
- **Properties:**
  - **Text:** Submit Mixture
  - **Variant:** greenFilled
  - **Size:** Large
  - **Disabled:** No
  - **Style:**
    - Height: 40px
    - Background: #509D45
    - Text Color: #FFFFFF
    - Border: None
    - Hover Background: #3F7C37
    - Border Radius: 12px
    - Padding: 8px 16px
    - Font Size: 16px
    - Font Weight: 500

- **Interactions:**
  - **Action:** Click
  - **Triggers:** Sends mixture data to the server
  - **Feedback:** Shows loading spinner during submission

- **States:**
  - Default: Button is active.
  - Loading: Button shows spinner and is disabled.
  - Error: Button is red and shows an error state.
  - Disabled: Button is grayed out.
  - Success: Button shows a green checkmark.

- **Accessibility:**
  - **ARIA Label:** Submit mixture button
  - **ARIA Described By:** submit-help-text
  - **Keyboard Navigation:** Tab to focus, Enter to click
  - **Screen Reader Text:** Click to submit mixture.

#### Navigation
- **From:** Home Screen
- **To:** Process Monitoring Screen
- **Breadcrumbs:** Home > Mixture Submission

#### Data Flow
- **Inputs:** User enters substrate and enzyme volumes
- **Outputs:** POST /api/mixture/submit
- **API Calls:** POST /api/mixture/submit

#### Loading State
- **Indicator:** Spinner
- **Message:** Submitting mixture...

#### Error Handling
- **Validation Errors:** Inline error messages below each input.
- **API Errors:** Toast notification for API errors.
- **User Feedback:** Clear error messages with actionable guidance.

---

### 2. Process Monitoring Screen
#### Description
This screen monitors the ongoing chemical process, displaying the current status of the mixture and temperature.

#### Layout
- **Structure:** Header / Content / Footer
- **Breakpoints:**
  - **Mobile:** Single-column layout for status display
  - **Tablet:** Two-column layout with graph
  - **Desktop:** Three-column layout with detailed metrics
- **Grid:**
  - **Columns:** 12
  - **Gap:** 16px

#### Components

##### Gauge: Current Temperature (°C)
- **Properties:**
  - **Label:** Current Temperature (°C)
  - **Min:** 0
  - **Max:** 100
  - **Current:** 37
  - **Unit:** °C
  - **Warning Threshold:** 45
  - **Danger Threshold:** 50
  - **Style:**
    - Width: 100%
    - Height: 50px
    - Background: #1A1A1A
    - Text Color: #FFFFFF
    - Border: None

- **States:**
  - Default: Gauge displays current temperature.
  - Warning: Gauge color changes to yellow.
  - Danger: Gauge color changes to red.

- **Accessibility:**
  - **ARIA Label:** Current temperature gauge
  - **ARIA Described By:** temperature-description
  - **Screen Reader Text:** Current temperature in degrees Celsius.

##### Card: Mixture Status
- **Properties:**
  - **Title:** Mixture Status
  - **Content:** Currently processing. Ensure temperature stays within range.
  - **Style:** (Similar properties as above)

- **States:**
  - Default: Card displays status information.
  - Warning: Card highlights.
  - Error: Card shows error message.

- **Accessibility:** (Similar structure as Gauge)

#### Navigation
- **From:** Mixture Submission Screen
- **To:** Cooling Management Screen
- **Breadcrumbs:** Home > Process Monitoring

#### Data Flow
- **Inputs:** Real-time temperature data from API
- **Outputs:** Updated status for monitoring
- **API Calls:** GET /api/process/status

#### Loading State
- **Indicator:** Spinner
- **Message:** Loading process status...

#### Error Handling
- **API Errors:** Display inline error messages.
- **User Feedback:** Provide clear update messages.

---

### 3. Cooling Management Screen
#### Description
This screen allows users to manage the cooling jacket, adjusting the valve to control the temperature effectively.

#### Layout
- **Structure:** Header / Content / Footer
- **Breakpoints:**
  - **Mobile:** Single-column layout for controls
  - **Tablet:** Two-column layout with control elements
  - **Desktop:** Three-column layout with additional information
- **Grid:**
  - **Columns:** 12
  - **Gap:** 16px

#### Components

##### Button: Open Cooling Jacket Valve
- **Properties:** (Similar structure as Submit Mixture button)

- **Interactions:**
  - **Action:** Click
  - **Triggers:** Opens the cooling jacket valve.
  - **Feedback:** Shows success message upon operation.

- **States:** (Similar structure as Submit Mixture button)

- **Accessibility:** (Similar structure as Submit Mixture button)

##### Input: Desired Temperature (°C)
- **Properties:** (Similar structure as Substrate Volume)

#### Navigation
- **From:** Process Monitoring Screen
- **To:** Emergency Response Screen
- **Breadcrumbs:** Home > Cooling Management

#### Data Flow
- **Inputs:** User inputs desired temperature
- **Outputs:** PUT /api/cooling/adjust
- **API Calls:** PUT /api/cooling/adjust

#### Loading State
- **Indicator:** Spinner
- **Message:** Adjusting cooling settings...

#### Error Handling
- **Validation Errors:** Inline messages displayed below input.
- **API Errors:** Error notifications for failed adjustments.
- **User Feedback:** Clarifying messages for user action.

---

### 4. Emergency Response Screen
#### Description
This screen presents emergency protocols and options for users when dangerous scenarios occur.

#### Layout
- **Structure:** Header / Content / Footer
- **Breakpoints:**
  - **Mobile:** Single-column layout for readability.
  - **Tablet:** Two-column layout with protocol options.
  - **Desktop:** Three-column layout with detailed steps and actions.
- **Grid:**
  - **Columns:** 12
  - **Gap:** 16px

#### Components

##### Card: Emergency Protocols
- **Properties:**
  - **Title:** Emergency Protocols
  - **Content:** In case