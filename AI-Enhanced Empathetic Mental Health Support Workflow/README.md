# AI-Powered Mental Health Chatbot Workflow

## Overview

This workflow provides an AI-powered mental health chatbot designed to engage users empathetically, analyze their emotional state, and offer personalized follow-up recommendations, including scheduling professional sessions via Google Calendar.

---

## Workflow Nodes and Description

### 1. **Webhook**
- **Type:** HTTP POST Endpoint
- **Path:** `/chat`
- **Purpose:** Receives incoming chat requests from users, containing their message and optionally preferences or mental health conditions. Acts as the starting point for the workflow.

### 2. **Parse User Preferences**
- **Type:** Function
- **Purpose:** Extracts and merges optional user-specific information from the webhook payload such as:
  - `userPreferences` (e.g., session preferences, topics of interest)
  - `mentalHealthConditions` (user's known or self-reported conditions)
- **Output:** Attaches this data back to the workflow JSON for subsequent nodes.

### 3. **AI Chatbot**
- **Type:** HTTP Request (OpenAI API)
- **Purpose:** Sends the user's message to OpenAI GPT-4 model with a system prompt to act as an empathetic mental health assistant.
- **Request Body:**
  ```json
  {
    "model": "gpt-4",
    "messages": [
      {
        "role": "system",
        "content": "You are an empathetic mental health assistant. Provide compassionate responses based on user input."
      },
      {
        "role": "user",
        "content": "<user_message>"
      }
    ],
    "temperature": 0.7
  }
  ```
- **Authentication:** Requires an OpenAI API key provided via HTTP Basic Auth credentials.

### 4. **Sentiment Analysis**
- **Type:** HTTP Request (MeaningCloud API)
- **Purpose:** Analyzes the sentiment of the user's message to identify emotional tone (positive, negative, or neutral).
- **Parameters:**
  - `key`: Your MeaningCloud API Key
  - `txt`: The user's original message
  - `lang`: `"en"` (English language)
- **Note:** Replace `"YOUR_MEANINGCLOUD_API_KEY"` with your actual API key.

### 5. **Process Sentiment**
- **Type:** Function
- **Purpose:** Parses the sentiment API response and determines:
  - Sentiment score (`P+`, `P`, `N`, `N+`, or neutral)
  - Confidence level of sentiment detection
  - Simplified emotion category (`positive`, `negative`, or `neutral`)
  - Preserves the original user message for reference

### 6. **Generate Recommendations**
- **Type:** Function
- **Purpose:** Creates tailored recommendations based on detected emotion and user preferences:
  - If **negative** emotion: Suggests professional counseling and either booking sessions or self-help resources depending on user preferences.
  - If **positive** emotion: Encourages maintaining positive habits.
  - If **neutral**: Offers general supportive message reminders.
- **Input:** Emotion, user preferences, and known mental health conditions.
- **Output:** Array of personalized recommendations.

### 7. **Schedule Follow-up**
- **Type:** Google Calendar Node
- **Purpose:** Automatically schedules a one-hour follow-up session on the user's primary Google Calendar if applicable.
- **Authentication:** Requires OAuth2 credentials for Google API.
- **Event Details:**
  - Summary: "Mental health follow-up session"
  - Description: "Scheduled follow-up session based on your conversation with the mental health assistant."
  - Start: Current time (ISO string)
  - End: One hour after the start time

### 8. **Prepare Response**
- **Type:** Function
- **Purpose:** Gathers and sanitizes information for the response to the user:
  - AI chatbot's empathetic reply
  - Detected emotion
  - Personalized recommendations
- **Security:** Strips any sensitive or irrelevant fields before responding.

### 9. **Respond**
- **Type:** No Operation (NoOp)
- **Purpose:** Sends the prepared final response from the last node back to the original HTTP webhook response.

---

## Data Flow Summary

1. User sends a POST request with a message (and optionally preferences/conditions) to `/chat`.
2. The webhook triggers and user preferences are parsed.
3. The message goes in parallel to:
   - AI Chatbot (OpenAI GPT-4) for empathetic response generation.
   - Sentiment Analysis (MeaningCloud) for emotional tone detection.
4. Sentiment is processed and simplified into `positive`, `negative`, or `neutral`.
5. Based on the emotion and user preferences, personalized recommendations are generated.
6. If necessary, a follow-up session is scheduled on Google Calendar.
7. AI response, sentiment emotion, and recommendations are merged and prepared for reply.
8. Response is sent back via the webhook.

---

## Setup Instructions

1. **OpenAI API Key**
   - Acquire your API key from OpenAI.
   - Add the API key to the `AI Chatbot` node credentials (`HTTP Basic Auth`).

2. **MeaningCloud API Key**
   - Sign up at MeaningCloud and get an API key.
   - Replace the `"YOUR_MEANINGCLOUD_API_KEY"` string in the `Sentiment Analysis` node with your key.

3. **Google Calendar OAuth2 Setup**
   - Create Google API credentials with Calendar access.
   - Set up OAuth2 credentials in n8n and assign to the `Schedule Follow-up` node.

4. **Webhook Integration**
   - Deploy this workflow in your n8n instance.
   - Ensure the webhook endpoint (`/chat`) is publicly accessible or integrated with your frontend/chat interface.

---

## Expected Input JSON Structure (POST Request to `/chat`)

```json
{
  "message": "I have been feeling anxious lately.",
  "preferences": {
    "preferSessions": true,
    "topics": ["anxiety", "stress"]
  },
  "conditions": ["generalized anxiety disorder"]
}
```

- `message` (string): User's text input.
- `preferences` (object, optional): User preferences for interaction style or session booking.
- `conditions` (array, optional): Known mental health conditions for better recommendation tailoring.

---

## Expected Output JSON Structure

```json
{
  "reply": "<empathetic AI-generated response>",
  "emotion": "negative",
  "recommendations": [
    "Consider scheduling a professional counseling session.",
    "Available slots for next week are open. Would you like to book a session?"
  ]
}
```

- `reply`: AI chatbot's empathetic response.
- `emotion`: Detected emotional tone (`positive`, `negative`, or `neutral`).
- `recommendations`: Personalized follow-up suggestions based on sentiment and user preferences.

---

## Notes

- Ensure API keys and OAuth tokens are securely stored in n8n credentials.
- The schedule follow-up will default to current time and next hour; customize times as needed.
- This workflow respects user privacy by not storing sensitive data beyond runtime.
- Adapt language and system prompts as necessary to fit different use cases or languages.

---

This comprehensive workflow empowers mental health support by blending AI-driven empathy with actionable follow-ups.