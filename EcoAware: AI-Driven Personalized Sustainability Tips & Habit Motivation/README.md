# EcoAware: Personalized Sustainability Tips & Habit Tracker

## Overview

EcoAware is an automated workflow designed to provide users with personalized daily sustainability tips and track their eco-friendly habit progress. It leverages AI to generate customized tips and motivational messages based on user preferences and habit updates. The workflow is powered by n8n and integrates MongoDB for habit data storage and OpenAI for generating AI-driven content.

---

## Workflow Components

### 1. Webhook Trigger - Get Daily Tips
- **Type:** Webhook
- **Path:** `/getDailyTips`
- **Purpose:** Entry point for requests to obtain daily personalized sustainability tips.
- **Input:** Receives user data including preferences.

### 2. Extract Preferences
- **Type:** Function
- **Purpose:** Extracts `preferences` from the incoming webhook JSON payload.
- **Output:** JSON object containing user preferences to be used by AI.

### 3. AI - Generate Sustainability Tips
- **Type:** OpenAI Node (`gpt-4o-mini` model)
- **Purpose:** Uses user preferences to generate a concise list of 3 personalized sustainability tips.
- **Input:** User preferences passed as prompt context.
- **Output:** JSON array with the key `"tips"` containing tips.

### 4. Parse AI Tips
- **Type:** Function
- **Purpose:** Parses the AI response to extract the `"tips"` array.
- **Output:** Clean JSON with tips array for downstream consumption.

### 5. Respond with Tips
- **Type:** Function
- **Purpose:** Prepares the tips for response in the webhook reply.
- **Output:** JSON including the list of tips.

### 6. Respond to Get Daily Tips
- **Type:** Respond to Webhook
- **Purpose:** Sends the generated tips back to the caller with HTTP 200 status.

---

### 7. Webhook Trigger - Update Habit
- **Type:** Webhook
- **Path:** `/updateHabit`
- **Purpose:** Entry point for incoming habit progress updates.
- **Input:** Habit progress data including `userId` and `habitId`.

### 8. Extract Habit Update Data
- **Type:** Function
- **Purpose:** Extracts habit data from the incoming webhook payload.
- **Output:** JSON object with habit update details.

### 9. Database - Upsert Habit Progress
- **Type:** MongoDB Node
- **Operation:** Upsert (update or insert)
- **Collection:** `ecoAwareHabits`
- **Purpose:** Inserts or updates the user's habit progress in the database.
- **Query:** Matches documents by `userId` and `habitId`.
- **Credentials:** Connected via `EcoAware MongoDB`.

### 10. Database - Get User Habit Progress
- **Type:** MongoDB Node
- **Operation:** Find
- **Collection:** `ecoAwareHabits`
- **Purpose:** Retrieves all habit progress data for the user.
- **Query:** By `userId`.
- **Credentials:** Connected via `EcoAware MongoDB`.

### 11. AI - Generate Motivational Message
- **Type:** OpenAI Node (`gpt-4o-mini` model)
- **Purpose:** Generates a personalized motivational message based on the user's current habit progress.
- **Input:** Habit progress data as prompt context.
- **Output:** JSON object with the key `"message"`.

### 12. Parse Motivational Message
- **Type:** Function
- **Purpose:** Extracts the motivational message from the AI response.
- **Output:** JSON object containing the motivational message.

### 13. Respond with Motivation
- **Type:** Function
- **Purpose:** Prepares the motivational message for response in the webhook reply.
- **Output:** JSON including the motivational message.

### 14. Respond to Update Habit
- **Type:** Respond to Webhook
- **Purpose:** Sends the motivational message back to the caller with HTTP 200 status.

---

## How It Works

### Getting Daily Sustainability Tips

1. A request made to the `/getDailyTips` webhook with user preferences triggers the workflow.
2. Preferences are extracted and sent to the AI node.
3. AI generates 3 personalized daily sustainability tips based on preferences.
4. The tips are parsed and sent back in the webhook response.

### Updating Habit Progress & Receiving Motivation

1. A user's habit progress update is sent to the `/updateHabit` webhook.
2. Habit data is extracted and upserted (inserted or updated) in the MongoDB collection.
3. All habit progress data for the user is retrieved.
4. AI generates a motivational message based on current habit progress.
5. The motivational message is parsed and responded back to the user via webhook.

---

## Requirements

- **n8n:** To run and manage the workflow.
- **MongoDB:** For storing user habit progress (Credential: `EcoAware MongoDB`).
- **OpenAI API Key:** To use GPT-4o-mini for generating tips and motivational messages.
- **Webhook Endpoints:**
  - `/getDailyTips` - For fetching daily personalized tips.
  - `/updateHabit` - For updating habit progress and getting motivation.

---

## Setup Instructions

1. **Import Workflow:** Import the provided JSON workflow into n8n.
2. **Configure Credentials:**
   - Set up MongoDB credentials named `EcoAware MongoDB`.
   - Set up OpenAI credentials with API key access.
3. **Expose Webhook Endpoints:** Ensure `/getDailyTips` and `/updateHabit` are publicly accessible.
4. **Invoke Endpoints:** Call the endpoints with appropriate JSON payloads:
   - `/getDailyTips`: Should include user preferences in JSON.
   - `/updateHabit`: Should include `userId`, `habitId`, and progress data.
5. **Receive Responses:** The workflow will return JSON-formatted tips or motivational messages.

---

## Example Payloads

### /getDailyTips

```json
{
  "preferences": {
    "focusAreas": ["reduce plastic", "energy saving"],
    "lifestyle": "vegetarian",
    "timeAvailable": "5 minutes"
  }
}
```

### /updateHabit

```json
{
  "userId": "user123",
  "habitId": "habit456",
  "progress": {
    "date": "2024-06-15",
    "completed": true,
    "notes": "Used reusable bags today"
  }
}
```

---

## Summary

EcoAware workflow integrates personalized AI content generation and user habit tracking into an automated system. By using this, users receive daily tailored sustainability advice and motivational feedback based on their ongoing eco-friendly actions, encouraging behavioral change towards a greener lifestyle.