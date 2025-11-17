# Personalized AI-Powered Learning Scheduler

This workflow leverages AI to generate personalized learning schedules based on user input, automatically creates calendar events, and tracks progress in a Google Sheet.

---

## Overview

When a user submits their learning preferences and availability, this system:

1. Receives the user input via an HTTP POST request.
2. Sends the input to OpenAI's GPT-4 model to generate a tailored learning schedule and content recommendations.
3. Parses the AI response and creates corresponding events in the user's Google Calendar.
4. Logs scheduled tasks and progress into a Google Sheet for tracking.

---

## Nodes Description

### 1. Receive User Input

- **Type:** HTTP Trigger
- **Method:** POST
- **Path:** `/get-personalized-content`
- **Function:** Accepts user learning preferences, available time, and progress details as JSON payload to trigger the workflow.

---

### 2. Generate Personalized Content

- **Type:** HTTP Request
- **API:** OpenAI Chat Completions (`https://api.openai.com/v1/chat/completions`)
- **Model:** GPT-4
- **Purpose:** Sends user preferences to the AI learning assistant configured to:
  - Create personalized learning schedules.
  - Recommend suitable learning content.
  - Suggest optimized and adaptive learning paths based on input.
- **Input:** The user preferences received from the previous node.
- **Temperature:** 0.7 (Balances creativity and coherence)

**Authentication:** Uses OpenAI API key (configured via HTTP Basic Auth credentials).

---

### 3. Parse AI Response

- **Type:** Function
- **Purpose:** Parses the AI response content, which is expected in JSON format, extracting the schedule details into a usable format for subsequent nodes.
- **Error Handling:** Throws an error if the AI response is not valid JSON.

---

### 4. Create Calendar Events

- **Type:** Google Calendar
- **Operation:** Create events in the primary calendar.
- **Event Details:** Uses parsed AI JSON output for:
  - Summary (event title)
  - Description (event details)
  - Start and end dateTime with timeZone support (defaults to UTC if not specified)
- **Purpose:** Automatically adds personalized learning schedule events to the user's Google Calendar.

**Authentication:** Google Calendar OAuth2 credentials.

---

### 5. Track Progress in Sheet

- **Type:** Google Sheets
- **Operation:** Append row to the specified sheet and range (`Progress!A1:D1`)
- **Data Fields:**
  - Date — current date in `YYYY-MM-DD` format.
  - Task — event summary from AI output.
  - Status — set as `"Scheduled"`.
  - Notes — detailed description from AI output.
- **Purpose:** Maintains a log of all scheduled learning tasks and their status for progress tracking.

**Authentication:** Google Sheets OAuth2 credentials.

---

## Workflow Connections

- User input triggers the AI content generation.
- AI response is parsed into structured data.
- Parsed data branches into two parallel flows:
  - Creation of calendar events.
  - Appending scheduling data into Google Sheets.

---

## Setup Instructions

1. **OpenAI Credentials:**  
   Configure your OpenAI API key under HTTP Basic Auth credentials.

2. **Google API Credentials:**  
   Set up OAuth2 credentials for both Google Calendar and Google Sheets integrations.

3. **Google Sheets Preparation:**  
   Ensure a Google Sheet exists with ID `1A2B3C4D5E6F7G8H9I0J` and a tab named `Progress` with columns: Date, Task, Status, Notes.

4. **Deploy & Activate:**  
   Import this workflow in n8n, configure credentials, and activate it.

5. **Make Requests:**  
   POST user preferences JSON to `/get-personalized-content` endpoint to generate schedules.

---

## Example User Preferences Payload

```json
{
  "userPreferences": {
    "topics": ["Machine Learning", "Python", "Data Visualization"],
    "availableTimePerDay": 2,
    "learningStyle": "visual",
    "currentProgress": {
      "Machine Learning": "beginner",
      "Python": "intermediate"
    }
  }
}
```

---

# License

This workflow is provided as-is with no warranties. Use and adapt it freely.