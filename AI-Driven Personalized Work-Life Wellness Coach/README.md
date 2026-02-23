# AI-Powered Work-Life Balance Wellness Insights & Personalized Action Plan Generator

This workflow leverages user lifestyle data combined with external wellness data to generate daily personalized wellness insights, health & work-life balance tips, and an actionable 3-step plan using AI.

---

## Overview

The workflow receives user-submitted data via a webhook, enriches it with environmental wellness factors, analyzes the combined data, and then generates personalized coaching insights and recommendations using OpenAI’s GPT-4 model.

---

## Workflow Nodes and Details

### 1. Webhook - Collect User Input
- **Type:** Webhook (HTTP POST endpoint)
- **Path:** `/submitUserData`
- **Purpose:** Receives user lifestyle input data which should include:
  - `workHours` (number): Daily working hours
  - `stressLevel` (number, 1-10): Self-reported stress intensity
  - `sleepHours` (number): Average sleep hours per night
  - `exerciseFreq` (number): Weekly exercise frequency
  - `mood` (string): Current mood (e.g., happy, neutral, sad)

---

### 2. Process User Data
- **Type:** Function
- **Purpose:** Parses and normalizes incoming user data.
- **Details:**
  - Converts input values to appropriate types.
  - Provides default values if any data is missing:
    - workHours = 8
    - stressLevel = 5
    - sleepHours = 7
    - exerciseFreq = 3
    - mood = "neutral"
  - Outputs a structured `userProfile` JSON object for further use.

---

### 3. Fetch External Wellness Data
- **Type:** HTTP Request
- **Purpose:** Retrieves environmental wellness data from an external weather API.
- **Endpoint:** `https://api.open-meteo.com/v1/forecast?latitude=40.7128&longitude=-74.0060&daily=temperature_2m_max,precipitation_sum&timezone=auto`
- **Details:**
  - Fetches daily max temperature and precipitation forecast for New York City (latitude 40.7128, longitude -74.0060).
  - Enables consideration of environmental factors affecting wellness.

---

### 4. Analyze Wellness Data
- **Type:** Function
- **Purpose:** Combines user data with external environmental data to derive a wellness score.
- **Details:**
  - Extracts temperature and precipitation from the weather data.
  - Applies a heuristic to calculate `weatherScore`:
    - Score `8` if temperature > 20°C and precipitation < 5 mm (good weather conditions)
    - Otherwise, score `5` (moderate conditions)
  - Packages both `userProfile` and `weatherScore` as output.

---

### 5. Generate AI Wellness Insights
- **Type:** OpenAI GPT-4 (Chat Completion)
- **Purpose:** Uses AI to analyze user lifestyle and wellness score, then generate personalized insights.
- **Prompt System Message:**
  - AI acts as a wellness coach focusing on work-life balance.
  - Tasked with analyzing data and generating daily insights, 3 personalized tips, and a 3-step actionable plan.
- **User Message Template:**
  ```
  User data: {{ $json.user | json }}
  External wellness score: {{ $json.weatherScore }}

  Provide a personalized daily insight, 3 health & work-life balance tips, and a personalized 3-step action plan.
  ```
- **Model Settings:**
  - Model: gpt-4
  - Temperature: 0.7
  - Max tokens: 500

---

### 6. Prepare Response
- **Type:** Function
- **Purpose:** Formats AI-generated insights for the HTTP response.
- **Details:**
  - Returns status code 200.
  - Provides the AI insights content as JSON in the response body:
    ```json
    {
      "insights": "<AI generated content>"
    }
    ```

---

## How to Use

1. **Submit User Data:**  
   Send a POST request to `/submitUserData` with JSON containing:
   ```json
   {
     "workHours": 9,
     "stressLevel": 6,
     "sleepHours": 6,
     "exerciseFreq": 2,
     "mood": "happy"
   }
   ```
2. **Receive Response:**  
   The workflow responds with personalized wellness insights, tips, and a 3-step plan.

---

## Summary

This workflow harnesses real-time user data and environmental inputs to deliver AI-curated, actionable wellness guidance, helping individuals improve their work-life harmony day by day.