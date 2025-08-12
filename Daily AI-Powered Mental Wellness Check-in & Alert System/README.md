# Automated Daily Mental Wellness Check-in & Personalized Support System

## Overview

This workflow automates a daily mental wellness check-in process using SMS, AI-powered analysis, and personalized support. It engages users daily, collects their mental health status through SMS responses, analyzes the data with an AI assistant, and delivers tailored feedback. Critical cases trigger immediate alerts to a support team via email.

---

## Workflow Components

### 1. Daily Trigger

- **Type:** Cron
- **Description:** Initiates the workflow every day at 9:00 AM.
- **Purpose:** Automates the daily start of the wellness check-in sequence.

### 2. Send Check-in Message

- **Type:** Twilio (POST)
- **API Endpoint:** `/send-checkin-message`
- **Description:** Sends an SMS check-in message to the user asking them about their current mental wellness status.
- **Credentials Required:** Twilio API Credentials

### 3. Receive User Response

- **Type:** Webhook (POST)
- **Path:** `/receive-checkin-response`
- **Description:** Receives the user's SMS response to the check-in.
- **Response Mode:** Returns "Sent" after receiving the message confirming delivery.
- **Purpose:** Captures real-time user input for analysis.

### 4. Extract User Response

- **Type:** Function
- **Code:**
  ```javascript
  const responseText = items[0].json.body || items[0].json.Body || '';
  return [{ json: { text: responseText } }];
  ```
- **Description:** Extracts the textual content from the user’s SMS response for further processing.

### 5. Ensure Phone Number

- **Type:** Function
- **Code:**
  ```javascript
  return [{ json: { phoneNumber: $json.phoneNumber || '1234567890', ...$json } }];
  ```
- **Description:** Ensures there is a phone number attached to the data payload. Defaults to a placeholder if not present.
- **Purpose:** Guarantees smooth message delivery for personalized responses.

### 6. Analyze Response with AI

- **Type:** OpenAI Completion (GPT-4)
- **Model:** GPT-4
- **System Prompt:** 
  > You are an empathetic mental wellness assistant. Analyze the daily user response to assess mood, identify trends and flag any potential mental health concerns or risks.
- **User Prompt Template:**
  ```
  Daily mental wellness check-in response: {{ $json.text }}

  Please analyze the mood, identify if there are signs of distress, anxiety, depression, or any critical alerts, and provide a summary with recommended personalized support resources or suggestions.
  ```
- **Description:** Uses AI to interpret the user's message, detect mood trends, mental health indications, and recommend support suggestions.
- **Credentials Required:** OpenAI API Credentials

### 7. Process AI Analysis

- **Type:** Function
- **Code:**
  ```javascript
  const analysis = items[0].json.choices[0].message.content || '';

  const criticalKeywords = ['suicide', 'self-harm', 'danger', 'emergency', 'crisis', 'harm to self', 'help immediately'];

  const isCritical = criticalKeywords.some(keyword => analysis.toLowerCase().includes(keyword));

  return [{ json: { analysis, isCritical } }];
  ```
- **Description:** Extracts the AI’s textual analysis and scans for critical keywords indicating emergency or serious distress.

### 8. Filter Critical Cases

- **Type:** Function
- **Code:**
  ```javascript
  if (items[0].json.isCritical) {
    return items;
  } else {
    return null;
  }
  ```
- **Description:** Filters workflow execution to trigger only if the analysis contains critical alerts requiring immediate attention.

### 9. Send Critical Alert Email

- **Type:** Email Send
- **Email To:** supportteam@example.com
- **Subject:** CRITICAL Mental Wellness Alert for User
- **Body:**
  ```
  Critical alert detected in daily mental wellness check-in:

  {{$json.analysis}}
  ```
- **Description:** Sends an email notification with the critical AI analysis summary to the support team.
- **Credentials Required:** SMTP Credentials

### 10. Send Personalized Support SMS

- **Type:** Twilio - sendMessage
- **Message Template:**
  ```
  Thank you for your response. Based on your check-in:

  {{$json.analysis}}

  Please consider the provided suggestions and feel free to reach out anytime.
  ```
- **Description:** Sends the user a personalized SMS message with AI-generated feedback and support resource suggestions based on their check-in.
- **Credentials Required:** Twilio API Credentials

---

## Workflow Execution Flow

1. **Daily Trigger:** Starts the workflow daily at 9:00 AM.
2. **Send Check-in Message:** Sends an SMS asking the user for their mental wellness update.
3. **Receive User Response:** Collects the user’s SMS reply.
4. **Extract User Response:** Parses the received message text.
5. **Ensure Phone Number:** Verifies phone number availability.
6. **Analyze Response with AI:** Sends the user’s response to AI for compassionate analysis.
7. **Process AI Analysis:** Assesses the AI output and detects critical concern keywords.
8. **Filter Critical Cases:** If critical keywords detected, triggers an alert.
9. **Send Critical Alert Email:** Notifies support team for urgent intervention.
10. **Send Personalized Support SMS:** Sends tailored feedback and resources back to the user.

---

## Prerequisites

- **Twilio Account:** For sending and receiving SMS messages.
- **OpenAI API Access:** For AI-powered response analysis using GPT-4.
- **SMTP Server:** For sending email alerts to the mental wellness support team.
- **User Phone Numbers:** Must be stored and accessible via the workflow for SMS communication.

---

## Security & Privacy Considerations

- Ensure all API credentials (Twilio, OpenAI, SMTP) are securely stored within the workflow environment.
- Personal data must be handled with strict confidentiality and compliance with relevant data privacy laws.
- Critical alerts trigger immediate human review to safeguard users’ safety.

---

## Customization

- **Trigger Time:** Modify the cron node parameters to change daily check-in time.
- **Support Team Email:** Update the recipient email address to current support contact.
- **Critical Keywords:** Extend or adjust the list of keywords for customized risk detection sensitivity.
- **SMS Message Content:** Tailor the check-in prompt and support messages to suit the audience tone and language.

---

## License

This workflow and its components are provided as-is for informational and development purposes. Use responsibly and consult mental health professionals for support program implementation.