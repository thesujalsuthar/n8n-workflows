# EcoAware: AI-Driven Personalized Sustainability Tips & Habit Motivation

## Overview

EcoAware is an automated workflow designed to deliver personalized daily sustainability tips and eco-friendly habit motivation to users. Leveraging real user preferences, curated sustainability tips, AI-driven personalization, and multi-channel notifications, this workflow encourages and tracks sustainable behavior effectively.

---

## Workflow Components

### 1. Fetch User Preferences (`FetchUserPreferences`)
- **Type:** Google Sheets (OAuth2 Authentication)
- **Purpose:** Retrieves all user preferences and behavioral data from a designated Google Sheet (`USER_PREFERENCES_SHEET_ID`).
- **Output:** JSON array of user preferences data.

### 2. Fetch Daily Sustainability Tips (`FetchDailyTips`)
- **Type:** HTTP Request
- **Purpose:** Retrieves the daily curated sustainability tips from the external API endpoint: `https://api.curatedsustainabilitytips.com/daily`.
- **Response Format:** JSON
- **Output:** Daily tips data for personalization.

### 3. Merge Preferences and Tips (`MergePreferencesAndTips`)
- **Type:** Function
- **Purpose:** Combines the user preferences and the daily sustainability tips into a single structured JSON for downstream AI processing.
- **Output:** JSON object containing both `userPreferences` and `dailyTips`.

### 4. Personalize Tips with AI (`PersonalizeTipsAI`)
- **Type:** OpenAI (GPT-4)
- **Purpose:** Generates 1-3 personalized, actionable sustainability habit suggestions for each user based on their preferences and the daily tips.
- **Parameters:**
  - Model: GPT-4
  - Temperature: 0.7
  - Max Tokens: 500
  - Prompt: Includes user preferences and daily tips, requests encouragement in tone.
- **Output:** Personalized sustainability messages.

### 5. Send Notifications
Multi-channel notification nodes to deliver the personalized tips.

- **Send Email Notification (`SendEmailNotification`)**
  - Sends personalized tips via email from `no-reply@ecoaware.com` to the user's email address.
  - Subject: "Your Daily Personalized Sustainability Tips 🌿"
  - Supports plain text and HTML formatting.

- **Send SMS Notification (`SendSMSNotification`)**
  - Sends the personalized message via SMS using Twilio.
  - Targets user phone numbers.

- **Send Push Notification (`SendPushNotification`)**
  - Delivers personalized messages as push notifications on Firebase.
  - Targets users by their unique user IDs.

### 6. Format Engagement Data (`FormatEngagementData`)
- **Type:** Function
- **Purpose:** Prepares engagement data by adding relevant fields such as engagement metrics and current date.
- **Output:** Structured data for tracking.

### 7. Track User Engagement (`TrackUserEngagement`)
- **Type:** Google Sheets Append Operation
- **Purpose:** Logs user engagement with tips into a Google Sheet (`USER_ENGAGEMENT_SHEET_ID`) under the range `Engagement!A:D`.
- **Stored Data:** User ID, date, tip ID, engagement metric.

### 8. Generate Analytical Insights (`AnalyticsQuery`)
- **Type:** SQL Query
- **Purpose:** Aggregates engagement and progress metrics per user from the stored engagement data.
- **Query Example:** 
  ```sql
  SELECT userId, SUM(engagementScore) as totalEngagement, AVG(progressPercent) as avgProgress FROM user_engagement_data GROUP BY userId
  ```

### 9. Generate Insights with AI (`InsightsAI`)
- **Type:** OpenAI (GPT-4)
- **Purpose:** Synthesizes aggregated user engagement data into actionable insights and recommendations to optimize future sustainability tips.
- **Parameters:**
  - Model: GPT-4
  - Temperature: 0.5
  - Max Tokens: 400
  - Prompt: Includes aggregated analytics data for analysis.

---

## Workflow Sequence

1. **Fetch User Preferences** and **Fetch Daily Tips** run in parallel.
2. Their data is merged in **Merge Preferences and Tips**.
3. The merged data is sent to **Personalize Tips with AI** for creating tailored sustainability messages.
4. Personalized messages are sent out via **Email**, **SMS**, and **Push Notification** simultaneously.
5. Each notification triggers **Format Engagement Data** to prepare user interaction details.
6. Engagement data is appended in **Track User Engagement**.
7. Engagement data is analyzed through the **Generate Analytical Insights** SQL query.
8. Finally, **Generate Insights with AI** produces summarized insights and recommendations based on the analytics.

---

## Prerequisites & Configuration

- **Google Sheets API Access:** OAuth2 authenticated for reading user preferences and writing engagement data.
- **API Endpoint:** Access to `https://api.curatedsustainabilitytips.com/daily` for fresh sustainability tips.
- **OpenAI API Key:** For GPT-4 interaction.
- **Twilio Account:** For SMS notifications.
- **Email SMTP Setup:** To send email notifications from `no-reply@ecoaware.com`.
- **Firebase Push Notifications:** Configured for push notification delivery.
- **Database:** SQL-accessible database storing user engagement data.

---

## Notes

- Replace placeholder IDs (`USER_PREFERENCES_SHEET_ID` and `USER_ENGAGEMENT_SHEET_ID`) with actual Google Sheet IDs.
- Ensure the environment is correctly set up with all necessary API keys and authentication credentials.
- The workflow is designed to scale for multiple users, personalizing and tracking engagement in a structured manner.

---

This workflow enables a seamless, automated cycle of empowering users with personalized sustainability guidance, fostering engagement, and iteratively improving tip effectiveness through AI-driven analytics.