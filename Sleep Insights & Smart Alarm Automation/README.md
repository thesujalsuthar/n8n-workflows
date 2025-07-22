# Sleep Data Analysis and Smart Alarm Workflow

## Overview

This workflow enables the automated analysis of user sleep data and provides personalized sleep hygiene recommendations using AI. It further stores these recommendations in Notion and integrates with a smart alarm system to set optimized wake-up times.

---

## Workflow Steps

### 1. Receive Sleep Data

- **Node:** `Receive Sleep Data`
- **Type:** HTTP Trigger
- **Description:** This node exposes an HTTP POST endpoint `/submit-sleep-data` to receive user sleep data submissions in JSON format.
- **Configuration:**
  - HTTP Method: POST
  - Path: `submit-sleep-data`
  - Response Mode: Respond based on the last node output

---

### 2. Parse Sleep Data

- **Node:** `Parse Sleep Data`
- **Type:** Function
- **Description:** Extracts and formats incoming JSON sleep data for further processing.
- **Code:**
  ```javascript
  const sleepData = $json;
  return [{ json: { sleepData } }];
  ```

---

### 3. AI Analyze Sleep Data

- **Node:** `AI Analyze Sleep Data`
- **Type:** OpenAI
- **Description:** Sends the sleep data to an AI model (GPT-4) to analyze and generate personalized sleep hygiene advice.
- **Configuration:**
  - Model: GPT-4
  - Temperature: 0.7 (balanced creativity)
  - Messages:
    - System: Acts as an AI sleep specialist.
    - User: Provides the JSON stringified sleep data in the prompt.
- **Prompt Example:**
  ```
  You are an AI sleep specialist. Analyze the user sleep data provided and generate personalized sleep hygiene recommendations, including bedtime optimization and smart alarm integration tips.

  User sleep data:
  <sleep data JSON here>
  ```

---

### 4. Parse AI Recommendations

- **Node:** `Parse AI Recommendations`
- **Type:** Function
- **Description:** Attempts to parse the AI's recommendations as structured JSON. If parsing fails, saves the raw text.
- **Code:**
  ```javascript
  const recommendationText = $json.choices[0].message.content;

  let recommendations;
  try {
    recommendations = JSON.parse(recommendationText);
  } catch (e) {
    recommendations = { rawText: recommendationText };
  }

  return [{ json: { recommendations } }];
  ```

---

### 5. Store Recommendations

- **Node:** `Store Recommendations`
- **Type:** Notion
- **Description:** Saves the AI-generated recommendations as a new note in Notion for record-keeping and future reference.
- **Configuration:**
  - Resource: Document
  - Operation: Create
  - Additional Fields:
    - Text: Raw advice text or structured JSON stringified
    - Title: `Sleep Improvement Plan - <current-date>`
    - Type: Note
- **Credentials:** Requires configured Notion API credentials.

---

### 6. Extract Bedtime & Alarm Time

- **Node:** `Extract Bedtime & Alarm Time`
- **Type:** Function
- **Description:** Parses the recommendation text to extract specific recommended bedtime and smart alarm time if mentioned.
- **Code:**
  ```javascript
  const rec = $json.recommendations;

  const bedtimeMatch = rec.rawText ? rec.rawText.match(/recommended bedtime: (\d{1,2}:\d{2} (AM|PM))/i) : null;
  const alarmMatch = rec.rawText ? rec.rawText.match(/smart alarm time: (\d{1,2}:\d{2} (AM|PM))/i) : null;

  return [{ json: {
    bedtime: bedtimeMatch ? bedtimeMatch[1] : null,
    alarmTime: alarmMatch ? alarmMatch[1] : null
  } }];
  ```

---

### 7. Set Smart Alarm

- **Node:** `Set Smart Alarm`
- **Type:** HTTP Request
- **Description:** Sends a request to a smart alarm API to set the alarm based on the extracted smart alarm time.
- **Configuration:**
  - URL: `https://api.smartalarm.example.com/v1/setAlarm`
  - Method: POST
  - Body JSON:
    ```json
    {
      "alarmTime": "<extracted alarm time>",
      "userId": "defaultUserId"
    }
    ```
- **Credentials:** Requires HTTP header authentication with the Smart Alarm API key.

---

## Requirements

- **n8n Workflow Automation Platform**
- **OpenAI API Key** with access to GPT-4
- **Notion API credentials** for storing notes
- **Smart Alarm API credentials**

---

## Usage

1. Deploy this workflow in your n8n instance.
2. Ensure all external service credentials (OpenAI, Notion, Smart Alarm API) are configured properly.
3. Send a POST request to `/submit-sleep-data` with user sleep data JSON payload.
4. The workflow will analyze the data, generate and store recommendations, and set a smart alarm accordingly.

---

## Example Sleep Data Payload

```json
{
  "userId": "user123",
  "date": "2024-06-10",
  "sleepDuration": 7.5,
  "sleepQuality": "good",
  "bedtime": "10:45 PM",
  "wakeTime": "6:15 AM",
  "interruptions": 1
}
```

---

## Notes

- The AI response format is flexible; the parsing function attempts to handle both JSON and plain text.
- Extracted times for bedtime and alarm rely on recognized phrases in the AI's response. Adjust regular expressions if needed.
- The smart alarm API endpoint is a placeholder and should be replaced with your actual service URL.

---

# End of Documentation