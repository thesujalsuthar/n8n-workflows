# Real-Time Transit Delay Alert Workflow

This workflow monitors real-time public transit delay data, predicts the impact of delays on commuter routes using AI, generates alerts for significant delays, matches alerts to subscribed users, and sends notifications via SMS, email, and push notifications.

---

## Workflow Overview:

### 1. Fetch Real-Time Transit Delay Data
- **Node:** `Fetch Real-Time Transit Delay Data`
- **Type:** HTTP Request (GET)
- **Purpose:** Retrieves live delay information from the public transit API endpoint:  
  `https://api.publictransit.example.com/realtime-delays`
- **Response Format:** JSON

---

### 2. Extract Affected Routes
- **Node:** `Extract Affected Routes`
- **Type:** Function
- **Purpose:** Parses the API response to extract key information about delayed routes such as:
  - `routeId`
  - `delayMinutes`
  - `stopId`
  - `description`

---

### 3. AI Predict Impact on Commuter Routes
- **Node:** `AI Predict Impact on Commuter Routes`
- **Type:** AI Prediction
- **Model:** `publictransit-delay-impact`
- **Input:** For each route, sends:
  - `routeId`
  - `delayMinutes`
  - `stopId`
- **Purpose:** Predicts how each delay impacts commuters and suggests alternative routes when possible.

---

### 4. Format Prediction Results
- **Node:** `Format Prediction Results`
- **Type:** Function
- **Purpose:** Structures AI prediction data by including:
  - `routeId`
  - `delayMinutes`
  - `stopId`
  - Predicted `impact` level (e.g., "high", "medium", "low")
  - `alternativeRoutes` suggested by the AI model

---

### 5. Generate Alerts for High Impact Delays
- **Node:** `Generate Alerts for High Impact Delays`
- **Type:** Function
- **Purpose:** Filters out delays with high impact and generates alert messages containing:
  - Route ID
  - Delay duration
  - Suggested alternative routes
- **Output:** A list of alert objects with message text.

---

### 6. Match Alerts to Subscribed Users
- **Node:** `Match Alerts to Subscribed Users`
- **Type:** Function
- **Purpose:** Matches each alert with users subscribed to the affected routes.
- **User Subscription Data:**
  - Contains user contact details (phone, email, device token)
  - Contains each user’s list of subscribed routes
- **Output:** Enriched alerts that include user information to enable targeted notifications.

---

### 7. Send Notifications

- **Send SMS Alert**
  - **Node:** `Send SMS Alert`
  - **Type:** Twilio
  - **Input:** User phone number and alert message.
  - **Credentials:** Twilio Account credentials required.
  
- **Send Email Alert**
  - **Node:** `Send Email Alert`
  - **Type:** Email Send
  - **Input:** User email, subject "Transit Delay Alert", alert message.
  - **Credentials:** SMTP email account required.
  
- **Send Push Notification**
  - **Node:** `Send Push Notification`
  - **Type:** Push Notification
  - **Input:** User device token, notification title and body with the alert message.
  - **Credentials:** Push Notification Service account required.

---

## Node Connections Flow

1. Fetch Real-Time Transit Delay Data → Extract Affected Routes  
2. Extract Affected Routes → AI Predict Impact on Commuter Routes  
3. AI Predict Impact on Commuter Routes → Format Prediction Results  
4. Format Prediction Results → Generate Alerts for High Impact Delays  
5. Generate Alerts for High Impact Delays → Match Alerts to Subscribed Users  
6. Match Alerts to Subscribed Users → Send SMS Alert / Send Email Alert / Send Push Notification (in parallel)

---

## Configuration Notes

- **API Endpoint:** Ensure the endpoint URL for real-time delays is accessible and updated as needed.
- **AI Model:** The prediction node uses the model `publictransit-delay-impact` which should be properly configured in your AI Prediction service.
- **User Subscription Data:** Modify or extend the array of user subscription data inside the "Match Alerts to Subscribed Users" function with actual subscriber details and routes.
- **Credentials:** 
  - Twilio API credentials are needed for sending SMS.
  - SMTP credentials are required for sending emails.
  - A push notification service credential is needed to send push notifications.
  
---

## Summary

This workflow automates monitoring transit delays, leverages AI to assess commuter impacts, generates targeted alerts for high impact situations, and notifies subscribed users across multiple channels to help commuters effectively manage their routes during disruptions.