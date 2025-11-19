# UX Specifications

## Executive Summary
This document outlines the user experience (UX) specifications for the tank simulation application. The specifications detail the key screens, user interactions, data flows, accessibility considerations, and overall content guidelines. This comprehensive approach aims to provide an effective learning tool for students to understand chemical processes and safety protocols while engaging with the application.

## Key Screens

### 1. Tank Configuration Screen
**Description:**  
The Tank Configuration Screen enables users to set the substrate volume for the tank setup process. Students can input specific volumes and observe the effects on the chemical processes within the simulation.

**Layout:**
- **Structure:** Header / Content / Footer
- **Breakpoints:**
  - Mobile: Single column, stacked components
  - Tablet: Two-column layout
  - Desktop: Three-column layout with sidebar
- **Grid:** 12 columns, 16px gap

**Components:**
- **Input Field:**
  - **Label:** Substrate Volume (Liters)
  - **Placeholder:** Enter volume
  - **Type:** Number
  - **Required:** Yes
  - **Range:** 0 to 2000 liters
  - **Style:** Height: 40px, Background: #1A1A1A, Text: #FFFFFF, Border: 1px solid #3B3B3B, Focus Border: 2px solid #3F7C37, Border Radius: 4px, Padding: 8px 12px, Font Size: 16px
  
**Interactions:**
- **Change Action:** Validates input on blur, providing visual feedback with a green border on valid input and a red border on invalid input.

**States:**
- Default: Normal state with white border
- Loading: Disabled with spinner
- Error: Red border with error message below
- Disabled: Grayed out, not editable
- Success: Green border with checkmark

**Validation:**
- **Rules:** Required, min: 0, max: 2000, pattern: ^[0-9]+$
- **Messages:** 
  - Error: Please enter a value between 0 and 2000 liters
  - Success: Valid input

**Accessibility:**
- **ARIA Label:** Substrate volume input in liters
- **ARIA Described By:** substrate-help-text
- **Keyboard Navigation:** Tab to focus, Enter to submit
- **Screen Reader Text:** Enter the substrate volume in liters, between 0 and 2000

**Navigation:**
- **From:** Home Screen
- **To:** Enzyme Configuration Screen
- **Breadcrumbs:** Home > Tank Configuration

**Data Flow:**
- **Inputs:** User enters substrate volume
- **Outputs:** POST /api/tank/configure with substrate volumes
- **API Calls:** POST /api/tank/configure

**Loading State:**
- **Indicator:** Spinner
- **Message:** Starting configuration...

**Error Handling:**
- Validation Errors: Inline error messages below the input field
- API Errors: Toast notifications with options to retry the configuration
- User Feedback: Clear error messages provided below the inputs

---

### 2. Enzyme Configuration Screen
**Description:**  
The Enzyme Configuration Screen allows users to specify the enzyme volume necessary for the tank simulation. Students can enter the exact amount of enzyme needed to facilitate the reaction process.

**Layout:**
- **Structure:** Header / Content / Footer
- **Breakpoints:**
  - Mobile: Single column, stacked components
  - Tablet: Two-column layout
  - Desktop: Three-column layout with sidebar
- **Grid:** 12 columns, 16px gap

**Components:**
- **Input Field:**
  - **Label:** Enzyme Volume (Liters)
  - **Placeholder:** Enter volume
  - **Type:** Number
  - **Required:** Yes
  - **Range:** 0 to 100 liters
  - **Style:** Height: 40px, Background: #1A1A1A, Text: #FFFFFF, Border: 1px solid #3B3B3B, Focus Border: 2px solid #3F7C37, Border Radius: 4px, Padding: 8px 12px, Font Size: 16px
  
**Interactions:**
- **Change Action:** Validates input on blur, providing visual feedback with a green border on valid input and a red border on invalid input.

**States:**
- Default: Normal state with white border
- Loading: Disabled with spinner
- Error: Red border with error message below
- Disabled: Grayed out, not editable
- Success: Green border with checkmark

**Validation:**
- **Rules:** Required, min: 0, max: 100, pattern: ^[0-9]+$
- **Messages:** 
  - Error: Please enter a value between 0 and 100 liters
  - Success: Valid input

**Accessibility:**
- **ARIA Label:** Enzyme volume input in liters
- **ARIA Described By:** enzyme-help-text
- **Keyboard Navigation:** Tab to focus, Enter to submit
- **Screen Reader Text:** Enter the enzyme volume in liters, between 0 and 100

**Navigation:**
- **From:** Tank Configuration Screen
- **To:** Submit Configuration Screen
- **Breadcrumbs:** Home > Enzyme Configuration

**Data Flow:**
- **Inputs:** User enters enzyme volume
- **Outputs:** POST /api/enzyme/configure with enzyme volumes
- **API Calls:** POST /api/enzyme/configure

**Loading State:**
- **Indicator:** Spinner
- **Message:** Configuring enzyme...

**Error Handling:**
- Validation Errors: Inline error messages below the input field
- API Errors: Toast notifications with options to retry the configuration
- User Feedback: Clear error messages provided below the inputs

---

### 3. Tank Status Monitor Screen
**Description:**  
The Tank Status Monitor Screen displays the current status of the tank, including temperature and pressure levels. Students can monitor these metrics in real-time to ensure they remain within safe limits.

**Layout:**
- **Structure:** Header / Content / Footer
- **Breakpoints:**
  - Mobile: Single column, full-width components
  - Tablet: Two-column layout
  - Desktop: Three-column layout with sidebar
- **Grid:** 12 columns, 16px gap

**Components:**
- **Gauge:**
  - **Label:** Temperature (ºC)
  - **Range:** Min: 0, Max: 100, Current: 30
  - **Unit:** ºC
  - **Warning Threshold:** 35
  - **Danger Threshold:** 50
  - **Style:** Height: 200px, Width: 100%, Background: #1A1A1A, Text: #FFFFFF, Border: none
  
**Interactions:**
- **Update Action:** Reflects changes in the tank temperature, providing visual updates with appropriate color changes.

**States:**
- Default: Shows current temperature
- Loading: Loading temperature data...
- Error: Error retrieving temperature data

**Accessibility:**
- **ARIA Label:** Current temperature gauge in Celsius
- **ARIA Described By:** N/A
- **Keyboard Navigation:** N/A
- **Screen Reader Text:** Current temperature is displayed in Celsius

**Navigation:**
- **From:** Submit Configuration Screen
- **To:** Final Report Screen
- **Breadcrumbs:** Home > Tank Status Monitor

**Data Flow:**
- **Inputs:** N/A
- **Outputs:** GET /api/tank/status
- **API Calls:** GET /api/tank/status

**Empty State:**
- **Message:** No temperature data available
- **Icon:** temperature-off
- **Action:** Refresh to see current temperatures

**Loading State:**
- **Indicator:** Spinner
- **Message:** Loading tank status...

**Error Handling:**
- Validation Errors: No validation errors applicable
- API Errors: Toast notifications for API errors
- User Feedback: Visual cues provided for temperature status

---

### 4. Final Report Screen
**Description:**  
The Final Report Screen presents a comprehensive report of the experiment, detailing all inputs, outputs, and any issues encountered during the simulation. Students can review this report to understand the processes involved.

**Layout:**
- **Structure:** Header / Content / Footer
- **Breakpoints:**
  - Mobile: Single column, stacked components
  - Tablet: Two-column layout
  - Desktop: Three-column layout with sidebar
- **Grid:** 12 columns, 16px gap

**Components:**
- **Table:**
  - **Data:** []
  - **Columns:**
    - Step (key: step)
    - Input (key: input)
    - Output (key: output)
    - Status (key: status)
  - **Style:** Background: #1A1A1A, Text: #FFFFFF, Border: 1px solid #3B3B3B
  
**Interactions:**
- **Click Action:** Expands row for detailed view, providing feedback by showing more details.

**States:**
- Default: Displays experiment summary
- Loading: Loading report data...
- Error: Error retrieving report data

**Accessibility:**
- **ARIA Label:** Experiment report table
- **ARIA Described By:** table-help-text
- **Keyboard Navigation:** Use arrow keys to navigate rows
- **Screen Reader Text:** Table displaying detailed experiment report

**Navigation:**
- **From:** Tank Status Monitor Screen
- **To:** Home Screen
- **