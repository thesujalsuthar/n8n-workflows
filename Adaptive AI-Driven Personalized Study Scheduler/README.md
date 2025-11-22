# Adaptive AI-Powered Personalized Study Planner

## Overview

This workflow creates a personalized study plan for users based on their learning style, current progress, and available study times. It uses AI (OpenAI GPT-4) to generate adaptive daily study sessions and reminders, ensuring efficient and motivating learning experiences. The workflow also sends personalized adaptive tips via Slack.

---

## Workflow Nodes and Details

### 1. Receive User Input

- **Type:** HTTP Trigger (POST)
- **Path:** `/user-input`
- **Purpose:** Entry point to receive user data including user ID, learning style, current progress, and available study times.

### 2. Parse User Data

- **Type:** Function
- **Description:** Extracts and structures the incoming JSON payload into:
  - `userId` (string)
  - `learningStyle` (defaulting to "visual" if missing)
  - `currentProgress` (object)
  - `availableTimes` (array)

### 3. Generate Study Plan with AI

- **Type:** OpenAI (GPT-4)
- **Parameters:**
  - Temperature: 0.7
  - Max tokens: 800
- **Prompt:** 
  - You are an expert educational AI.
  - Use the user's learning style, current progress, and available times.
  - Generate a fully customized study plan broken down by day and time.
  - Tailor study methods to the learning style.
  - Include adaptive tips for motivation and efficiency.
  - Output format (JSON):

    ```json
    {
      "dailyPlan": [
        {
          "day": "YYYY-MM-DD",
          "sessions": [
            {
              "time": "HH:MM",
              "subject": "string",
              "method": "string",
              "durationMinutes": number
            }
          ]
        }
      ],
      "adaptiveTips": ["string"]
    }
    ```

### 4. Parse AI Response

- **Type:** Function
- **Description:** Parses the AI-generated JSON response string into a JSON object for further processing.

### 5. Format Adaptive Tips

- **Type:** Function
- **Description:** Takes the `adaptiveTips` array from the AI response and formats it as a numbered list string.

### 6. Send Adaptive Tips

- **Type:** Slack
- **Description:** Sends the formatted adaptive tips to the Slack channel named after the user ID.

### 7. Split Sessions for Reminders

- **Type:** SplitInBatches
- **Description:** Splits the daily study sessions into individual items for scheduling separate reminders.
- **Output Fields per session:**
  - `subject`
  - `time`
  - `durationMinutes`
  - `method`
  - `day`
  - `userId`

### 8. Format Reminder DateTime

- **Type:** Function
- **Description:** Combines the `day` and `time` fields into an ISO 8601 datetime string format (`YYYY-MM-DDTHH:MM:SS`) to be used for scheduling reminders.

### 9. Schedule Reminder

- **Type:** Push Notification
- **Parameters:**
  - Title: `"Study Session Reminder: {{$json["subject"]}}"`
  - Text: `"Hi! It's time for your scheduled study session on {{$json["subject"]}} using the {{$json["method"]}} method for {{$json["durationMinutes"]}} minutes."`
  - User IDs: `{{$json["userId"]}}`
  - DateTime: `{{$json["reminderDateTime"]}}`
- **Description:** Sends a scheduled push notification reminder to the user for each study session.

### 10. Confirm Scheduling

- **Type:** Function
- **Description:** Provides a confirmation message `"All reminders scheduled successfully."` after all reminders are processed.

---

## Data Flow

1. **User Input** → HTTP POST request triggers the workflow.
2. **Parse User Data** → Extracts and structures input.
3. **Generate Study Plan with AI** → Requests a personalized plan from GPT-4.
4. **Parse AI Response** → Converts AI-generated strings into JSON.
5. **Format Adaptive Tips** → Prepares motivational tips for messaging.
6. **Send Adaptive Tips** → Sends tips via Slack.
7. **Split Sessions for Reminders** → Prepares individual study sessions for scheduling.
8. **Format Reminder DateTime** → Converts time info into proper datetime format.
9. **Schedule Reminder** → Sends push notification reminders for each session.
10. **Confirm Scheduling** → Confirmation message after scheduling complete.

---

## Input Data Format

Send a POST request to `/user-input` with JSON body:

```json
{
  "userId": "user123",
  "learningStyle": "auditory", // e.g., visual, auditory, kinesthetic
  "currentProgress": {
    // subject progress details
  },
  "availableTimes": [
    // array of preferred study times
  ]
}
```

---

## Output

- The AI returns a detailed study plan JSON.
- Scheduled reminders are created based on the plan.
- Adaptive tips are sent to the user via Slack.

---

## Requirements

- n8n workflow environment.
- OpenAI GPT-4 API credentials.
- Push notification service integration.
- Slack integration configured with user channels.

---

## Summary

This workflow leverages AI to tailor study plans to individual users, helping optimize learning effectiveness through timely reminders and motivational adaptive tips delivered via Slack. It automates the complete flow from user data reception to personalized session reminders and feedback.