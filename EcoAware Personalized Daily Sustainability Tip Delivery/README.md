# EcoAware Daily Sustainability Tip Workflow

This n8n workflow automates the delivery of personalized daily sustainability tips, motivation, and community engagement prompts based on user preferences and local weather data.

---

## Overview

The workflow runs once daily at 8:00 AM and performs the following steps:

1. **Trigger daily** at 8:00 AM.
2. **Extract user data** including preferences and location.
3. **Fetch local weather** information using the OpenWeatherMap API based on the user's location.
4. **Generate AI-powered personalized content** (daily sustainability tips, motivation, and community prompts) using an AI model.
5. **Parse the AI-generated response** for sending.
6. **Send an email** with the personalized content to the user.

---

## Nodes Description

### 1. Daily Trigger
- **Type:** Schedule Trigger
- **Function:** Activates the workflow every day at 8:00 AM.

### 2. Extract User Data
- **Type:** Function
- **Function:** Extracts `preferences` and `location` data from the incoming JSON payload for further processing.

### 3. Fetch Local Weather
- **Type:** HTTP Request
- **Function:** Retrieves current weather data from the OpenWeatherMap API.
- **Parameters:**
  - Latitude and longitude obtained dynamically from user location.
  - API key (replace `YOUR_OPENWEATHERMAP_API_KEY` with a valid key).
  - Units set to metric.

### 4. AI Generate Tips
- **Type:** HTTP Request
- **Function:** Sends user preferences and local weather data to a GPT-4 based AI service to generate:
  - A concise daily sustainability tip.
  - A motivational message to encourage sustainable habits.
  - A prompt for community engagement.
- **Parameters:**
  - Uses `gpt-4` model.
  - Messages include system instructions and user context.
- **Credentials:** Requires OpenAI API credentials (replace `"YOUR_OPENAI_CREDENTIALS_ID"`).

### 5. Parse AI Response
- **Type:** Function
- **Function:** Extracts the generated daily insight text from the AI response for the email.

### 6. Send Email Notification
- **Type:** Email Send
- **Function:** Sends the personalized daily sustainability tip email to the user.
- **Parameters:**
  - Sender name: EcoAware Bot
  - Recipient email: dynamically pulled from user data.
  - Subject: "Your Personalized Daily Sustainability Tip"
  - Email body: AI-generated content.
- **Credentials:** Requires SMTP credentials (replace `"YOUR_SMTP_CREDENTIALS_ID"`).

---

## Setup Instructions

1. **OpenWeatherMap API Key**
   - Sign up at [OpenWeatherMap](https://openweathermap.org/api) and obtain an API key.
   - Replace `YOUR_OPENWEATHERMAP_API_KEY` in the "Fetch Local Weather" node with your key.

2. **OpenAI API Credentials**
   - Obtain API credentials for OpenAI GPT-4.
   - Configure these in n8n and replace `YOUR_OPENAI_CREDENTIALS_ID` in the "AI Generate Tips" node.

3. **SMTP Email Credentials**
   - Set up your SMTP account in n8n.
   - Replace `YOUR_SMTP_CREDENTIALS_ID` in the "Send Email Notification" node.

4. **User Data Integration**
   - Ensure that the incoming data triggering this workflow includes:
     - `preferences` object (user sustainability preferences).
     - `location` object with `latitude` and `longitude`.
     - `email` address for sending notifications.

---

## Workflow Activation

- After configuring all credentials and verifying user data input, activate the workflow.
- The workflow will automatically run every day at 8:00 AM, sending personalized sustainability tips directly to users’ emails.

---

## Notes

- Customize the AI prompt in "AI Generate Tips" node as needed for tailored messages.
- Update recipient email extraction if your user data structure changes.
- Monitor API usage and quotas to ensure uninterrupted operation.