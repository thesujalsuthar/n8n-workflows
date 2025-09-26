# Automated Daily Mental Wellness Check-in & Personalized Support System

This workflow is designed to facilitate a daily mental wellness check-in through a personalized survey, analyze user responses with AI-driven sentiment and mental health indicators, and deliver tailored recommendations and alerts to support users' mental well-being.

---

## Workflow Overview

### 1. Daily Trigger
- **Node:** `Daily Trigger` (Schedule Trigger)
- **Description:** Activates at 9:00 AM daily to initiate the mental wellness check-in process.

### 2. Create Mood Survey
- **Node:** `Create Mood Survey` (Google Forms - Create Survey)
- **Description:** Automatically creates or resets a Google Form titled **"Daily Mood Survey"** with two questions:
  - *"How are you feeling today?"* (Single choice; required)
    - Choices: Very Happy, Happy, Neutral, Sad, Very Sad, Anxious, Stressed
  - *"Would you like to share more about how you’re feeling?"* (Text; optional)

### 3. Update Survey Customization
- **Node:** `Update Survey Customization` (Google Forms - Update Survey)
- **Description:** Adds an informational note about the survey and its customizable nature for ease of adjustments.

### 4. Evening Data Collection Trigger
- **Node:** `Evening Data Collection Trigger` (Schedule Trigger)
- **Description:** Triggers at 8:00 PM daily to fetch responses collected from the survey throughout the day.

### 5. Get Survey Responses
- **Node:** `Get Survey Responses` (Google Forms - Get Responses)
- **Description:** Retrieves all responses submitted to the Daily Mood Survey within the current day.

### 6. Extract Sentiment Text
- **Node:** `Extract Sentiment Text` (Function)
- **Description:** Extracts textual feedback from the optional question "Would you like to share more about how you’re feeling?" to prepare data for sentiment analysis.

### 7. AI Sentiment & Mental Health Analysis
- **Node:** `AI Sentiment & Mental Health Analysis` (OpenAI GPT-4o-mini)
- **Description:** Leverages OpenAI to analyze the sentiment and detect mental health signals from the user’s textual input.  
- **Output:** A JSON object containing:
  - `sentiment`: positive, neutral, or negative
  - `indicators`: list of detected mental health indicators (e.g., anxiety, stress, depression)
  - `severity`: level of concern (low, medium, high)

### 8. Parse AI Analysis
- **Node:** `Parse AI Analysis` (Function)
- **Description:** Parses the AI's JSON output safely and prepares the structured data for aggregation.

### 9. Aggregate Indicators and Severity
- **Node:** `Aggregate Indicators and Severity` (Function)
- **Description:** Aggregates indicators from all responses and determines the highest reported severity level for the day.

### 10. Generate Personalized Recommendations
- **Node:** `Generate Personalized Recommendations` (Function)
- **Description:** Based on the aggregated severity and indicators, generates tailored advice and relevant mental health resources:
  - **High severity:** Advises immediate professional support and provides crisis resource links.
  - **Medium severity:** Offers helpful resources and mindfulness exercises.
  - **Low severity:** Encourages ongoing monitoring and self-care.

### 11. Send Notification Alert
- **Node:** `Send Notification Alert` (Pushbullet)
- **Description:** Sends a daily summary notification containing:
  - Severity level
  - Detected mental health indicators
  - Personalized recommendations
  - Helpful mental health resources  
  Delivered via Pushbullet to notify the user promptly.

---

## Key Features

- **Automated scheduling:** Morning survey deployment and evening data collection.
- **Customizable survey:** Easily adjustable questions and information via Google Forms nodes.
- **AI-driven analysis:** Utilizes GPT-4o-mini for nuanced understanding of user input.
- **Personalized recommendations:** Dynamically adapts support based on severity and detected indicators.
- **Real-time alerts:** Provides actionable insights through Pushbullet notifications daily.

---

## Prerequisites

- **n8n Automation Platform** properly set up and running.
- **Google Forms API credentials** configured within n8n for survey creation and response retrieval.
- **OpenAI API credentials** with access to GPT-4o-mini model.
- **Pushbullet API credentials** for sending push notifications.

---

## How to Use

1. **Set up credentials** for Google Forms, OpenAI, and Pushbullet in your n8n instance.
2. **Activate** the workflow to enable automatic daily execution.
3. **Receive daily surveys** delivered automatically at 9:00 AM.
4. **Users complete the survey**, optionally sharing detailed feelings.
5. **AI analyzes inputs** and aggregates results by 8:00 PM.
6. **Get personalized mental wellness summaries and recommendations** via Pushbullet notification each evening.
7. **Customize questions or recommendations** within the workflow as needed.

---

## Customization

- Modify survey questions and options in `Create Mood Survey` and `Update Survey Customization` nodes.
- Adjust AI prompt in `AI Sentiment & Mental Health Analysis` for different analysis styles or expanded output.
- Tailor resource links and recommendation logic in `Generate Personalized Recommendations` for varying support focuses.
- Change notification delivery methods or content formatting in the `Send Notification Alert` node.

---

*This workflow aims to support daily mental wellness by combining scheduled user engagement, AI-powered sentiment analysis, and compassionate personalized care guidance.*