# AI-Powered Disaster Preparedness and Real-Time Community Support System

## Overview

This workflow integrates real-time weather alerts, social media monitoring, and community resource data to provide personalized disaster preparedness tips and timely community support. Using AI-powered sentiment analysis, it gauges public sentiment on recent disaster-related events and automates notifications and support triggers to enhance community safety.

---

## Workflow Steps

### 1. Initialize Location & User Info
- **Node Type:** Function
- **Purpose:** Sets initial user-specific data including email and geographic coordinates (latitude and longitude).
- **Details:** This node outputs a static user email and location data. These values are used by subsequent nodes to fetch localized weather alerts and community resources.

---

### 2. Fetch Weather Alerts
- **Node Type:** HTTP Request
- **Purpose:** Retrieves current weather alerts for the given location via the Weather.com API.
- **Parameters:**
  - HTTP Method: `GET`
  - URL: `https://api.weather.com/v3/alerts/headlines`
  - Query Parameters:
    - `geocode`: Combines latitude and longitude from the initialized data.
    - `format`: `json`
    - `language`: `en-US`
- **Output:** Contains weather alerts relevant to the user's location.

---

### 3. Fetch Social Media Data
- **Node Type:** Twitter Search
- **Purpose:** Collects recent tweets containing disaster-related keywords from the past hour.
- **Parameters:**
  - Resource: `tweet`
  - Operation: `search`
  - Search Text: `"disaster OR earthquake OR flood OR hurricane OR wildfire"`
  - Since: Last one hour timestamp
  - Max Results: 50
- **Credentials:** Requires configured Twitter API credentials.
- **Output:** Tweets related to disaster events.

---

### 4. Fetch Community Resources
- **Node Type:** MongoDB Find
- **Purpose:** Pulls community resource information from a MongoDB collection named `community_resources`.
- **Parameters:**
  - Operation: `find`
  - Collection: `community_resources`
  - Query: `{}` (fetches all documents)
- **Credentials:** Requires MongoDB credentials to access the database.
- **Output:** Community resource data including types and locations.

---

### 5. Combine Data
- **Node Type:** Function
- **Purpose:** Merges the outputs from the three data-fetching nodes into a single JSON object.
- **Logic:**
  - Extract `alerts` from Weather Alerts node.
  - Extract `tweets` from Twitter node.
  - Extract `resources` from MongoDB node.
- **Output:** Consolidated data object with `alerts`, `tweets`, and `resources`.

---

### 6. Sentiment Analysis
- **Node Type:** Text Analysis
- **Purpose:** Performs sentiment analysis on the combined tweets' text to understand community mood and concerns.
- **Input:** Text concatenation of all disaster-related tweets.
- **Output:** Sentiment data representing tone and emotion from social media posts.

---

### 7. Generate Tips & Match Resources
- **Node Type:** Function
- **Purpose:** 
  - Generates personalized disaster preparedness tips based on the types of weather alerts.
  - Matches community resources to alert coverage areas for actionable response.
- **Logic:**
  - Detects alerts mentioning specific disasters (e.g., hurricane, flood, wildfire, earthquake) and suggests safety tips accordingly.
  - Deduplicates tips for clarity.
  - Filters community resources whose locations overlap with alert coverage areas to identify relevant support options.
- **Output:**
  - `tips`: Array of preparedness tips.
  - `sentiment`: Sentiment analysis results.
  - `matchedResources`: Array of location-relevant community resources.
  - `alerts`: Weather alerts data.

---

### 8. Send Notification Email
- **Node Type:** Email Send
- **Purpose:** Sends a real-time notification email to the user with disaster alerts, personalized tips, and available community resources.
- **Parameters:**
  - From: `community@example.org`
  - To: User's email or default email.
  - Subject: `Real-Time Disaster Alert and Preparedness Tips`
  - Text Content:
    - Lists active alerts.
    - Provides personalized preparedness tips.
    - Lists community resources with contact info.
    - Encourages safety and community connectivity.
- **Credentials:** Requires SMTP credentials to send emails.

---

### 9. Trigger Community Support Actions
- **Node Type:** HTTP Request
- **Purpose:** Notifies an external community support system via API to initiate support measures based on current alerts and matched resources.
- **Parameters:**
  - HTTP Method: `POST`
  - URL: `https://community-support.example.org/api/support-actions`
  - Body: JSON containing alerts and matched resources.
- **Output:** API response from the community support system.

---

## Connections & Data Flow

- The workflow starts with **Initialize Location & User Info**, providing user location and email.
- This data triggers in parallel:
  - **Fetch Weather Alerts**
  - **Fetch Social Media Data**
  - **Fetch Community Resources**
- Outputs of these three nodes feed into **Combine Data**.
- Combined data’s tweets text feeds the **Sentiment Analysis**.
- Sentiment analysis results and combined data are processed in **Generate Tips & Match Resources**.
- The output then bifurcates to:
  - **Send Notification Email**
  - **Trigger Community Support Actions**

---

## Prerequisites

- **Twitter API Credentials:** For accessing tweet search data.
- **MongoDB Access:** To retrieve community resource information.
- **SMTP Credentials:** For sending email notifications.
- **Weather API Access:** Access to Weather.com alerts API.
- **Community Support API:** Endpoint for triggering support actions.

---

## Notes

- The location and user email in the initialization node can be dynamically modified to target different users/areas.
- The disaster preparedness tips are customizable and can be expanded for additional disaster types.
- The workflow assumes presence of relevant community resources with accurate location metadata.
- Sentiment analysis helps gauge community anxiety or reassurance, enriching the contextual understanding.

---

## Summary

This AI-powered workflow effectively integrates multi-source data to support disaster preparedness, enabling near real-time community alerts, sentiment-driven insights, targeted safety tips, and automated community response coordination — enhancing resilience and connectivity in times of crisis.