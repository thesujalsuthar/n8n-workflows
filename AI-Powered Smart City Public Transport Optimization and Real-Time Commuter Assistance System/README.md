# AI-Powered Smart City Public Transport Optimization and Real-Time Commuter Assistance System

This workflow is designed to optimize public transport routes in a smart city environment by leveraging real-time data and commuter preferences. It provides personalized travel suggestions, predicts delays, and sends real-time alerts to commuters via Telegram. Additionally, it includes a mechanism to check and save the workflow to a GitHub repository while preventing duplicate entries.

---

## Workflow Overview

### 1. Fetch Public Transport Real-Time Data
- **Node Type:** HTTP Request  
- **Description:** Retrieves the latest public transportation routes and their real-time data from the public transport API endpoint:  
  `https://api.publictransport.example.com/realtime/routes`  
- **Data includes:** Current route details, stops, and modes of transportation.

### 2. Fetch Traffic Conditions
- **Node Type:** HTTP Request  
- **Description:** Obtains current traffic conditions impacting public transport routes from:  
  `https://api.traffic.example.com/v1/current_conditions`  
- **Data includes:** Traffic delays, route-specific congestion info, etc.

### 3. Fetch Commuter Preferences
- **Node Type:** HTTP Request  
- **Description:** Fetches personalized commuter preferences such as preferred mode of transport from:  
  `https://api.commuterpreferences.example.com/v1/preferences`  
- **Authentication:** Uses Bearer Token (`Authorization: Bearer {{ $credentials.commuterPrefApiToken }}`)  
- **Purpose:** Customizes route optimization based on commuter choices.

### 4. Optimize Routes and Predict Delays
- **Node Type:** Function  
- **Description:**  
  Processes and merges real-time public transport data, traffic conditions, and commuter preferences to:  
  - Score routes based on preferred mode and current estimated delays.  
  - Prioritize routes with less congestion and match commuter preferred transportation modes.  
  - Sort and output the optimized list of routes with delay predictions.

- **Key logic:**  
  - Assigns higher scores to routes matching commuter preferences.  
  - Deducts points for delay minutes.  
  - Outputs optimized routes sorted by descending score.

### 5. Generate Personalized Travel Suggestions and Alerts
- **Node Type:** Function  
- **Description:**  
  Creates personalized travel suggestions by selecting the top 3 optimized routes with:  
  - Simple alert generation for delays greater than 5 minutes.  
  - A message recommending the route and mode of transportation.  
- **Output:** List of suggestions with optional delay alerts.

### 6. Send Notification
- **Node Type:** Telegram Node  
- **Description:**  
  Notifies commuters via Telegram with personalized travel suggestions and alerts.  
- **Message:** `"Your personalized travel suggestions and alerts are ready."`  
- **Authentication:** Uses Telegram API credentials.

### 7. Check Existing GitHub Repository Content
- **Node Type:** HTTP Request  
- **Description:**  
  Checks the existing workflow files in the target GitHub repository by querying:  
  `https://api.github.com/repos/yourorg/yourrepo/contents/`  
- **Authentication:** Uses GitHub OAuth2 credentials to access repository content.

### 8. Validate Workflow Uniqueness
- **Node Type:** Function  
- **Description:**  
  Validates that the workflow named "AI-Powered Smart City Public Transport Optimization and Real-Time Commuter Assistance System" does not already exist in the repository.  
- **Behavior:** Throws an error and aborts if a duplicate workflow is detected.

### 9. Save Workflow to GitHub
- **Node Type:** GitHub Node  
- **Description:**  
  Saves the current workflow JSON file to the GitHub repository under the path:  
  `/workflows/ai_smart_city_transport_optimization.json` on the `main` branch.  
- **Commit Message:** `"Add AI-Powered Smart City Public Transport Optimization workflow"`  
- **Author:** `"n8n Bot" <n8n-bot@example.com>`  
- **Authentication:** Uses GitHub API token credentials.

---

## Data Flow and Connections

1. **Parallel Data Fetch:**  
   - Fetch Public Transport Real-Time Data, Traffic Conditions, and Commuter Preferences run in parallel.

2. **Route Optimization:**  
   - All three fetched datasets are passed into the "Optimize Routes and Predict Delays" node.

3. **Suggestion Generation:**  
   - Optimized routes and commuter preferences are inputs for "Generate Personalized Travel Suggestions and Alerts".

4. **Notification:**  
   - Suggestions produced are sent to commuters via Telegram.

5. **GitHub Operations:**  
   - The existing GitHub repo contents are checked concurrently.  
   - Uniqueness validation of the workflow name, followed by saving the workflow JSON to GitHub if unique.

---

## Credentials Required

- **Commuter Preferences API Token:** For authorization header in Commuter Preferences data fetch.
- **GitHub OAuth2:** For accessing repository content.
- **GitHub API Token:** For committing workflow files to the repository.
- **Telegram API Token:** For sending notifications to commuters.

---

## Summary

This workflow provides a dynamic, AI-powered system that:

- Integrates multiple real-time data sources.
- Optimizes public transport routes based on traffic and user preferences.
- Generates personalized travel suggestions and alerts commuters of delays.
- Delivers notifications efficiently through Telegram.
- Maintains version control and workflow integrity via GitHub integration.

By using this system, smart city administrations can enhance commuter experience and streamline public transport management with up-to-date, actionable data.