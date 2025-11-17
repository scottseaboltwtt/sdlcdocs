# UX Specifications

## Executive Summary
This document outlines the UX specifications for the Mixture Submission, Process Monitoring, Cooling Management, and Emergency Response screens. It details the layout, components, data flow, and error handling to ensure a clear understanding of user interactions and system responses. The specifications aim to provide a user-friendly interface that meets educational and safety requirements in a laboratory setting.

## Key Screens

### 1. Mixture Submission Screen
**Description:** This screen enables users to submit substrate and enzyme mixtures for processing, ensuring that proper quantities and conditions are met.

#### Layout
- **Structure:** Header / Content / Footer
- **Breakpoints:**
  - **Mobile:** Single column layout with stacked inputs
  - **Tablet:** Two-column layout for inputs
  - **Desktop:** Three-column layout with main content and sidebar
- **Grid:**
  - **Columns:** 12
  - **Gap:** 16px

#### Components
1. **Input: Substrate Volume (Liters)**
   - **Properties:**
     - **Label:** Substrate Volume (Liters)
     - **Placeholder:** Enter 1100
     - **Type:** Number
     - **Required:** Yes
     - **Range:** 0 - 2000
     - **Step:** 1
     - **Unit:** L
     - **Style:** Custom styles for height, background, text, border, and padding
   - **Interactions:**
     - **Action:** Change
     - **Triggers:** Validates input on blur
     - **Feedback:** Visual indication of valid/invalid input
   - **States:** Default, Loading, Error, Disabled, Success
   - **Validation:**
     - **Rules:** Required, min: 0, max: 2000
     - **Messages:** Custom error and success messages
   - **Accessibility:**
     - **ARIA Label:** Input for substrate volume in liters
     - **Keyboard Navigation:** Tab to focus, Enter to submit

2. **Input: Enzyme Volume (Liters)**
   - **Properties:** Same as Substrate Volume with respective labels and ARIA details.

3. **Button: Submit Mixture**
   - **Properties:**
     - **Text:** Submit Mixture
     - **Variant:** Green Filled
     - **Size:** Large
     - **Disabled:** No
     - **Style:** Custom styles for height, background, text, and padding
   - **Interactions:**
     - **Action:** Click
     - **Triggers:** Sends mixture data to the server
     - **Feedback:** Shows loading spinner during submission
   - **States:** Default, Loading, Error, Disabled, Success
   - **Accessibility:**
     - **ARIA Label:** Submit mixture button
     - **Keyboard Navigation:** Tab to focus, Enter to click

#### Navigation
- **From:** Home Screen
- **To:** Process Monitoring Screen
- **Breadcrumbs:** Home > Mixture Submission

#### Data Flow
- **Inputs:** User enters substrate and enzyme volumes
- **Outputs:** POST /api/mixture/submit
- **APICalls:** POST /api/mixture/submit

#### Loading State
- **Indicator:** Spinner
- **Message:** Submitting mixture...

#### Error Handling
- **Validation Errors:** Inline error messages below each input
- **API Errors:** Toast notification for API errors
- **User Feedback:** Clear error messages with actionable guidance

---

### 2. Process Monitoring Screen
**Description:** This screen monitors the ongoing chemical process, displaying the current status of the mixture and temperature.

#### Layout
- **Structure:** Header / Content / Footer
- **Breakpoints:**
  - **Mobile:** Single column layout for status display
  - **Tablet:** Two-column layout with graph
  - **Desktop:** Three-column layout with detailed metrics
- **Grid:**
  - **Columns:** 12
  - **Gap:** 16px

#### Components
1. **Gauge: Current Temperature (°C)**
   - **Properties:**
     - **Label:** Current Temperature (°C)
     - **Range:** 0 - 100
     - **Current Value:** 37
     - **Warning Threshold:** 45
     - **Danger Threshold:** 50
     - **Style:** Custom styles for width, height, background, and text
   - **States:** Default, Warning, Danger
   - **Accessibility:**
     - **ARIA Label:** Current temperature gauge

2. **Card: Mixture Status**
   - **Properties:**
     - **Title:** Mixture Status
     - **Content:** Currently processing. Ensure temperature stays within range.
     - **Style:** Custom styles for background, text, border, and padding
   - **States:** Default, Warning, Error
   - **Accessibility:**
     - **ARIA Label:** Mixture status card

#### Navigation
- **From:** Mixture Submission Screen
- **To:** Cooling Management Screen
- **Breadcrumbs:** Home > Process Monitoring

#### Data Flow
- **Inputs:** Real-time temperature data from API
- **Outputs:** Updated status for monitoring
- **APICalls:** GET /api/process/status

#### Empty State
- **Message:** No process currently running.
- **Icon:** Warning
- **Action:** Start a new mixture.

#### Loading State
- **Indicator:** Spinner
- **Message:** Loading process status...

#### Error Handling
- **API Errors:** Display inline error messages
- **User Feedback:** Provide clear update messages

---

### 3. Cooling Management Screen
**Description:** This screen allows users to manage the cooling jacket, adjusting the valve to control the temperature effectively.

#### Layout
- **Structure:** Header / Content / Footer
- **Breakpoints:**
  - **Mobile:** Single column layout for controls
  - **Tablet:** Two-column layout for control elements
  - **Desktop:** Three-column layout with additional information
- **Grid:**
  - **Columns:** 12
  - **Gap:** 16px

#### Components
1. **Button: Open Cooling Jacket Valve**
   - **Properties:** Similar to Submit Mixture button with custom text and ARIA details.

2. **Input: Desired Temperature (°C)**
   - **Properties:** Same as above inputs with respective labels and ARIA details.

#### Navigation
- **From:** Process Monitoring Screen
- **To:** Emergency Response Screen
- **Breadcrumbs:** Home > Cooling Management

#### Data Flow
- **Inputs:** User inputs desired temperature
- **Outputs:** PUT /api/cooling/adjust
- **APICalls:** PUT /api/cooling/adjust

#### Loading State
- **Indicator:** Spinner
- **Message:** Adjusting cooling settings...

#### Error Handling
- **Validation Errors:** Inline messages displayed below input
- **API Errors:** Error notifications for failed adjustments
- **User Feedback:** Clarifying messages for user action

---

### 4. Emergency Response Screen
**Description:** This screen presents the emergency protocols and options for users when dangerous scenarios occur.

#### Layout
- **Structure:** Header / Content / Footer
- **Breakpoints:**
  - **Mobile:** Single column layout for readability
  - **Tablet:** Two-column layout with protocol options
  - **Desktop:** Three-column layout with detailed steps and actions
- **Grid:**
  - **Columns:** 12
  - **Gap:** 16px

#### Components
1. **Card: Emergency Protocols**
   - **Properties:** Similar to Mixture Status card with custom content and ARIA details.

2. **Button: Notify Supervisor**
   - **Properties:** Similar to Open Cooling Jacket Valve button with custom text and ARIA details.

#### Navigation
- **From:** Cooling Management Screen
- **To:** Home Screen
- **Breadcrumbs:** Home > Emergency Response

#### Data Flow
- **Inputs:** User clicks notify supervisor
- **Outputs:** POST /api/emergency/notify
- **APICalls:** POST /api/emergency/notify

#### Loading State
- **Indicator:** Spinner
- **Message:** Notifying supervisor...

#### Error Handling
- **API Errors:** Toast for failed notifications
- **User Feedback:** Clear guidance is provided after errors

---

## Flows

### Mixture Preparation
1. User navigates to Mixture Submission Screen.
2. User enters substrate and enzyme volumes.
3. User clicks 'Submit Mixture'.
4. Application processes the input and sends data to the back end.
5. User navigates to Process Monitoring Screen.

### Cooling Management
1. User navigates to Cooling Management Screen from Process Monitoring.
2. User adjusts cooling jacket settings.
3. User monitors temperature readouts.
4. If temperature is too high, user opens cooling jacket valve.
5. User confirms successful adjustment.

### Emergency Notification
1. User identifies an emergency scenario on Process Monitoring Screen.
2. User navigates to Emergency Response Screen.
3. User reads protocols and decides to notify supervisor.
4. User clicks 'Notify Supervisor'.
5. System sends notification and provides feedback.

### Process Monitoring
1. User navigates to Process Monitoring Screen.
2. User views temperature and mixture status.
3. If parameters exceed limits, user initiates cooling adjustments or notifications.

---

## Information Architecture
- **Mixture Submission:** Form to submit substrate and enzyme data for the processing tank.
- **Process Monitoring:** Real-time monitoring display of the processing parameters.
- **Cooling Management:** Controls to manage the cooling jacket, including open/close valves.
- **Emergency Response:** Protocols and actions