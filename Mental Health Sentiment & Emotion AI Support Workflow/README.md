# Mental Health Sentiment & Emotion Analysis Workflow

This n8n workflow analyzes user messages or voice inputs to detect sentiment and emotional state, and provides personalized mental health support recommendations via email and chat messages.

---

## Workflow Overview

The workflow processes input from chat messages or voice recordings, sends the data to an AI model for sentiment and emotion analysis, parses the AI response, evaluates the results, and delivers tailored recommendations through different communication channels based on the user's emotional state.

---

## Nodes Description

### 1. Chat Trigger

- **Type:** Webhook (n8n-nodes-base.webhook)
- **Event:** Listens to new chat messages (`message` event) on all channels.
- **Polling Interval:** 3000 ms
- **Webhook ID:** `your-chat-webhook-id`
- **Purpose:** Initiates the workflow when a new chat message is received.

### 2. Voice Input Trigger

- **Type:** Trigger (n8n-nodes-base.trigger)
- **Operation:** Starts recording voice input.
- **Encoding:** WAV
- **Duration:** 10 seconds
- **Purpose:** Captures voice input as another source of data for emotional analysis.

### 3. AI Sentiment & Emotion Analysis

- **Type:** OpenAI Node (n8n-nodes-base.openai)
- **Mode:** Text analysis
- **Model:** `gpt-4o-mini`
- **Input:** User message or transcript
- **Temperature:** 0.7
- **Max Tokens:** 500
- **Purpose:** Sends the input data to the AI model to analyze sentiment, emotion, and provide recommendations.

### 4. Parse AI Response

- **Type:** Function (n8n-nodes-base.function)
- **Function Code:** Parses the JSON response from the AI model.
- **Expected AI Output Structure:**
  ```json
  {
    "sentiment": "positive|neutral|negative",
    "emotions": ["anxiety", "sadness", "anger", ...],
    "recommendationType": "self_help|resource|professional_help",
    "recommendations": ["Take deep breaths", "Read article on mindfulness", "Contact counselor"],
    "confidence": 0.9
  }
  ```
- **Purpose:** Converts AI output string into a structured JSON object for further processing.

### 5. Check Negative Sentiment

- **Type:** If (n8n-nodes-base.if)
- **Condition:** Checks if `sentiment` equals `"negative"`.
- **Purpose:** Branches the workflow based on whether the message has a negative sentiment.

### 6. Check Professional Help Recommended

- **Type:** If (n8n-nodes-base.if)
- **Condition:** Checks if `recommendationType` equals `"professional_help"`.
- **Purpose:** Filters for recommendations that suggest professional assistance.

### 7. Send Email - Professional Help

- **Type:** Email Send (n8n-nodes-base.emailSend)
- **Recipient:** User email (`userEmail` or `email` from data)
- **Subject:** "Personalized Mental Health Support Recommendations"
- **Content:** Lists personalized recommendations highlighting professional help.
- **Purpose:** Sends an email when professional help is advised.

### 8. Send Email - Self Help/Resource

- **Type:** Email Send (n8n-nodes-base.emailSend)
- **Recipient:** User email
- **Subject:** "Personalized Coping Strategies & Resources"
- **Content:** Provides coping strategies and resources for self-help.
- **Purpose:** Sends an email for less critical cases suggesting self-help or resource materials.

### 9. Send Chat Message - General

- **Type:** Chat Message (n8n-nodes-base.chatMessage)
- **Recipient:** User chat ID (`userChatId` or `chatId`)
- **Message:** Provides suggestions and reminders to reach out if help is needed.
- **Purpose:** Delivers general recommendations via chat.

### 10. Send Chat Message - Professional Help

- **Type:** Chat Message
- **Recipient:** User chat ID
- **Message:** Suggests considering professional help with listed resources.
- **Purpose:** Provides urgent help recommendations via chat.

### 11. Set Message Routing

- **Type:** Function
- **Function Code:** Routes the data according to sentiment and recommendation type.
- **Purpose:** Determines the correct downstream path based on conditions.

---

## Workflow Connections

- Both **Chat Trigger** and **Voice Input Trigger** send data to **AI Sentiment & Emotion Analysis**.
- AI analysis results flow into **Parse AI Response**.
- Parsed data is checked for negative sentiment in **Check Negative Sentiment**:
  - If **negative**, passes through **Check Professional Help Recommended**.
    - If professional help is recommended:
      - Sends emails and chat messages recommending professional support.
    - Else:
      - Sends emails and chat messages suggesting self-help/resources.
  - If **not negative**, sends self-help/resources messages directly.
- Message routing is managed by **Set Message Routing** to ensure correct delivery.

---

## Configuration Notes

- Replace `"your-chat-webhook-id"` in **Chat Trigger** with your actual webhook ID.
- Ensure valid email addresses (`userEmail` or `email`) and chat IDs (`userChatId` or `chatId`) are available from incoming data.
- Set up SMTP credentials in n8n for **Email Send** nodes.
- Confirm access to OpenAI API and valid model (`gpt-4o-mini`) usage.
- Adjust voice recording device ID in **Voice Input Trigger** if applicable.

---

## Summary

This workflow provides an automated method to:

- Monitor chat and voice inputs in real time.
- Analyze user sentiment and emotions with an AI model.
- Parse and interpret AI results for actionable insights.
- Send personalized mental health support via email and chat.
- Differentiate between general support and urgent professional help recommendations.

It aims to enhance mental wellbeing by offering timely, tailored assistance based on users' emotional cues.