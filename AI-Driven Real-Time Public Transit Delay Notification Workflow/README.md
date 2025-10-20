# AI-Powered Real-Time Public Transit Delay Alert System

## Overview

This workflow monitors public transit vehicle positions in real-time, utilizes AI to analyze delays, and sends alert notifications via email and SMS for significant delays. The AI suggests alternative routes when delays exceed a threshold, enhancing commuter experience by providing timely and actionable information.

---

## Workflow Nodes Description

### 1. Fetch Real-Time Transit Data
- **Type:** HTTP Request  
- **Function:** Retrieves real-time vehicle position and delay data from the public transit API endpoint:  
  `https://api.publictransit.example.com/realtime/vehicle_positions`  
- **Details:**  
  - Accepts JSON responses.  
  - Retries up to 3 times on failure before stopping.  

### 2. Extract Delay Data
- **Type:** Function  
- **Function:** Extracts and formats relevant delay information from the fetched data.  
- **Extracted Fields:**  
  - Vehicle `id`  
  - Transit `route`  
  - `delaySeconds` (delay in seconds)  
  - `currentStop`  
  - `nextStops` (used for suggesting alternatives)  
- **Output:** JSON array of delay objects for AI processing.

### 3. Analyze Delays with AI
- **Type:** OpenAI API (GPT-4)  
- **Function:**  
  - Sends delay data to GPT-4 for analysis.  
  - AI filters delays greater than 120 seconds.  
  - Generates alerts containing:  
    - `id`  
    - `route`  
    - `delaySeconds`  
    - `currentStop`  
    - `alternativeRoutes` (suggested alternative routes using `nextStops`)  
- **System prompt:** Defines AI role as assistant analyzing public transit delays and recommending alternatives.

### 4. Parse AI Response
- **Type:** Function  
- **Function:** Parses the AI's JSON-formatted response.  
- **Output:** Individual alert objects extracted from AI response.

### 5. Check for Alerts
- **Type:** Function  
- **Function:** Checks if any alerts were returned by AI.  
- **Behavior:**  
  - If no alerts, returns empty (terminates alert notifications).  
  - Otherwise, passes alerts down the workflow.

### 6. Split Alerts
- **Type:** Split In Batches  
- **Function:** Splits alert array into separate items (batch size = 1) to process each alert individually for notifications.

### 7. Send Email Notification
- **Type:** Email Send  
- **Function:** Sends delay alert emails to commuters.  
- **Email Details:**  
  - From: `alerts@publictransit.example.com`  
  - To: Public transit commuter's email or fallback `commuter@example.com`  
  - Subject: "Transit Delay Alert for Route {route}"  
  - Body includes route details, delay time, current stop, and alternative route suggestions.  
- **Failure Handling:** Continues workflow even if email fails.

### 8. Send SMS Notification
- **Type:** Twilio SMS  
- **Function:** Sends delay alert SMS messages to commuters.  
- **SMS Details:**  
  - To: Commuter phone number or fallback `+10000000000`  
  - Message includes route, delay seconds, current stop, and alternative routes.  
- **Failure Handling:** Continues workflow even if SMS fails.

### 9. No Delay Alerts
- **Type:** No-op  
- **Function:** Placeholder node for cases where no alerts are generated (no significant delays).

---

## Connection Flow

1. **Fetch Real-Time Transit Data** → Extract Delay Data  
2. Extract Delay Data → Analyze Delays with AI  
3. Analyze Delays with AI → Parse AI Response  
4. Parse AI Response → Check for Alerts  
5. Check for Alerts → Split Alerts  
6. Split Alerts → Send Email Notification AND Send SMS Notification  

---

## Summary

This workflow automates the ingestion of transit vehicle data, leverages AI for intelligent delay analysis and alternative route suggestion, and reliably informs commuters via email and SMS about significant delays in near real-time. It handles errors gracefully, retries API calls, and ensures no unnecessary alerts are sent when delays are minimal.