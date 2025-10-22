# Automated Daily Mental Wellness Check-in & Personalized Support System

## Overview

This workflow automates a daily mental wellness check-in via SMS and provides personalized support based on user responses. It leverages scheduled triggers, user data from a spreadsheet, SMS communication through Twilio, and AI-powered sentiment analysis with OpenAI to identify users in distress and escalate support appropriately.

---

## Workflow Components

### 1. **Daily Trigger**
- **Type:** Cron
- **Description:** Activates the workflow every day at 9:00 AM.
- **Purpose:** Initiates daily wellness check-in messages to all users.

### 2. **Get Users from Spreadsheet**
- **Type:** Google Sheets
- **Parameters:**
  - **Sheet ID:** Replace `"your_sheet_id_here"` with your Google Sheets document ID.
  - **Range:** Reads user data from `Users!A2:B`.
- **Purpose:** Retrieves all users’ first names and phone numbers for outreach.

### 3. **Send Wellness Check-in SMS**
- **Type:** Twilio
- **Parameters:**
  - Sends a personalized SMS asking users to rate their mental wellness on a scale of 1 to 10 and optionally provide an explanation.
  - Uses the user's phone number and first name dynamically.
- **Credentials:** Twilio API required.
- **Purpose:** Initiates mental wellness check-in with the user.

### 4. **Receive Response Webhook**
- **Type:** Webhook
- **Settings:**
  - Custom URI generated per user phone number (format: `wellness-check-in-<phoneNumber>`).
  - HTTP method: POST.
  - Returns a thank you response after user submits their rating and explanation.
- **Purpose:** Receives user replies to the wellness check-in SMS.

### 5. **Extract Message Text**
- **Type:** Function Node
- **Description:** Extracts the text message (`Body`) received from the user response.
- **Purpose:** Prepares the user's input for analysis.

### 6. **Analyze Mental State**
- **Type:** OpenAI GPT-4o-mini
- **Prompt:** 
  - Acts as a caring mental health assistant.
  - Analyzes user input for distress, depression, anxiety, or suicidal ideation.
  - Responds with a structured JSON containing:
    - `distressDetected` (boolean)
    - `severity` (`none`, `mild`, `moderate`, `severe`)
    - `recommendedAction` (`support resources` or `human counselor`)
    - `summary` (brief emotional state description)
- **Credentials:** OpenAI API key required.
- **Purpose:** Evaluates the emotional state based on user input.

### 7. **Parse AI Analysis**
- **Type:** Function Node
- **Description:** Parses the JSON response from AI; if parsing fails, defaults to no distress detected.
- **Purpose:** Safely extracts mental health analysis results.

### 8. **Check If Escalate Needed**
- **Type:** Split Node
- **Logic:** 
  - If distress is detected with moderate or severe severity, route to escalation branch.
  - Otherwise, route to general support branch.
- **Purpose:** Determines if immediate intervention is required.

### 9. **Send Escalation SMS**
- **Type:** Twilio
- **Content:** 
  - Sends an empathetic message indicating support escalation.
  - Provides emergency contacts and local counseling resources.
- **Purpose:** Provides urgent support information to users in distress.
- **Credentials:** Twilio API required.

### 10. **Send Support Resources SMS**
- **Type:** Twilio
- **Content:** 
  - Sends a thank-you message with self-care reminders.
  - Shares personalized mental wellness resources like meditation apps and CBT exercises.
- **Purpose:** Offers general wellness resources to users without immediate distress.
- **Credentials:** Twilio API required.

---

## Setup Instructions

1. **Google Sheets:**
   - Create a Google Sheet with a sheet named `Users`.
   - Include columns for `firstName` and `phoneNumber` starting at row 2.
   - Set the sheet ID in the "**Get Users from Spreadsheet**" node.

2. **Twilio:**
   - Configure Twilio API credentials in n8n.
   - Ensure phone numbers are in the correct international format for SMS delivery.

3. **OpenAI:**
   - Add OpenAI API credentials in n8n.
   - Adjust model or prompt if needed to fit your requirements.

4. **Webhook Exposure:**
   - Make sure your n8n webhook endpoint is accessible to receive SMS replies.

5. **Activate Workflow:**
   - Enable the workflow in n8n to start daily check-ins at 9 AM.

---

## Summary

This fully automated system fosters daily mental health awareness by engaging users with check-ins and delivering compassionate, AI-informed support. It detects signs of distress early and escalates cases as needed to provide timely care, while also encouraging general wellness with ongoing resources.