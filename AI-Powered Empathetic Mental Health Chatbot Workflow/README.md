# AI-Driven Mental Health Support Chatbot Integration

## Overview

This workflow provides an AI-powered mental health support chatbot integration designed to receive user messages, analyze their sentiment, generate personalized empathetic coping strategies, and respond back to the user via a specified reply URL. It leverages OpenAI's GPT-4o-mini model for sentiment analysis and generating coping suggestions.

---

## Workflow Structure

### 1. Webhook

- **Node Name:** Webhook  
- **Type:** `n8n-nodes-base.webhook`  
- **Function:** Acts as an API endpoint to receive incoming POST requests containing user messages.  
- **Endpoint Path:** `/chat`  
- **Response Behavior:** Responds immediately upon receiving the request (responseMode: `onReceived`).  
- **Webhook ID:** `ai-mental-health-chat-webhook`  
- **Input Data Example:**  
  ```json
  {
    "message": "I'm feeling overwhelmed with work lately.",
    "replyUrl": "https://example.com/reply-endpoint"
  }
  ```
  
---

### 2. Sentiment Analysis

- **Node Name:** Sentiment Analysis  
- **Type:** `n8n-nodes-base.openAi`  
- **Function:** Uses OpenAI's GPT-4o-mini model to perform sentiment analysis on the incoming message.  
- **Input:** The extracted `message` from the webhook.  
- **Prompt:**  
  ```
  Analyze the sentiment of the following message. Respond ONLY with one word describing the sentiment: Positive, Neutral, Negative.

  Message: <user_message>
  ```
- **Model:** GPT-4o-mini  
- **Response:** One word (`Positive`, `Neutral`, or `Negative`) indicating the sentiment of the message.  
- **Settings:** maxTokens: 5, temperature: 0 (deterministic output), topP: 1  

---

### 3. Generate Coping Strategy

- **Node Name:** Generate Coping Strategy  
- **Type:** `n8n-nodes-base.openAi`  
- **Function:** Generates a concise, empathetic, personalized coping strategy or mental health resource suggestion based on the message sentiment.  
- **Input:**  
  - The original user message.  
  - The sentiment result from the Sentiment Analysis node.  
- **Prompt:**  
  ```
  <user_message>
  Sentiment: <sentiment_result>

  Based on the sentiment, suggest a concise, empathetic personalized coping strategy or mental health resource with a gentle tone. Return only the suggestion as plain text.
  ```  
- **Model:** GPT-4o-mini  
- **Settings:** maxTokens: 150, temperature: 0.7 (creative yet controlled output), topP: 1  

---

### 4. Send Reply

- **Node Name:** Send Reply  
- **Type:** `n8n-nodes-base.httpRequest`  
- **Function:** Sends the generated coping strategy back to the caller via the `replyUrl` provided in the initial webhook request.  
- **HTTP Method:** POST  
- **Request URL:** Extracted from incoming data field `replyUrl`.  
- **Content Type:** JSON  
- **Request Body Format:**  
  ```json
  {
    "reply": "<generated_coping_strategy_text>"
  }
  ```  

---

## Workflow Execution Flow

1. **Webhook Node** receives a POST request containing the user's message and a `replyUrl`.  
2. The message is forwarded to **Sentiment Analysis**, which determines the overall sentiment.  
3. The sentiment and original message are used by **Generate Coping Strategy** to create a personalized, empathetic response.  
4. The generated response is sent back to the client asynchronously by the **Send Reply** HTTP request node to the specified `replyUrl`.

---

## Prerequisites

- An [n8n](https://n8n.io/) instance to run the workflow.  
- OpenAI API credentials with access to GPT-4o-mini or equivalent model.  
- The service or client must be capable of POSTing to the `/chat` webhook endpoint with a JSON body including `message` and `replyUrl`.

---

## Usage

1. Deploy the workflow in your n8n instance.  
2. Configure your environment with valid OpenAI API credentials under the credential named `OpenAI API`.  
3. Start the workflow (set active).  
4. Send a POST request to `/chat` with JSON payload:  
   ```json
   {
     "message": "Your mental health message here",
     "replyUrl": "https://your-callback-url.com/receive-reply"
   }
   ```  
5. The workflow analyzes the sentiment and sends an empathetic coping strategy suggestion to the `replyUrl`.

---

## Notes

- The workflow response to the initial webhook is immediate to acknowledge receipt but the detailed reply is sent asynchronously via the `replyUrl`.  
- Ensure your receiving endpoint (`replyUrl`) can handle POST requests with JSON body containing the `reply` field.  
- Adjust OpenAI parameters (model, temperature, maxTokens) as needed for desired behavior.  

---

## License

This workflow is provided as-is for educational and integration purposes. Ensure compliance with OpenAI's usage policies when deploying.