# Real-time Air Quality Monitoring and Health Advisory System

This workflow provides real-time monitoring of air quality index (AQI) using multiple data sources, normalizes the AQI data, generates personalized health advisories based on user health profiles, and sends advisory emails to users.

---

## Workflow Overview

The system retrieves AQI data from two external APIs, merges and normalizes the data, applies health advisory logic based on the combined AQI and user health profiles, and finally sends customized email notifications to each user.

---

## Workflow Nodes Description

### 1. Get Users

- **Type:** Function
- **Purpose:** Retrieves user data records containing location (city, state, country) and health profiles (age, asthma, cardiovascular condition, outdoor activity intensity).
- **Details:** This is a mock function simulating user data retrieval. Replace this logic with actual database or API calls to fetch real users.
- **Sample Users:**
  - Alice: 30 years old, has asthma, high outdoor activity.
  - Bob: 70 years old, cardiovascular condition, low outdoor activity.

---

### 2. Prepare User Location Data

- **Type:** Function
- **Purpose:** Formats user data to extract necessary location parameters (`city`, `state`, `country`) and attaches user profiles to each record.
- **Additional:** Assigns a placeholder email if one is not present.

---

### 3. Fetch AQI OpenAQ

- **Type:** HTTP Request
- **Purpose:** Fetches the latest air quality data for the user's city from the OpenAQ API.
- **Request URL:** `https://api.openaq.org/v2/latest?city={{ $json["city"] }}`
- **Response:** JSON containing pollutant measurements (e.g., PM2.5).

---

### 4. Fetch AQI AirVisual

- **Type:** HTTP Request
- **Purpose:** Fetches city-level air quality data including AQI values from AirVisual API.
- **Request URL:**  
  `https://api.airvisual.com/v2/city?city={{ $json["city"] }}&state={{ $json["state"] }}&country={{ $json["country"] }}&key={{ $credentials.airvisualApi.apiKey }}`
- **Response:** JSON with current pollution data including AQI US Scale (`aqius`).

---

### 5. Normalize AQI Data

- **Type:** Function
- **Purpose:** Combines and normalizes AQI measurements from OpenAQ and AirVisual sources into a single standardized AQI value.
- **Logic:**
  - Extracts PM2.5 value from OpenAQ.
  - Extracts AQI US scale from AirVisual.
  - Calculates average AQI if both present; otherwise uses whichever is available.
- **Output:** A single `combinedAQI` numeric value.

---

### 6. Generate Health Advisory

- **Type:** Function
- **Purpose:** Generates personalized health advisory messages based on the combined AQI and the user's health profile.
- **Logic:**
  - Applies general advisory based on AQI thresholds:
    - AQI ≤ 50: Air quality good.
    - AQI 51–100: Moderate, sensitive individuals take caution.
    - AQI 101–150: Unhealthy for sensitive groups.
    - AQI 151–200: Unhealthy for all.
    - AQI 201–300: Very unhealthy.
    - AQI > 300: Hazardous.
  - Modifies advisory based on user profile:
    - Asthma: medication caution.
    - Cardiovascular conditions: extra precautions.
    - Age > 65: increased caution.
    - High outdoor activity with AQI > 100: reduce activity.
- **Output:** A customized advisory message string.

---

### 7. Send Advisory Email

- **Type:** Email Send
- **Purpose:** Sends the generated health advisory as an email to the user.
- **Fields:**  
  - Recipient email: Uses user's email or a placeholder if missing.  
  - Subject: "Air Quality Health Advisory"  
  - Message: The advisory message text.

- **Credentials:** Requires configured SMTP credentials.

---

## Workflow Execution Flow

1. **Get Users** retrieves all user profiles.
2. **Prepare User Location Data** formats the data for API calls.
3. Two parallel HTTP requests:
   - **Fetch AQI OpenAQ**
   - **Fetch AQI AirVisual**
4. Results from both APIs are fed into **Normalize AQI Data** to produce a combined AQI.
5. The combined AQI and user profile are passed to **Generate Health Advisory** to produce custom health advice.
6. Finally, **Send Advisory Email** dispatches the advisory message to the user's email.

---

## Setup and Configuration

- **API Keys:**  
  - Obtain an API key from AirVisual and add it as a credential in n8n (`airvisualApi.apiKey`).
- **SMTP SMTP Server:**  
  - Configure SMTP credentials in n8n for the "Send Advisory Email" node.
- **User Data Source:**  
  - Replace the "Get Users" node logic with your actual user database or API calls.
- **Email Field:**  
  - Ensure user data contains valid email addresses or modify the placeholder logic accordingly.

---

## Customization Tips

- Expand user profiles with more health conditions or preferences.
- Enhance AQI normalization with weighted averages or additional data sources.
- Add scheduling triggers to automatically run this workflow at set intervals (e.g., hourly).
- Add error handling for API failures or missing data.
- Customize email templates with richer formatting or HTML.

---

## Summary

This workflow offers a scalable and extensible solution for personalized air quality monitoring and health advisory distribution, helping users make informed decisions based on real-time environmental data and their unique health circumstances.