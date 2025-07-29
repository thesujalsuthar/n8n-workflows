# AI-Powered Personalized Mental Health Sentiment & Emotion Analysis and Support System

This workflow provides an end-to-end solution for analyzing users' mental health-related text inputs using advanced AI, delivering empathetic responses, suggesting coping strategies, and managing follow-up support through calendar events and reminders.

---

## Workflow Overview

1. **User Input Reception**  
   Accepts POST requests containing user messages related to their mental health and optional contact details (email, phone, channel).

2. **Sentiment and Emotion Analysis**  
   Uses OpenAI's GPT-4 model to analyze the message for:
   - Sentiment: positive, neutral, or negative  
   - Emotions: e.g., sad, anxious, happy, angry, stressed, etc.  
   - Provides an empathetic response  
   - Suggests 2-3 coping strategies tailored to the detected emotional state  
   - Determines if a follow-up check-in is recommended  

3. **Response Parsing**  
   Extracts and parses the AI's JSON-formatted output into structured data fields.

4. **Notification Delivery**  
   Sends an email to the user with:  
   - The empathetic response  
   - Coping strategies formatted as a list  
   - A note about follow-up check-in if applicable  

5. **Follow-up Scheduling (Conditional)**  
   If a follow-up is recommended:  
   - Creates a Google Calendar event scheduled 3 days from the current date (30-minute duration) inviting the user via their email.  
   - Sends a gentle SMS reminder 3 days before the event via Twilio.

---

## Detailed Node Description

### 1. User Input Webhook
- **Type:** Webhook (HTTP POST)  
- **Endpoint:** `/user-input`  
- **Purpose:** Receives the user's mental health message and contact data (email, phone, channel).  
- **Response Mode:** Sends response immediately after input is received.

### 2. Analyze Sentiment and Emotions (OpenAI GPT-4)
- **Model:** GPT-4  
- **Prompt Setup:**  
  - System message sets the assistant's role as empathetic mental health support.  
  - User message is injected from webhook input message content.  
- **Expected Output:** JSON object containing the following keys:  
  - `sentiment`: "positive", "neutral", or "negative"  
  - `emotions`: Array of emotions (e.g., ["sad", "anxious"])  
  - `empatheticResponse`: An empathetic message addressing the user's feelings  
  - `copingStrategies`: Array of 2-3 coping strategy suggestions  
  - `followUpNeeded`: Boolean, indicating if a follow-up check-in is necessary.  
- **Parameters:** Temperature set to 0.7, max tokens to 500 to ensure creativity and ample response length.

### 3. Parse AI Response
- Extracts the AI's JSON string output and converts it into readable nodal variables for further processing:
  - `sentiment`  
  - `emotions` (stringified JSON array)  
  - `empatheticResponse`  
  - `copingStrategies` (stringified JSON array)  
  - `followUpNeeded` (boolean)

### 4. Send Empathetic Response Notification (Email)
- **Channel:** Defaults to email but determined by webhook input if specified  
- **Recipient:** Email pulled from user input fields (`email` or `userEmail`)  
- **Subject:** `"Mental Health Support: Your Recent Check-in"`  
- **Email Body:**  
  - The empathetic response from AI  
  - A formatted bullet list of suggested coping strategies  
  - Follow-up note if applicable ("We will schedule a follow-up check-in soon...")

### 5. Check If Follow-up Needed (If Condition)
- Checks whether `followUpNeeded` is `true` to trigger subsequent scheduling steps.

### 6. Schedule Follow-up Check-in (Google Calendar Event)
- Creates a calendar event 3 days from the current date/time.  
- Event details:  
  - Title: "Follow-up Mental Health Check-in"  
  - Description: Reminder focusing on well-being and mental health follow-up  
  - Attendees: The user's email from the webhook input  
- Duration: 30 minutes

### 7. Send SMS Reminder (Twilio)
- Sends an SMS reminder 3 days from now, timed with the calendar event.  
- Message: Gentle reminder about the upcoming mental health follow-up.  
- Recipient number extracted from the webhook input (`phone`).

---

## Input Format

Request must be a POST to `/user-input` with JSON body containing at least:

```json
{
  "message": "<user's mental health message>",
  "email": "<user's email address>",           // or "userEmail"
  "phone": "<user's phone number>",             // optional, for SMS
  "channel": "<preferred channel>"              // optional, defaults to email
}
```

---

## Output

- Immediate HTTP response from webhook confirming receipt.  
- User receives an email with empathetic support and coping strategies.  
- Conditional follow-up calendar event and SMS reminder if indicated by AI.

---

## Integration Requirements

- **OpenAI API:** Access to GPT-4  
- **Email Service:** Configured in n8n's email node for sending to user  
- **Google Calendar:** Google account linked with appropriate calendar permissions  
- **Twilio:** Account and API credentials for SMS sending  

---

## Notes

- The AI assistant is designed to be empathetic and supportive, but not a replacement for professional therapy.  
- Scheduling follow-ups ensures users receive gentle care and reminders when deeper support may be necessary.  
- Privacy and data security should be prioritized given sensitive information handling.

---

## Summary

This workflow automates personalized mental health support by analyzing user input, providing caring responses, recommending coping techniques, and managing follow-up interactions—all powered by GPT-4 and integrated communication services.