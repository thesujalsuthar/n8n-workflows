# AI-Driven Real-Time Wildfire Risk Assessment and Alert System

## Overview

This n8n workflow implements an AI-driven system for real-time wildfire risk assessment and alerting. It continuously fetches environmental data, evaluates wildfire risk using an AI model, and sends alerts via email, SMS, and push notifications to subscribed users if the risk exceeds a predefined threshold.

---

## Workflow Nodes Detail

### 1. Fetch Environmental Data
- **Type:** HTTP Request
- **Purpose:** Pulls real-time environmental data relevant to wildfire risk from the API endpoint:  
  `https://api.environmentaldata.org/v1/wildfire/conditions`
- **Method:** GET
- **Response format:** JSON
- **Key data fetched:** temperature, humidity, wind speed, rainfall, vegetation index, etc.

### 2. Prepare Features
- **Type:** Function
- **Purpose:** Extracts and formats the key environmental features from the fetched data to feed into the AI model.
- **Extracted Features:**
  - temperature
  - humidity
  - windSpeed
  - rainfall
  - vegetationIndex

### 3. AI Wildfire Risk Model
- **Type:** OpenAI Node
- **Purpose:** Uses a custom AI model (`model-abcdefg1234567`) to predict wildfire risk based on the extracted features.
- **Input:** Structured environmental feature data from the "Prepare Features" node.
- **Output:** JSON response expected to contain a `riskScore` (e.g., a float between 0 and 1, representing wildfire risk level).

### 4. Evaluate Risk Threshold
- **Type:** Function
- **Purpose:** Checks if the predicted wildfire risk score meets or exceeds the threshold of 0.7.
- **Behavior:**
  - If `riskScore >= 0.7`, proceeds to send alerts.
  - If below threshold, halts workflow (no alerts are sent).

### 5. Get Subscribed Users
- **Type:** Postgres Database Query
- **Purpose:** Retrieves all subscribed users along with their contact information (email, phone number, user ID) from a PostgreSQL database using stored credentials (`PostgresCreds`).
- **Note:** Required to send targeted alerts to all relevant users.

### 6. Send Email Alert
- **Type:** Email Send
- **Purpose:** Sends an email alert to each subscribed user about high wildfire risk.
- **From:** `alerts@wildfirealerts.com`
- **To:** User's registered email (`{{$json["userEmail"]}}`)
- **Subject:** "Wildfire Risk Alert: High Risk Detected"
- **Email Body:**
  ```
  Dear User,

  Our AI system has detected a high wildfire risk in your area.

  Risk Score: {{$json.riskScore}}

  Please take necessary precautions.

  Stay safe,
  Wildfire Alert Team
  ```

### 7. Send SMS Alert
- **Type:** Twilio Node
- **Purpose:** Sends an SMS notification to users with mobile phone numbers registered.
- **To:** User's phone number (`{{$json["userPhoneNumber"]}}`)
- **Message:**  
  `Wildfire Alert! High risk detected with risk score {{$json.riskScore}}. Please take immediate precautions.`

### 8. Send Push Notification
- **Type:** Pushbullet Node
- **Purpose:** Sends a push notification to the user's devices.
- **Users:** Based on user IDs (`{{$json["userId"]}}`)
- **Notification Content:**
  - **Title:** Wildfire Risk Alert
  - **Body:** High wildfire risk detected! Risk Score: {{$json.riskScore}}. Stay alert and follow safety instructions.

---

## Connections and Data Flow

1. **Fetch Environmental Data** → **Prepare Features**  
   Environmental data retrieved and features extracted.

2. **Prepare Features** → **AI Wildfire Risk Model**  
   Prepared features sent to AI model for risk prediction.

3. **AI Wildfire Risk Model** → **Evaluate Risk Threshold**  
   AI output analyzed for risk score threshold crossing.

4. **Evaluate Risk Threshold** → **Get Subscribed Users** (if threshold exceeded)  
   Subscribed users fetched for alert dispatching.

5. **Get Subscribed Users** → All three alert nodes in parallel:  
   - **Send Email Alert**  
   - **Send SMS Alert**  
   - **Send Push Notification**

---

## Prerequisites and Setup

- **API Access:**  
  Ensure access to the environmental data API and appropriate API keys if required.

- **AI Model:**  
  The `model-abcdefg1234567` is a deployed AI model for wildfire risk prediction. Confirm this model is accessible and authorized for use with the OpenAI node.

- **Database:**  
  PostgreSQL credentials (`PostgresCreds`) must be configured with access to a users table containing subscription information and contact details:
  - Email addresses (`userEmail`)
  - Phone numbers (`userPhoneNumber`)
  - User IDs (`userId`)

- **Alert Services:**
  - Email SMTP server set up (configured in the Email Send node).
  - Twilio account for SMS sending configured.
  - Pushbullet account and credentials for push notifications configured.

---

## Alerting Logic Summary

- The system continuously monitors environmental conditions.
- It uses AI to assess wildfire risk scores in real-time.
- If risk is **high (≥0.7)**, all subscribed users receive alerts via:
  - Email
  - SMS
  - Push Notification
- If risk is below threshold, no notifications are sent to avoid fatigue.

---

## Safety Notice

This system aims to provide timely wildfire risk alerts to assist with early awareness and safety measures. Users should always follow local authority instructions and emergency protocols regardless of alerts from this system.

---

## License

This workflow and associated code examples are provided as-is under no specific license. Modify and use according to your organization's compliance and policies.