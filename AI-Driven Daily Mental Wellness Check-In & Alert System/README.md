# Daily AI-Powered Mental Wellness Check-in & Alert System

This workflow is designed to perform a daily mental wellness check-in with users via Slack, analyze their responses using AI-driven sentiment analysis, securely store the data, and send SMS alerts if negative sentiment is detected. It automates continual mental health monitoring and timely support outreach.

---

## Workflow Overview

- **Trigger Time:** Everyday at 9:00 AM
- **User Interaction Channel:** Slack (Direct Message)
- **AI Model:** OpenAI GPT-4 for sentiment and emotional analysis
- **Data Storage:** MongoDB
- **Alert Channel:** SMS via Twilio (sent only for negative sentiment responses)

---

## Nodes Description

### 1. Daily Trigger
- **Type:** Cron
- **Function:** Triggers the workflow at 9:00 AM daily to initiate the check-in process.

### 2. Send Daily Check-in Prompt
- **Type:** Slack
- **Function:** Sends a personalized message to the user asking how they are feeling on a scale of 1-10 and invites them to share feelings in their own words.
- **Message Template:**  
  `Hi {{ userName || "there" }}! How are you feeling today on a scale of 1 to 10? You can also share how you're feeling in your own words.`

### 3. Listen for User Response
- **Type:** Slack Trigger
- **Function:** Listens for an incoming direct message reply from the specified Slack user.

### 4. Perform Sentiment Analysis & Insights
- **Type:** OpenAI API
- **Function:** Sends the user's text response to the GPT-4 model. The prompt instructs the AI to analyze sentiment and emotional cues, returning a JSON object containing:  
  - `sentiment`: positive, neutral, or negative  
  - `sentimentScore`: numeric sentiment strength  
  - `emotionalKeywords`: list of emotional indicators  
  - `suggestedSupport`: summary advice or support suggestions  

### 5. Parse AI Output
- **Type:** Set
- **Function:** Parses the JSON output from the AI and extracts key data fields (`sentiment`, `sentimentScore`, `emotionalKeywords`, `suggestedSupport`) into structured workflow variables.

### 6. Check for Negative Sentiment
- **Type:** If Condition
- **Function:** Evaluates if the sentiment returned from the AI analysis is `negative`.

### 7. Send SMS Alert
- **Type:** Twilio
- **Function:** If negative sentiment is detected, sends a personalized SMS alert to the user's phone number offering supportive messaging and crisis help information.  
- **Message Template:**  
  `Hi {{ userName || "there" }}, we noticed you might be having a tough day based on your recent check-in. Remember, support is available if you need it. Here’s a personalized suggestion: {{ suggestedSupport }}. If you are in crisis, please consider contacting professional help.`

### 8. Store Check-in Data Securely
- **Type:** MongoDB
- **Function:** Saves the check-in record along with analyzed sentiment insights into a MongoDB collection `userCheckIns`. Fields stored include:  
  - `userId`  
  - `timestamp`  
  - `originalText`  
  - `sentiment`  
  - `sentimentScore`  
  - `emotionalKeywords`  
  - `suggestedSupport`

---

## Workflow Connections

- The **Daily Trigger** initiates sending the Slack check-in prompt.
- After sending the prompt, the workflow waits for a Slack DM response.
- Once the user replies, their message is processed by the AI sentiment analysis.
- AI output is parsed, then:
  - **If negative sentiment is detected**, send an SMS alert.
  - Regardless of sentiment, store the check-in data securely in MongoDB.

---

## Required Credentials

- **Slack API Credential**  
  Needed for sending messages and listening to user responses on Slack.

- **OpenAI API Credential**  
  Required to access GPT-4 for text analysis.

- **Twilio API Credential**  
  Used to send SMS alerts to users.

- **MongoDB Credential**  
  Required to store user check-in data securely.

---

## Configuration Notes

- **Trigger Time:** Can be adjusted in the "Daily Trigger" node by modifying the cron schedule.
- **Slack Channel:** Direct message identified by `slackUserId` variable.
- **User Identification:** The workflow assumes user metadata such as `userName`, `slackUserId`, `phoneNumber`, and `userId` are available in the input data context.
- **Sentiment Threshold:** The system sends alerts only on strict `"negative"` sentiment classification.
- **Execution Timeout:** Set to 300 seconds to allow sufficient processing time.

---

## Summary

This workflow provides an automated, AI-enhanced mental wellness check-in and alerting system using Slack as an interface and AI for sentiment analysis. It helps mental health teams monitor well-being, detect negative states early, and offer timely support through SMS alerts, while securely logging user check-in data for longitudinal analysis.