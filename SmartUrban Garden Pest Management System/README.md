# AI-Powered Smart Urban Garden AI Pest Detection & Eco-Friendly Treatment Scheduler

## Overview

This workflow automates pest detection in your urban garden using AI image recognition, alerts you via email and Telegram when pests are detected, and schedules eco-friendly pest treatment events on your Google Calendar.

---

## Workflow Components

### 1. Image Upload Webhook

- **Node**: `Image Upload Webhook`
- **Type**: Webhook (POST)
- **Function**: Receives base64 encoded images through an HTTP POST request at the endpoint `/pest_detection_image_upload`.
- **Output**: Forwards the uploaded image data to the AI Pest Detection node.

### 2. AI Pest Detection

- **Node**: `AI Pest Detection`
- **Type**: Image Recognition
- **Function**: Uses an AI model (`ai-pest-detection-model`) to analyze the submitted image for pest detection.
- **Input**: Base64 image from the webhook.
- **Output**: Detected objects with labels and confidence scores.

### 3. Evaluate Pest Detection

- **Node**: `Evaluate Pest Detection`
- **Type**: Function
- **Function**: Filters detections for pests with confidence greater than 0.7.
- **Output**: JSON including the list of detected pests, pest count, and an alert flag indicating whether pests were found.

### 4. Check If Pest Detected (Conditional Check)

- **Node**: `Check If Pest Detected`
- **Type**: If Condition
- **Function**: Checks the alert flag from the evaluation step.
- **Branches**:
  - **True**: Pest(s) detected.
  - **False**: No pest detected (ends workflow silently).

### 5. Send Pest Alert Email

- **Node**: `Send Pest Alert Email`
- **Type**: Email Send
- **Function**: Sends email alert to gardener.
- **From**: `garden.ai.alerts@example.com`
- **To**: `gardener@example.com`
- **Subject**: `"Alert: Pest Detected in Your Urban Garden"`
- **Body**: Informs gardener about the number of pests detected and advises to review treatments.

### 6. Schedule Eco-Friendly Treatments

- **Node**: `Schedule Eco-Friendly Treatments`
- **Type**: Function
- **Function**: Maps detected pests to appropriate eco-friendly treatments and schedules treatment times relative to detection.
- **Sample Treatments & Scheduling**:
  - Aphid → Neem Oil Spray (next day)
  - Whitefly → Insecticidal Soap (2 days later)
  - Caterpillar → BT Bacteria Spray (3 days later)
  - Others → General Eco-Friendly Treatment (next day)

### 7. Create Treatment Event in Google Calendar

- **Node**: `Create Treatment Event`
- **Type**: Google Calendar
- **Function**: Creates calendar events for each treatment.
- **Event Details**:
  - **Summary**: "Eco-Friendly Treatment: \<Treatment Type\>"
  - **Description**: "Scheduled automatic treatment for detected pest."
  - **Start**: Scheduled time from Function2
  - **Duration**: 1 hour
- **Credentials Required**: Google Calendar OAuth2 account.

### 8. Send Telegram Notification

- **Node**: `Send Telegram Notification`
- **Type**: Telegram
- **Function**: Sends notification message via Telegram bot to the gardener’s chat.
- **Message**: "Alert: Pests detected (\<count\>) in your garden. Scheduled eco-friendly treatments applied."
- **Credentials Required**: Telegram Bot API token.

---

## Workflow Execution Flow

1. **Image received** via webhook (`/pest_detection_image_upload`).
2. Image goes through **AI Pest Detection** model.
3. Results are **evaluated** for pests with high confidence.
4. If pests found:
   - **Email alert** sent to gardener.
   - Runs treatment scheduler to map pests to eco-friendly treatments.
   - Creates **Google Calendar events** for each treatment.
   - Sends **Telegram notification** confirming alerts and scheduling.
5. If no pests, the workflow simply ends without further action.

---

## Setup Instructions

1. Deploy this workflow within your n8n instance.
2. Configure the following credentials:
   - **Google Calendar OAuth2 Api** with write access to your calendar.
   - **Telegram Bot API** with your Telegram bot token and chat setup.
3. Update Email addresses in the `Send Pest Alert Email` node:
   - Replace `garden.ai.alerts@example.com` with your alert email sender.
   - Replace `gardener@example.com` with the recipient gardener’s email.
4. Expose the webhook URL (`/pest_detection_image_upload`) to your image source.
5. Ensure AI model `ai-pest-detection-model` is available or replaced with your own pest detection model.

---

## Input Data Format

Send a POST request with JSON payload including base64 image:

```json
{
  "imageBase64": "<base64-encoded-image-string>"
}
```

---

## Output and Notifications

- Email notifications on pest detection.
- Telegram alerts confirming detection and treatment scheduling.
- Google Calendar events for eco-friendly treatments.

---

## Additional Notes

- Pest confidence threshold is 0.7; adjust in the `Evaluate Pest Detection` function for sensitivity.
- Treatment schedules are set relative to the current date/time.
- Treatment types are customizable in the `Schedule Eco-Friendly Treatments` function.
- Workflow is designed for extensibility with other communication channels or treatment types.

---

**End of Document**