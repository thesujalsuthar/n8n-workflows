# AI-Driven Personalized Sleep Quality Improvement and Smart Alarm System

This workflow utilizes wearable device data and advanced AI to analyze your sleep patterns, provide personalized recommendations, and set a smart alarm to optimize your wake-up time. Below is a detailed description of each step in the workflow.

---

## Workflow Overview

The workflow fetches sleep, heart rate, and movement data from a wearable device, preprocesses it, analyzes the data using an AI model (GPT-4), determines the optimal wake-up time based on sleep cycles, and sends a personalized notification including the alarm time and sleep improvement advice.

---

## Nodes Description

### 1. Start Data

- **Type:** Function
- **Purpose:** Initializes user context and wearable device information.
- **Details:**  
  Returns static user data including:
  - `deviceId` (e.g., 'user-wearable-001')
  - `userEmail` (e.g., 'user@example.com')

---

### 2. Fetch Wearable Sleep Data

- **Type:** API node with OAuth2 authentication
- **Purpose:** Retrieves raw data from the wearable health device.
- **Parameters:**
  - Authentication via OAuth2.
  - Retrieves data for the current day from 00:00:00 to now.
  - Data types: sleep stages, heart rate, and movement.
- **Output:** Raw dataset containing time-series information for sleep stages, heart rate, and movement.

---

### 3. Preprocess Sleep Data

- **Type:** Function
- **Purpose:** Cleans and combines multiple data streams into a unified structure.
- **Process:**
  - Extracts arrays for sleep segments, heart rate, and movement.
  - Creates a combined dataset indexed by sleep segments with:
    - Start and end times.
    - Sleep stage classification (e.g., light, deep, REM).
    - Average heart rate.
    - Movement intensity.
- **Output:** Structured and aligned sleep data ready for analysis.

---

### 4. Analyze Sleep Patterns and Recommend Improvements

- **Type:** OpenAI GPT-4
- **Purpose:** Performs AI-driven interpretation of processed sleep data.
- **Prompt Details:**
  - Inputs combined sleep, heart rate, and movement data.
  - Requests:
    - Identification of sleep patterns.
    - Detection of potential sleep issues.
    - Detailed explanations.
    - Personalized recommendations including sleep hygiene, environment, timing, and behavior advice.
- **Output:** Textual analysis and actionable recommendations to improve sleep quality.

---

### 5. Determine Optimal Wake Up Time

- **Type:** Function
- **Purpose:** Calculates the best alarm time based on sleep cycle analysis.
- **Logic:**
  - Parses AI-generated recommendations.
  - Locates light sleep phases occurring after the current time.
  - Sets the alarm to the earliest upcoming light sleep phase end time.
  - Defaults to 8 hours from current time if no suitable phase is found.
- **Output:** JSON containing:
  - Sleep improvement recommendations.
  - Optimal wake-up time in ISO string format.

---

### 6. Send Smart Alarm Notification

- **Type:** Push Notification
- **Purpose:** Notifies the user with their personalized sleep advice and smart alarm time.
- **Parameters:**
  - High priority notification.
  - Title: "Smart Alarm Notification"
  - Message includes:
    - Optimal wake-up time.
    - AI-generated sleep recommendations.
  - Sends notification to the user's registered email.
  
---

## How It Works Step-by-Step

1. The workflow starts with `Start Data` providing the wearable `deviceId` and user email.
2. `Fetch Wearable Sleep Data` pulls raw sleep, heart rate, and movement data for the current day via the wearable's API.
3. `Preprocess Sleep Data` consolidates and synchronizes this data into an analysis-ready format.
4. `Analyze Sleep Patterns and Recommend Improvements` uses GPT-4 to analyze and interpret sleep quality, returning customized advice.
5. `Determine Optimal Wake Up Time` identifies the best wake-up time aligned with sleep cycles, enhancing morning alertness and restfulness.
6. `Send Smart Alarm Notification` delivers the optimal wake-up time and tailored sleep improvement tips to the user's email.

---

## Prerequisites

- OAuth2 credentials for the wearable health device API.
- Access to OpenAI API with GPT-4 model capability.
- Notification service configured to send push/emails.
- User must have a compatible wearable device collecting sleep, heart rate, and movement data.

---

## Customize and Extend

- Adjust the data range or add more biometric data types as supported by the wearable API.
- Modify AI prompt to enhance or tailor sleep analysis depth.
- Enhance wake-up time logic with actual sleep cycle modeling or machine learning.
- Integrate additional user notification channels (SMS, mobile push apps).

---

This workflow empowers users with actionable insights derived from their own biometric data to improve sleep quality and wake naturally feeling refreshed.