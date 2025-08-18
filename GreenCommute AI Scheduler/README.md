# AI-Powered Green Commute Impact Scheduler

## Overview
This workflow provides daily recommendations for the most environmentally friendly and time-efficient commute option. It analyzes different commute modes, estimates their carbon footprint and transit time for a fixed distance, and sends personalized daily reminders and reports via email to encourage green commuting habits.

---

## Workflow Nodes and Details

### 1. Trigger Daily Commute Check
- **Type:** Schedule Trigger  
- **Description:** Triggers the workflow every day at 7:00 AM.
- **Purpose:** Initiates the commute impact calculation and recommendation process each morning.

### 2. Commute Options Input
- **Type:** Set Node  
- **Parameters:**  
  - Commute options: `Driving`, `Biking`, `Public Transport` (multiple selections allowed)  
- **Description:** Defines the set of commute modes to evaluate.

### 3. Calculate Impact
- **Type:** Function Node  
- **Function Code:**  
  - Reads the commute options list.  
  - Simulates carbon footprint (kg CO2) and transit time (minutes) per km for each mode using predefined data:  
    - Driving: 0.25 kg CO2/km, 40 km/h  
    - Biking: 0 kg CO2/km, 15 km/h  
    - Public Transport: 0.05 kg CO2/km, 25 km/h  
  - Uses a fixed commute distance of 10 km.  
  - Calculates total estimated carbon emissions and transit time for each mode.  
  - Outputs a list of objects containing:  
    - Mode  
    - Estimated carbon footprint (kg CO2)  
    - Estimated transit time (minutes)  
    - Or error if data unavailable.

### 4. Generate Recommendation
- **Type:** Function Node  
- **Function Code:**  
  - Takes all calculated impacts as input.  
  - Filters out any invalid or error entries.  
  - Sorts the commute modes by ascending carbon footprint.  
  - Picks the mode with the lowest carbon footprint as the best option.  
  - Constructs a recommendation object containing:  
    - Best commute mode  
    - Corresponding carbon footprint  
    - Estimated transit time  
    - A user-friendly recommendation message.

### 5. Daily Reminder
- **Type:** Email Send Node  
- **Parameters:**  
  - Email content:  
    ```
    Reminder: Consider taking greener commute options today like {{ $json.bestCommuteMode }}. Estimated carbon footprint: {{ $json.carbonFootprintKgCO2 }} kg CO2, Transit time: {{ $json.estimatedTimeMin }} minutes.
    ```  
- **Description:** Sends a daily reminder email encouraging users to choose the recommended green commute option.

### 6. Daily Report
- **Type:** Email Send Node  
- **Parameters:**  
  - Email content:  
    ```
    Daily Green Commute Report:
    Recommended Mode: {{ $json.bestCommuteMode }}
    Estimated Carbon Footprint: {{ $json.carbonFootprintKgCO2 }} kg CO2
    Estimated Transit Time: {{ $json.estimatedTimeMin }} minutes

    Keep up the sustainable commuting habits!
    ```  
- **Description:** Sends a detailed daily report email summarizing the green commute recommendation and related impact metrics.

---

## Connections Flow
1. **Trigger Daily Commute Check** → starts the workflow  
2. Passes to **Commute Options Input** → sets the commuting options  
3. **Commute Options Input** → passes options to **Calculate Impact** → calculates carbon and time impact  
4. **Calculate Impact** → forwards results to **Generate Recommendation** → sorts and chooses best option  
5. **Generate Recommendation** → sends output to both **Daily Reminder** and **Daily Report** → sends respective emails

---

## Requirements & Configuration
- **SMTP Credentials:** Required for the two Email Send nodes (`Daily Reminder` & `Daily Report`) to send emails properly.  
- **Fixed Commute Distance:** Set at 10 km within the function node for calculations; can be adjusted as needed in code.  
- **Commute Modes:** Currently set as Driving, Biking, and Public Transport; can be modified in the `Commute Options Input` node.

---

## Summary
This workflow automates the evaluation of daily commute options against their environmental impact and travel times, recommending the greenest commutes while providing timely, actionable reminders and reports to users, helping promote sustainable travel habits every day.