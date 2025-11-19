# UX Specifications

## Executive Summary
This document outlines the UX specifications for a chemical simulation application designed for educational purposes. It includes detailed descriptions of key screens, user interactions, data flows, and accessibility guidelines, ensuring a user-friendly experience for students engaging with chemical processes.

## Key Screens

### 1. Tank Configuration Screen
**Description:**  
This screen enables users to configure the substrate volume for the tank setup process. Students can input specific volumes and observe the effects on the chemical processes within the simulation.

**Layout:**  
- **Structure:** Header, Content, Footer  
- **Breakpoints:**  
  - Mobile: Single column, stacked components  
  - Tablet: Two-column layout  
  - Desktop: Three-column layout with sidebar  
- **Grid:**  
  - Columns: 12  
  - Gap: 16px  

**Components:**
- **Type:** Input  
  - **Properties:**  
    - **Label:** Substrate Volume (Liters)  
    - **Placeholder:** Enter volume  
    - **Value:** ""  
    - **Type:** Number  
    - **Required:** True  
    - **Min:** 0  
    - **Max:** 2000  
    - **Step:** 1  
    - **Unit:** L  
    - **Style:**  
      - Height: 40px  
      - Background: #1A1A1A  
      - Text: #FFFFFF  
      - Border: 1px solid #3B3B3B  
      - Focus Border: 2px solid #3F7C37  
      - Border Radius: 4px  
      - Padding: 8px 12px  
      - Font Size: 16px  

  - **Interactions:**  
    - **Action:** Change  
    - **Triggers:** Validates input on blur  
    - **Feedback:** Green border on valid, red on invalid  

  - **States:**  
    - **Default:** Normal state with white border  
    - **Loading:** Disabled with spinner  
    - **Error:** Red border with error message below  
    - **Disabled:** Grayed out, not editable  
    - **Success:** Green border with checkmark  

  - **Validation:**  
    - **Rules:**  
      - Required  
      - Min: 0  
      - Max: 2000  
      - Pattern: ^[0-9]+$  
    - **Messages:**  
      - **Error:** Please enter a value between 0 and 2000 liters  
      - **Success:** Valid input  

  - **Accessibility:**  
    - **ARIA Label:** Substrate volume input in liters  
    - **ARIA Described By:** substrate-help-text  
    - **Keyboard Navigation:** Tab to focus, Enter to submit  
    - **Screen Reader Text:** Enter the substrate volume in liters, between 0 and 2000  

**Navigation:**  
- **From:** Home Screen  
- **To:** Enzyme Configuration Screen  
- **Breadcrumbs:** Home, Tank Configuration  

**Data Flow:**  
- **Inputs:** User enters substrate volume  
- **Outputs:** POST /api/tank/configure with substrate volumes  
- **API Calls:** POST /api/tank/configure  

**Loading State:**  
- **Indicator:** Spinner  
- **Message:** Starting configuration...  

**Error Handling:**  
- **Validation Errors:** Inline error messages below the input field  
- **API Errors:** Toast notifications with options to retry the configuration  
- **User Feedback:** Clear error messages provided below the inputs  

### 2. Enzyme Configuration Screen
**Description:**  
This screen allows users to input the enzyme volume necessary for the tank simulation. Students can specify the exact amount of enzyme needed to facilitate the reaction process.

**Layout:**  
- **Structure:** Header, Content, Footer  
- **Breakpoints:**  
  - Mobile: Single column, stacked components  
  - Tablet: Two-column layout  
  - Desktop: Three-column layout with sidebar  
- **Grid:**  
  - Columns: 12  
  - Gap: 16px  

**Components:**
- **Type:** Input  
  - **Properties:**  
    - **Label:** Enzyme Volume (Liters)  
    - **Placeholder:** Enter volume  
    - **Value:** ""  
    - **Type:** Number  
    - **Required:** True  
    - **Min:** 0  
    - **Max:** 100  
    - **Step:** 1  
    - **Unit:** L  
    - **Style:**  
      - Height: 40px  
      - Background: #1A1A1A  
      - Text: #FFFFFF  
      - Border: 1px solid #3B3B3B  
      - Focus Border: 2px solid #3F7C37  
      - Border Radius: 4px  
      - Padding: 8px 12px  
      - Font Size: 16px  

  - **Interactions:**  
    - **Action:** Change  
    - **Triggers:** Validates input on blur  
    - **Feedback:** Green border on valid, red on invalid  

  - **States:**  
    - **Default:** Normal state with white border  
    - **Loading:** Disabled with spinner  
    - **Error:** Red border with error message below  
    - **Disabled:** Grayed out, not editable  
    - **Success:** Green border with checkmark  

  - **Validation:**  
    - **Rules:**  
      - Required  
      - Min: 0  
      - Max: 100  
      - Pattern: ^[0-9]+$  
    - **Messages:**  
      - **Error:** Please enter a value between 0 and 100 liters  
      - **Success:** Valid input  

  - **Accessibility:**  
    - **ARIA Label:** Enzyme volume input in liters  
    - **ARIA Described By:** enzyme-help-text  
    - **Keyboard Navigation:** Tab to focus, Enter to submit  
    - **Screen Reader Text:** Enter the enzyme volume in liters, between 0 and 100  

**Navigation:**  
- **From:** Tank Configuration Screen  
- **To:** Submit Configuration Screen  
- **Breadcrumbs:** Home, Enzyme Configuration  

**Data Flow:**  
- **Inputs:** User enters enzyme volume  
- **Outputs:** POST /api/enzyme/configure with enzyme volumes  
- **API Calls:** POST /api/enzyme/configure  

**Loading State:**  
- **Indicator:** Spinner  
- **Message:** Configuring enzyme...  

**Error Handling:**  
- **Validation Errors:** Inline error messages below the input field  
- **API Errors:** Toast notifications with options to retry the configuration  
- **User Feedback:** Clear error messages provided below the inputs  

### 3. Tank Status Monitor Screen
**Description:**  
This screen displays the current status of the tank, including temperature and pressure levels. Students can monitor these metrics in real-time to ensure they remain within safe limits.

**Layout:**  
- **Structure:** Header, Content, Footer  
- **Breakpoints:**  
  - Mobile: Single column, full-width components  
  - Tablet: Two-column layout  
  - Desktop: Three-column layout with sidebar  
- **Grid:**  
  - Columns: 12  
  - Gap: 16px  

**Components:**
- **Type:** Gauge  
  - **Properties:**  
    - **Label:** Temperature (ºC)  
    - **Min:** 0  
    - **Max:** 100  
    - **Current:** 30  
    - **Unit:** ºC  
    - **Warning Threshold:** 35  
    - **Danger Threshold:** 50  
    - **Style:**  
      - Height: 200px  
      - Width: 100%  
      - Background: #1A1A1A  
      - Text: #FFFFFF  
      - Border: None  

  - **Interactions:**  
    - **Action:** Update  
    - **Triggers:** Reflects changes in the tank temperature  
    - **Feedback:** Visual update with appropriate color changes  

  - **States:**  
    - **Default:** Shows current temperature  
    - **Loading:** Loading temperature data...  
    - **Error:** Error retrieving temperature data  

  - **Accessibility:**  
    - **ARIA Label:** Current temperature gauge in Celsius  
    - **ARIA Described By:**  
    - **Keyboard Navigation:**  
    - **Screen Reader Text:** Current temperature is displayed in Celsius  

**Navigation:**  
- **From:** Submit Configuration Screen  
- **To:** Final Report Screen  
- **Breadcrumbs:** Home, Tank Status Monitor  

**Data Flow:**  
- **Inputs:** None  
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
- **Validation Errors:** No validation errors applicable  
- **API Errors:** Toast notifications for API errors  
- **User Feedback:** Visual cues provided for temperature status  

### 4. Final Report Screen
**Description:**  
This screen presents a comprehensive report of the experiment, including all inputs, outputs, and any issues encountered during the simulation. Students can review this report to understand the processes.

**Layout:**  
- **Structure:** Header, Content, Footer  
- **Breakpoints:**  
  - Mobile: Single column, stacked components  
  - Tablet: Two-column layout  
  -