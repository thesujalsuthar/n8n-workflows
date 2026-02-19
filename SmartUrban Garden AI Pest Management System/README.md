# SmartUrban Garden AI Pest Detection & Eco-Friendly Treatment Scheduler

## Overview

This n8n workflow facilitates a smart urban garden pest management system by detecting pests from uploaded images, retrieving environmental and plant health data, determining eco-friendly pest treatments, scheduling daily reminders for treatments, and generating detailed pest management reports.

---

## Workflow Components

### 1. Pest Image Webhook

- **Type:** Webhook Trigger  
- **Path:** `/pest-detection-upload`  
- **Purpose:** Receives image URLs uploaded by users for pest detection.

---

### 2. AI Pest Detection

- **Type:** AI Image Classification  
- **Model:** Custom Pest Detection Model  
- **Input:** Image URL from the Pest Image Webhook  
- **Purpose:** Classifies the uploaded image to identify the type of pest present.

---

### 3. Get Environmental Data

- **Type:** HTTP Request  
- **Credential:** `EnvDataApiCredentials` (HTTP Basic Auth)  
- **Purpose:** Fetches real-time environmental sensor data such as temperature, humidity, light levels, and soil moisture from garden sensors or an external API.

---

### 4. Fetch Plant Health Data

- **Type:** HTTP Request  
- **Credential:** `PlantHealthApiCredentials` (HTTP Basic Auth)  
- **Purpose:** Retrieves latest plant health metrics including leaf moisture, color index, and growth rate to aid in optimizing pest treatment decisions.

---

### 5. Determine Eco-Friendly Treatment

- **Type:** Function  
- **Input:**  
  - Pest Type from AI Pest Detection  
  - Environmental Data from sensors/APIs  
  - Plant Health Data  
- **Logic:**  
  Selects suitable eco-friendly pest treatments based on pest type and current environmental conditions. For example:  
  - Aphids: Use neem oil spray if humidity > 70%, otherwise insecticidal soap.  
  - Spider mites: Increase humidity and apply predatory mites if temperature > 30°C, else horticultural oil spray.  
  - Whiteflies: Introduce ladybugs and use yellow sticky traps.  
  - Others: Manual removal and close monitoring.  
- **Output:** Pest type and recommended eco-friendly treatment.

---

### 6. Schedule Treatment Reminder

- **Type:** Schedule Trigger  
- **Frequency:** Daily (every 1 day)  
- **Start Date:** Workflow start time  
- **Purpose:** Sets up a daily recurring trigger to send treatment reminders to users.

---

### 7. Prepare Reminder Message

- **Type:** Function  
- **Purpose:** Formats a daily reminder message containing the eco-friendly treatment advice.

---

### 8. Send Notification Email

- **Type:** Email Send  
- **Credential:** `SmtpCredentials` (SMTP)  
- **Recipient:** User email either from webhook input (`userEmail`) or environment variable (`USER_EMAIL`)  
- **Subject:** Daily Eco-Friendly Pest Treatment Reminder  
- **Content:** Reminder message prepared in the previous step.  
- **Purpose:** Sends the daily pest treatment reminder email to the user.

---

### 9. Report Request Webhook

- **Type:** Webhook Trigger  
- **Path:** `/pest-management-report`  
- **Purpose:** Receives requests from users to generate a current pest management report.

---

### 10. Compile Pest Management Report

- **Type:** Function  
- **Data Sources:**  
  - Pest Detection Results  
  - Eco-Friendly Treatment Info  
  - Environmental Conditions  
- **Purpose:** Compiles a detailed report including detected pests, recommended treatments, and current environmental data.

---

### 11. Respond with Report

- **Type:** Webhook Response  
- **Purpose:** Returns the compiled pest management report as JSON in response to report requests.

---

## Workflow Execution Flow

1. **Image Upload:** User uploads a pest image triggering the **Pest Image Webhook**.
2. **Pest Classification:** The image URL is sent to **AI Pest Detection** for classification.
3. **Data Collection:** Simultaneously, **Get Environmental Data** and **Fetch Plant Health Data** nodes retrieve necessary environmental and plant health metrics.
4. **Treatment Decision:** The classification and sensor data are fed into **Determine Eco-Friendly Treatment** which selects appropriate eco-friendly pest control methods.
5. **Reminder Scheduling:** A daily trigger (**Schedule Treatment Reminder**) initiates sending daily reminders.
6. **Reminder Notification:** A message is prepared (**Prepare Reminder Message**) and emailed to the user (**Send Notification Email**).
7. **Report Generation:** On request via **Report Request Webhook**, the workflow compiles (**Compile Pest Management Report**) and responds with (**Respond with Report**) a summary of current pest and treatment data.

---

## Prerequisites

- **n8n Instance:** Properly hosted and accessible for webhooks and scheduling.
- **AI Model:** Custom pest detection AI model integrated with n8n AI node.
- **APIs & Credentials:**  
  - Environmental data API access with HTTP Basic Auth.  
  - Plant health data API access with HTTP Basic Auth.  
  - SMTP credentials for sending emails.  
- **Environment Variables:** `USER_EMAIL` (optional fallback email recipient).

---

## Endpoints

| Endpoint                  | Method | Description                             |
|---------------------------|--------|-------------------------------------|
| `/pest-detection-upload`  | POST   | Upload pest images for detection      |
| `/pest-management-report` | GET    | Request current pest management report |

---

## Environment Variables

- `USER_EMAIL`: Fallback email address for sending daily treatment reminders.

---

## Notes

- The workflow is designed for a smart urban garden setup, integrating AI, IoT sensor data, and eco-friendly practices.
- Treatment strategies take into account current environmental conditions to maximize effectiveness and sustainability.
- Daily reminders help users to maintain consistent pest management for healthy plant growth.
- The report endpoint provides a comprehensive snapshot for monitoring garden health and treatment status.

---

## License

This workflow is provided as-is for educational and personal use. Please ensure compliance with local regulations and best practices when applying pest treatment methods.