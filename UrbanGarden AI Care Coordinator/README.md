# Smart Urban Garden AI Assistant

## Overview  
This workflow is designed to assist urban gardeners by using AI to analyze plant sensor data along with manual inputs. The analysis determines plant health status, optimizes watering schedules, detects pest presence, and generates personalized care recommendations. Notifications are sent via email and SMS to keep the gardener informed and on schedule.

---

## Workflow Breakdown

### 1. Receive Sensor Data  
- **Node:** Receive Sensor Data  
- **Type:** HTTP Trigger (POST)  
- **Endpoint:** `/sensor-data`  
- **Description:** Receives real-time sensor data from the urban garden (e.g., soil moisture, temperature, humidity). Triggers processing upon data receipt.

### 2. Receive Manual Data Entry  
- **Node:** Manual Data Entry  
- **Type:** HTTP Trigger (POST)  
- **Endpoint:** `/manual-entry`  
- **Description:** Allows gardeners to manually submit additional input about plants, such as observations or manual measurements.

### 3. Merge Sensor and Manual Data  
- **Node:** Merge Sensor and Manual Data  
- **Type:** Function  
- **Function:** Combines sensor data and manual data into a single JSON object for comprehensive analysis.

### 4. AI Analysis  
- **Node:** AI Analysis  
- **Type:** OpenAI (GPT-4)  
- **Description:** Sends the combined data to GPT-4 with the prompt to analyze for:  
  - Plant health status  
  - Watering schedule optimization  
  - Pest detection alerts  
  - Care recommendations  
- **Output Format:** Structured JSON with keys:  
  - `healthStatus` (string)  
  - `wateringSchedule` (array of datetime strings)  
  - `pestAlert` (boolean)  
  - `careRecommendations` (array of strings)

### 5. Parse AI Response  
- **Node:** Parse AI Response  
- **Type:** Function  
- **Description:** Parses the AI's JSON response from the message content.

### 6. Extract Care Recommendations  
- **Node:** Extract Care Recommendations  
- **Type:** Function  
- **Description:** Extracts each care recommendation into individual items for processing.

### 7. Send Care Recommendation SMS  
- **Node:** Send Care Recommendation SMS  
- **Type:** Twilio SMS  
- **Description:** Sends each care recommendation as an SMS to the gardener's phone number.  
- **Credentials Required:** Twilio API credentials  
- **Note:** Replace `"YOUR_PHONE_NUMBER"` with the gardener's actual phone number.

### 8. Filter Pest Alerts  
- **Node:** Filter Pest Alerts  
- **Type:** Function  
- **Description:** Filters through the AI analysis results and passes items only if a pest alert is detected (`pestAlert` is `true`).

### 9. Send Pest Alert Notification  
- **Node:** Send Pest Alert Notification  
- **Type:** Email  
- **Description:** Sends an immediate pest alert email to the gardener if pests are detected.  
- **Recipient:** `gardener@example.com` (replace with actual recipient)  
- **Credentials Required:** SMTP credentials

### 10. Create Watering Events  
- **Node:** Create Watering Events  
- **Type:** Split In Batches  
- **Description:** Processes each watering schedule datetime as a separate event for scheduling notifications.

### 11. Format Watering Notification  
- **Node:** Format Watering Notification  
- **Type:** Function  
- **Description:** Formats each watering event into a reminder message with the scheduled watering date and time.

### 12. Schedule Watering Notification  
- **Node:** Schedule Watering Notification  
- **Type:** Schedule Trigger  
- **Timezone:** America/New_York  
- **Description:** Schedules triggers based on each watering event's datetime to send reminders at the appropriate time.

### 13. Send Watering Notification  
- **Node:** Send Watering Notification  
- **Type:** Email  
- **Description:** Sends an email reminder to the gardener to water their plants at the scheduled time.  
- **Recipient:** Configured from `"Notify User"` node parameters (ensure email address is set).  
- **Credentials Required:** SMTP credentials

---

## Credentials Required  
- **SMTP:** For sending emails (watering notifications and pest alerts).  
- **Twilio Account:** For sending SMS care recommendations.

---

## Endpoints  
- **/sensor-data:** POST endpoint for sensor data submission.  
- **/manual-entry:** POST endpoint for manual data submission.

---

## Notes  
- Ensure the SMTP credentials and Twilio credentials are properly set up in your n8n instance before running the workflow.  
- Replace placeholder phone numbers and email addresses with actual contacts for notifications.  
- The AI uses GPT-4 model with controlled temperature settings for balanced, consistent analysis.

---

This workflow automates urban garden care by leveraging AI-powered analysis combined with real-time and manual data inputs, helping gardeners maintain healthy plants with timely notifications and expert recommendations.