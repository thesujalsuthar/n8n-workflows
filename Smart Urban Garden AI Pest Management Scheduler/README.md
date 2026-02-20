# Smart Urban Garden AI Pest Detection & Eco-Friendly Treatment Scheduler

This workflow automates pest detection in an urban garden using AI image recognition and schedules eco-friendly pest treatments based on weather forecasts. It notifies the gardener throughout the process via Telegram and manages treatment appointments in Google Calendar.

---

## Workflow Overview

The workflow runs once daily at 6:00 AM and follows these key steps:

1. **Trigger Daily Workflow**  
   Initiated automatically every day at 6:00 AM.

2. **Fetch Latest Garden Image**  
   Downloads the most recent photo of the garden from a connected camera API.

3. **AI Pest Detection**  
   Uses an AI model to analyze the garden image and detect any pests.

4. **Check for Pests**  
   Determines if any pests were found in the image analysis.

5. **Notify Gardener**  
   - If pests are detected: Sends a notification listing detected pests and indicates scheduling for eco-friendly treatment.  
   - If no pests are detected: Sends a positive notification to the gardener.

6. **Fetch Weather Data**  
   Retrieves hourly weather forecast data (temperature, precipitation, wind speed) from Open-Meteo API for the garden’s location.

7. **Determine Treatment Time**  
   Analyzes weather data along with pest detection results to find a suitable treatment time when no rain is expected in the next 6 hours and temperatures are above 15°C.

8. **Schedule Treatment on Google Calendar**  
   Creates a calendar event titled "Eco-Friendly Pest Treatment" for the selected treatment time, with details of detected pests.

9. **Notify Treatment Scheduled**  
   Sends a Telegram message with the scheduled treatment time or advises manual check if no suitable time was found.

10. **Wait Until Treatment Time**  
    Pauses workflow execution until the scheduled treatment time.

11. **Send Treatment Reminder**  
    Sends a reminder message to the gardener via Telegram when it is time to apply the treatment.

---

## Node Details

### 1. Daily Trigger

- **Type:** Cron Trigger  
- **Schedule:** Every day at 06:00 AM

### 2. Fetch Latest Garden Image

- **Type:** HTTP Request  
- **Endpoint:** `https://your-garden-camera/api/latest-image`  
- **Destination:** Retrieves the latest garden image as a binary file to be analyzed

### 3. AI Pest Detection

- **Type:** AI Image Recognition  
- **Model ID:** `pest-detection-model-id` (replace with your AI model identifier)  
- **Input:** Image binary data (`image/jpeg`)  
- **Output:** JSON object containing a list of detected pests (`detectedPests`)

### 4. Check Pest Detection (IF Node)

- **Condition:** Checks if `detectedPests` exists and contains any entries to determine if pests were detected

### 5. Notify Gardener (Pests Found)

- **Type:** Telegram Message  
- **Chat ID:** Your Telegram chat ID  
- **Message:** Lists detected pest names and states eco-friendly treatment scheduling

### 6. Notify No Pests

- **Type:** Telegram Message  
- **Chat ID:** Your Telegram chat ID  
- **Message:** Confirms no pests detected and encourages gardener

### 7. Fetch Weather Data

- **Type:** HTTP Request  
- **Endpoint:** `https://api.open-meteo.com/v1/forecast` with latitude, longitude, and requested parameters (temperature, precipitation, windspeed)

### 8. Determine Treatment Time (Function Node)

- **Logic Overview:**  
  - Extracts detected pests and weather data.  
  - Searches for the first hour within the next 6 hours where precipitation is < 0.1 mm and temperature > 15°C.  
  - Identifies this hour as the suitable treatment time or returns `null` if no suitable time found.

### 9. Schedule Treatment (Google Calendar)

- **Operation:** Creates an event in the primary Google Calendar  
- **Event Details:**  
  - Title: "Eco-Friendly Pest Treatment"  
  - Description: Lists detected pests  
  - Start & End Times: One-hour duration starting at determined treatment time

### 10. Notify Treatment Scheduled

- **Type:** Telegram Message  
- **Content:** Confirms treatment schedule with time or alerts no suitable time was found

### 11. Wait Until Treatment Time

- **Operation:** Pauses workflow until the scheduled treatment time arrives

### 12. Send Treatment Reminder

- **Type:** Telegram Message  
- **Content:** Reminds the gardener to apply the eco-friendly pest treatment now

---

## Prerequisites

- **n8n Automation Platform** with access to:
  - HTTP Request node capability
  - AI Image Recognition integration (configured with your pest detection model)
  - Telegram Bot API connected with your chat ID
  - Google Calendar API access with event creation permission

- **Garden Camera API** endpoint providing latest images accessible by URL

- **Open-Meteo API** for weather forecast services (latitude and longitude configured)

---

## Configuration Instructions

1. **Update Endpoints & Credentials**
   - Replace `https://your-garden-camera/api/latest-image` with your actual camera image API URL.
   - Replace `pest-detection-model-id` with the ID of your AI pest detection model.
   - Set your Telegram `chatId` in each Telegram node.
   - Configure Google Calendar credentials and calendar ID (`primary` or your chosen calendar).
   - Update Open-Meteo API URL with your garden's latitude and longitude values.

2. **AI Model Preparation**
   - Train or deploy an AI model capable of recognizing common urban garden pests.
   - Make sure it accepts JPEG images and returns detected pest data in JSON format.

3. **Telegram Bot Setup**
   - Create a Telegram bot via BotFather.
   - Obtain your `chatId` from Telegram by messaging the bot or using @userinfobot.

4. **Google Calendar API**
   - Set up OAuth2 credentials for Google Calendar.
   - Enable API and grant permissions for event creation.

---

## Workflow Execution Flow

```plaintext
Daily Trigger (6:00 AM)
      ↓
Fetch Latest Garden Image
      ↓
AI Pest Detection
      ↓
Check Pest Detection ──────────→ No pests → Notify No Pests
      ↓ Yes pests
Notify Gardener
      ↓
Fetch Weather Data
      ↓
Determine Treatment Time
      ↓
Schedule Treatment (Google Calendar) + Notify Treatment Scheduled
      ↓
Wait Until Treatment Time
      ↓
Send Treatment Reminder
```

---

## Benefits

- **Automated Pest Surveillance:** Daily automatic image analysis for timely pest detection.
- **Environmentally Conscious:** Schedules treatments based on eco-friendly principles and weather conditions to avoid chemical runoff or inefficacy.
- **Convenient Notifications:** Keeps the gardener informed through Telegram at every important step.
- **Calendar Management:** Integrates with calendar apps to help organize pest treatment routines smoothly.

---

## Troubleshooting

- Ensure all API endpoints, chat IDs, and credentials are valid and accessible.
- Verify that the AI model returns expected JSON output with the `detectedPests` array.
- Confirm correct timezone and date/time formats are used when scheduling calendar events.
- Monitor the workflow run history in n8n for detailed error logging if issues arise.

---

## License

This workflow template is provided "as is" for educational and personal use. Modify according to your environment and needs.

---

Thank you for using the Smart Urban Garden AI Pest Detection & Eco-Friendly Treatment Scheduler!