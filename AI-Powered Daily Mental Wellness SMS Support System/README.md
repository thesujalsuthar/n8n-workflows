# AI-Driven Automated Daily Mental Wellness Check-in & Personalized Support System

This workflow automates a daily mental wellness check-in via SMS, analyzes user responses using AI-powered sentiment analysis, and provides personalized coping strategies or escalates alerts if needed. It leverages Twilio for SMS communication and OpenAI for natural language processing and sentiment evaluation.

---

## Workflow Overview

1. **Daily Trigger**  
   The workflow starts every day at 9:00 AM, triggering the mental wellness check-in process.

2. **Send Check-in SMS**  
   Sends an SMS message to the user prompting them to reply with how they are feeling in a few sentences.

3. **Wait for User Reply**  
   The system waits to receive an SMS reply from the user.

4. **Extract User Message**  
   Extracts the text content from the user's SMS reply for processing.

5. **AI Sentiment Analysis**  
   Uses OpenAI's GPT-4o-mini model to perform sentiment analysis on the user's message. The response includes:
   - `sentiment`: one of "positive", "neutral", or "negative"
   - `emotion_score`: a float between 0.0 and 1.0 indicating intensity

6. **Parse Sentiment JSON**  
   Parses the JSON response from the AI model to extract sentiment values.

7. **Determine Coping Strategy & Escalation**  
   Based on the sentiment and emotion score:
   - If sentiment is negative and emotion score ≥ 0.6, a supportive coping strategy is issued and escalation is triggered.
   - If sentiment is negative but emotion score < 0.6, a mild coping strategy is sent.
   - Neutral sentiment results in encouragement to maintain routines.
   - Positive sentiment receives positive reinforcement.

8. **Send Coping Strategy SMS**  
   Sends the personalized coping strategy SMS back to the user.

9. **Check if Escalation Needed**  
   If escalation is required, the workflow proceeds to notify an emergency contact.

10. **Send Escalation Alert SMS**  
    Sends an alert SMS to the designated emergency contact to notify them of the user's high-risk emotional state for further support.

---

## Nodes Description

### 1. Daily Trigger
- **Type:** Schedule Trigger
- **Trigger Time:** 09:00 AM every day
- **Purpose:** Initiate the daily check-in process

### 2. Send Check-in SMS
- **Type:** HTTP Request (POST to Twilio API)
- **Message:**  
  "Hi! How are you feeling today? Reply with a few sentences about how you feel."
- **Credentials:** Twilio account SID, Auth Token
- **Phone Numbers:** User's phone number (`USER_PHONE_NUMBER`), Twilio phone number (`TWILIO_PHONE_NUMBER`)

### 3. Wait for User Reply
- **Type:** Wait Node (listening for incoming SMS)
- **Filter:** Incoming messages must be from the user's phone number

### 4. Extract User Message
- **Type:** Function Node
- **Purpose:** Extract message body content from incoming SMS JSON payload

### 5. AI Sentiment Analysis
- **Type:** OpenAI Node (GPT-4o-mini)
- **Prompt:** Instructs model to analyze sentiment of the user message and return JSON containing sentiment category and emotion score.

### 6. Parse Sentiment JSON
- **Type:** Function Node
- **Purpose:** Parse the AI response and separate `sentiment` and `emotion_score` fields for downstream use

### 7. Determine Coping Strategy & Escalation
- **Type:** Function Node
- **Logic:** Assigns a personalized coping strategy text and sets an escalation flag if needed based on the sentiment results.

### 8. Send Coping Strategy SMS
- **Type:** HTTP Request (POST to Twilio API)
- **Message:** Sends the personalized support message determined in the previous step to the user.

### 9. Check if Escalation Needed
- **Type:** Function Node
- **Logic:** Passes the message forward only if escalation is required.

### 10. Send Escalation Alert SMS
- **Type:** HTTP Request (POST to Twilio API)
- **Message:** Alerts the emergency contact of the user's negative mental wellness with a high emotion score, recommending follow-up support.

---

## Environment Variables / Credentials

- `TWILIO_ACCOUNT_SID`: Your Twilio account SID.
- `TWILIO_AUTH_TOKEN`: Your Twilio Auth token (used via HTTP Basic Auth).
- `TWILIO_PHONE_NUMBER`: Twilio phone number sending SMS.
- `USER_PHONE_NUMBER`: User’s phone number receiving messages.
- `EMERGENCY_CONTACT_PHONE`: Phone number for escalation alerts.
- OpenAI API key configured with the OpenAI Node.

---

## How it Works

- Each day at 9 AM, the system sends a friendly mental wellness check-in SMS.
- The user replies with their current feelings.
- The workflow captures and analyzes the reply using AI, assigning a sentiment and emotion intensity score.
- A personalized supportive message is sent based on that analysis.
- If the user indicates significant distress (negative sentiment with high emotion score), an alert SMS is sent to a designated emergency contact to provide timely support.

---

## Setup Instructions

1. **Configure Credentials:**  
   Add Twilio API credentials (Account SID, Auth Token) to n8n. Also, setup OpenAI credentials.

2. **Set Environment Variables:**  
   Define phone numbers via environment variables in n8n or workflow variables:
   - `USER_PHONE_NUMBER`
   - `TWILIO_PHONE_NUMBER`
   - `EMERGENCY_CONTACT_PHONE`

3. **Import Workflow:**  
   Import this workflow JSON into your n8n instance.

4. **Activate Workflow:**  
   Make sure the workflow is active to automate the daily check-ins.

5. **Testing:**  
   Test the workflow by manually triggering or waiting for the scheduled time to ensure SMS messages are sent, received, processed, and follow-up messages or escalation alerts work accordingly.

---

## Notes

- The sentiment analysis model can be adjusted or replaced depending on available AI models and desired accuracy.
- Coping strategies can be customized inside the “Determine Coping Strategy & Escalation” node.
- Emergency escalation threshold (emotion score ≥ 0.6) can be fine-tuned as needed.
- Ensure compliance with privacy laws and obtain user consent before collecting and analyzing mental health data.

---

This automated system aims to promote daily mental wellness awareness and provide timely support while leveraging AI and SMS for seamless communication.