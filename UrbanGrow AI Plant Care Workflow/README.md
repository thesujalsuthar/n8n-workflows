# SmartUrban Garden AI Assistant

## Overview

SmartUrban Garden AI Assistant is an automated workflow designed to assist urban gardeners by analyzing plant images, detecting pests, gathering environmental sensor data, generating tailored plant care recommendations, and managing watering reminders. The system integrates AI, computer vision, IoT sensors, and communication tools for a seamless plant care assistant experience.

---

## Workflow Breakdown

### 1. Receive Plant Image (`Receive Plant Image`)

- **Type:** HTTP Request (POST)
- **Endpoint:** `/upload-plant-image`
- **Description:** This node receives images of plants uploaded by users. The image URL is extracted for downstream processing.

---

### 2. Pest Detection (`Pest Detection`)

- **Type:** Microsoft Computer Vision API (Detect Objects)
- **Input:** Image URL from the "Receive Plant Image" node.
- **Description:** Analyzes the plant image to detect the presence of pests or other significant objects relevant to plant health.
- **Credentials Required:** Microsoft Computer Vision API key.

---

### 3. Get Environmental Data

- **Nodes Involved:**
  - `Get Environmental Data`: IoT Node accessing device status for garden sensors.
  - `Fetch Sensor Data`: HTTP Request fetching sensor data from local garden sensor API (`http://garden-smart-sensor.local/api/data`).
- **Description:** Collects real-time environmental data such as temperature, humidity, soil moisture, and other relevant parameters from IoT garden sensors.
- **Credentials Required:** API access for IoT device and HTTP access to sensor data service.

---

### 4. Aggregate Data (`Aggregate Data`)

- **Type:** Function Node
- **Description:** Combines pest detection results and environmental sensor data into a single JSON object, preparing structured input for AI analysis.

```javascript
return [{
  json: {
    plantAnalysis: $node["Pest Detection"].json,
    environment: $node["Fetch Sensor Data"].json
  }
}];
```

---

### 5. AI Plant Care Recommendations (`AI Plant Care Recommendations`)

- **Type:** OpenAI GPT-4
- **Description:** Generates detailed recommendations on plant care based on the aggregated data: pest detection results and environmental parameters.

- **Prompt Highlights:**
  - Specialized in urban garden plant care.
  - Responses include:
    - Care Recommendations
    - Pest Detection summary
    - Watering Schedule
  - Response format formatted as clear bullet points for readability.

- **Credentials Required:** OpenAI API key.

---

### 6. Parse AI Output (`Parse AI Output`)

- **Type:** Function Node
- **Description:** Parses the AI-generated text output to extract distinct information sections:
  - Care Recommendations
  - Pest Detection summary
  - Watering Schedule

- **Additional Logic:** Transforms the watering schedule into a structured format with start and end times for scheduling.

```javascript
const recommendations = $node["AI Plant Care Recommendations"].json["choices"][0]["message"]["content"];

const careRecommendationsMatch = recommendations.match(/- Care Recommendations:\s*([\s\S]*?)(?=- Pest Detection:|$)/i);
const pestDetectionMatch = recommendations.match(/- Pest Detection:\s*([\s\S]*?)(?=- Watering Schedule:|$)/i);
const wateringScheduleMatch = recommendations.match(/- Watering Schedule:\s*([\s\S]*?)/i);

const careRecommendations = careRecommendationsMatch ? careRecommendationsMatch[1].trim() : "";
const pestDetection = pestDetectionMatch ? pestDetectionMatch[1].trim() : "";
const wateringScheduleRaw = wateringScheduleMatch ? wateringScheduleMatch[1].trim() : "";

// Example fixed watering schedule times (can be replaced with parsed times)
return [{
  json: {
    careRecommendations,
    pestDetection,
    wateringSchedule: [{
      start: new Date().toISOString(),
      end: new Date(Date.now() + 15 * 60 * 1000).toISOString()
    }]
  }
}];
```

---

### 7. Send Slack Notification (`Send Slack Notification`)

- **Type:** Slack Node
- **Channel:** `#garden-alerts`
- **Message:** The detailed care recommendations are sent as a notification to the designated Slack channel.
- **Credentials Required:** Slack API token.

---

### 8. Schedule Watering Reminder (`Schedule Watering Reminder`)

- **Type:** Google Calendar Node
- **Calendar ID:** `garden-watering`
- **Event:** Creates a calendar event titled "Watering Reminder" based on the watering schedule from AI output.
- **Description:** Automated watering reminders ensure timely plant care.
- **Credentials Required:** Google Calendar OAuth2 credentials.

---

### 9. Send SMS Reminder (`Send SMS Reminder`)

- **Type:** Twilio Node
- **Recipient:** User's phone number pulled from workflow context.
- **Message:** Reminder message to water plants according to the generated schedule.
- **Credentials Required:** Twilio API key.

---

## Workflow Connections Summary

- User uploads plant image → Pest Detection analyzes image.
- Pest Detection results + Environmental Sensor Data → Aggregated.
- Aggregated data → Sent to OpenAI GPT-4 for recommendations.
- AI recommendations → Parsed into structured info.
- Recommendations → Sent to Slack channel and used to schedule watering reminders.
- Watering reminders → Added to Google Calendar.
- Calendar events → Trigger SMS reminders via Twilio.

---

## Prerequisites and Requirements

- **APIs and Credentials:**
  - Microsoft Computer Vision API key.
  - OpenAI API key (GPT-4).
  - Slack API token.
  - Google Calendar OAuth2 setup.
  - Twilio API credentials.
- **IoT Devices:**
  - Garden sensors providing real-time environmental data accessible via HTTP API.
- **Environment:**
  - n8n automation platform or compatible workflow automation environment.

---

## Summary

This SmartUrban Garden AI Assistant automates plant health monitoring and care advisory through intelligent analysis of images and environmental data. It facilitates timely plant care by integrating communication tools and calendar management, enabling urban gardeners to maintain healthy plants effortlessly.