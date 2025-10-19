# SmartUrban Garden AI Pest Insight & Eco-Friendly Treatment Scheduler

## Overview

This workflow automates the detection of garden pests using AI image analysis and generates an eco-friendly treatment schedule. It streamlines pest management by creating actionable schedules logged into Google Sheets and notifying the garden care team via Slack.

---

## Workflow Steps

### 1. Receive Image

- **Node Type:** HTTP Trigger
- **Description:** Accepts POST requests containing garden plant images via the `/upload-image` endpoint.
- **Function:** Serves as the entry point for users to upload photos of plants suspected of pest infestation.
- **Output:** Receives raw image data for further processing.

---

### 2. AI Pest Detection

- **Node Type:** HTTP Request
- **Description:** Sends the uploaded image to an external AI pest detection API (`https://api.example-ai-pestdetection.com/detect`) to identify pests.
- **Method:** POST (with the image data in JSON body)
- **Response:** JSON containing a list of detected pests.
- **Authentication:** None (update as needed based on API requirements)

---

### 3. Eco-Friendly Treatment Recommender

- **Node Type:** Function
- **Description:** Maps detected pests to recommended eco-friendly treatments.
- **Logic:** 
  - Uses predefined pest-to-treatment mappings such as:
    - Aphids → Introduce ladybugs, spray neem oil, insecticidal soap
    - Whiteflies → Yellow sticky traps, parasitic wasps, insecticidal soap
    - Spider mites → Increase humidity, spray miticide, predatory mites
    - Caterpillars → Handpick, Bacillus thuringiensis, introduce birds
  - Aggregates and deduplicates treatment recommendations based on the detected pests.
- **Output:** List of unique eco-friendly treatment recommendations.

---

### 4. Treatment Scheduler

- **Node Type:** Function
- **Description:** Creates a 7-day treatment schedule by evenly spreading the recommended treatments over the upcoming week.
- **Logic:**
  - Assigns each treatment to a day, cycling through the 7 days.
  - Each scheduled item contains:
    - Treatment name
    - Scheduled date (ISO format)
    - Status: `"scheduled"`
- **Output:** Array of treatment schedule objects.

---

### 5. Log Schedule to Google Sheets

- **Node Type:** Google Sheets
- **Description:** Appends the treatment schedule to a Google Sheet.
- **Configuration:**
  - **Sheet ID:** Replace `<YOUR_GOOGLE_SHEET_ID>` with your target Google Sheet ID.
  - **Range:** `Sheet1!A:C` (Columns for Scheduled Date, Treatment, Status)
  - **Value Input Mode:** User Entered
- **Note:** Google Sheets OAuth2 credentials must be configured (`<YOUR_GOOGLE_CREDENTIALS>`).

---

### 6. Notify Team (Slack)

- **Node Type:** Slack
- **Description:** Sends a message to the Slack channel `garden-care` notifying the team about the new pest treatment schedule.
- **Message Format:**
  ```
  New eco-friendly pest treatment schedule created:
  YYYY-MM-DD: treatment
  YYYY-MM-DD: treatment
  ...
  ```
- **Note:** Slack credentials must be configured (`<YOUR_SLACK_CREDENTIALS>`).

---

## Setup Instructions

1. **API Endpoint**
   - Make the HTTP Trigger `/upload-image` endpoint publicly accessible to accept image uploads.

2. **Configure Pest Detection API**
   - Replace the placeholder URL (`https://api.example-ai-pestdetection.com/detect`) with your actual AI pest detection service endpoint.
   - Update authentication as required.

3. **Google Sheets Setup**
   - Create or select a Google Sheet.
   - Replace `<YOUR_GOOGLE_SHEET_ID>` in the Google Sheets node.
   - Obtain and add OAuth2 credentials for Google Sheets API.

4. **Slack Integration**
   - Set up a Slack app or use existing credentials.
   - Replace `<YOUR_SLACK_CREDENTIALS>` with appropriate Slack API credentials.
   - Ensure the Slack bot has permission to post messages in the `garden-care` channel.

5. **Deploy and Test Workflow**
   - Upload the workflow into your n8n instance.
   - Test by posting images to the `/upload-image` endpoint.
   - Verify pest detection results, treatment scheduling, Google Sheets logging, and Slack notifications.

---

## Summary

This workflow empowers urban gardeners by combining AI-powered pest detection with sustainable treatment recommendations, organized through dynamic scheduling and team notifications, enabling proactive and environmentally responsible pest management.