# Real-Time AI Transit Delay Alert System

## Overview

This workflow enables real-time transit delay prediction and user alerting using AI-based analysis on transit data. It fetches live transit data, simulates AI delay predictions, matches delays to user routes, generates alternative route suggestions, and sends alerts via email and SMS to impacted users.

---

## Workflow Nodes Description

### 1. Fetch Transit Data

- **Type:** HTTP Request
- **Purpose:** Retrieves real-time transit data from a specified transit API.
- **Inputs:**
  - `transitApiUrl`: URL endpoint of the transit API (from input JSON).
  - `transitApiKey`: API key for authentication (from input JSON).
- **Method:** GET
- **Output:** JSON data containing vehicle and route status, including current delays and stops affected.

---

### 2. AI Delay Prediction

- **Type:** Function
- **Purpose:** Simulates AI-based delay prediction by analyzing current transit data.
- **Logic:**
  - Processes each vehicle's current delay.
  - Applies a dummy prediction formula adding small randomness.
  - Estimates a confidence score for each predicted delay.
- **Output:** List of predictions with:
  - `routeId`
  - `vehicleId`
  - `currentDelay`
  - `predictedDelay`
  - `confidence`
  - `stopsAffected`

---

### 3. Match Users with Delays

- **Type:** Function
- **Purpose:** Matches predicted delay information with users' subscribed transit routes.
- **User Data:** Hardcoded sample users with `userId`, `email`, `phone`, and subscribed `routes`.
- **Logic:**
  - Iterates over each user and prediction.
  - Filters alerts where predicted delay exceeds 2 minutes for user's routes.
- **Output:** Alerts containing user contact info, route, predicted delay, and confidence.

---

### 4. Generate Alternative Routes

- **Type:** Function
- **Purpose:** Provides alternative route suggestions for routes with predicted delays.
- **Logic:** Dummy implementation returning a simple text recommendation.
- **Output:** Alert enriched with `alternativeRouteSuggestion` field.

---

### 5. Send Email Alert

- **Type:** Email Send
- **Purpose:** Sends email alerts to users impacted by delays.
- **Email Details:**
  - From: `alerts@transitalerts.com`
  - To: user's email from matched alerts.
  - Subject: Includes route ID.
  - Body: Details predicted delay, confidence, and alternative routes.
- **Triggered By:** Output of the "Generate Alternative Routes" node.

---

### 6. Send SMS Alert

- **Type:** Twilio (SMS)
- **Purpose:** Sends SMS alerts to users impacted by delays.
- **Message Content:** Includes route ID, predicted delay, and alternative route suggestion.
- **Credentials:** Requires Twilio API credentials (`Twilio API` node credential).
- **Triggered By:** Output of the "Generate Alternative Routes" node.

---

## Data Flow Summary

1. **Fetch Transit Data:** Pull live transit data using provided API credentials.
2. **AI Delay Prediction:** Simulate delay predictions using AI logic.
3. **Match Users with Delays:** Identify which users are impacted by delays on their subscribed routes.
4. **Generate Alternative Routes:** Suggest alternatives for affected routes.
5. **Send Alerts:** Notify impacted users by email and SMS with delay details and suggestions.

---

## Configuration Requirements

- **Transit API:**
  - Replace `transitApiUrl` with the actual transit data endpoint.
  - Replace `transitApiKey` with a valid API key.
  
- **User Routes:**
  - Update the users array in "Match Users with Delays" node with real user data and subscribed routes.
  
- **Twilio Credentials:**
  - Add your Twilio API credentials in the workflow under the "Send SMS Alert" node credentials.
  
- **Email Sender:**
  - Customize the `fromEmail` field in "Send Email Alert" node with a valid sender email.

---

## Notes

- The AI prediction logic is currently simulated and should be replaced with integration to a real AI model or predictive service for production.
- The alternative routes generation is a placeholder; integration with routing APIs is recommended for dynamic and accurate suggestions.
- Email and SMS alerting assume properly configured and verified sender credentials.
- Delay prediction threshold is set to 2 minutes but can be adjusted as needed.

---

## License

This workflow is provided as-is for educational and prototyping purposes. Customize and extend as needed for deployment.