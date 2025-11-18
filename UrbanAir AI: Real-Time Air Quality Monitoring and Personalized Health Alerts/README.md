# AI-Driven Real-Time Urban Air Quality Alert and Health Advisory System

## Overview
This workflow is designed to provide real-time urban air quality monitoring, personalized health risk assessments, and alerts to users based on combined data from multiple air quality sources. It leverages AI to analyze pollution data alongside user health profiles and generates actionable health advisories. Additionally, it logs data for periodic community health impact reporting.

---

## Workflow Components

### 1. Fetch OpenAQ Data
- **Type:** HTTP Request
- **Description:** Retrieves the latest air quality measurements from the OpenAQ API (`https://api.openaq.org/v2/latest`).
- **Output:** JSON response containing recent pollutant measurements.

### 2. Fetch Weather.com Air Quality Data
- **Type:** HTTP Request
- **Description:** Fetches current air quality conditions from Weather.com API (`https://api.weather.com/v3/airQuality/currentConditions`).
- **Parameters:**
  - `apiKey`: Your Weather.com API key.
  - `geocode`: Latitude and longitude concatenated from OpenAQ data.
  - `format`: JSON response format.
- **Output:** JSON with various pollutant values under `airQuality`.

### 3. Normalize and Merge AQ Data
- **Type:** Function
- **Description:** Combines and normalizes pollutant data (PM2.5, PM10, O3, NO2, SO2, CO) from OpenAQ and Weather.com datasets into a unified structure.
- **Output:** Single JSON object with combined air quality data and timestamp.

### 4. Log Air Quality Data
- **Type:** MongoDB Insert
- **Description:** Stores the combined air quality data along with a timestamp into `air_quality_logs` collection in `urban_health_db`.
- **Purpose:** Maintains historical data for analysis and reporting.

### 5. Prepare AI Input per User
- **Type:** Function
- **Description:** Prepares personalized AI input by pairing the combined air quality data with predefined user profiles including location and health conditions.
- **Output:** Array of JSON objects, each corresponding to a user’s profile paired with pollution data.

### 6. AI Health Risk Assessment
- **Type:** HTTP Request
- **Description:** Sends pollution data and user health profiles to an external AI API (`https://api.youraihealthservice.com/analyze`) that assesses health risk levels and generates personalized recommendations.
- **Authentication:** Bearer token using AI API key.
- **Input:** JSON body with `pollutionData` and `healthConditions`.
- **Output:** AI-generated health risk level and recommendations.

### 7. Format Personalized Alert
- **Type:** Function
- **Description:** Formats the AI response into user-friendly alert messages including risk level and health recommendations personalized for each user.
- **Output:** JSON containing the alert message and userId.

### 8. Send Email Alert
- **Type:** Email Send
- **Description:** Delivers the personalized health alert message to each user via email.
- **From:** `alerts@urbanhealth.com`
- **To:** Dynamic email address based on user ID.

### 9. Send SMS Alert
- **Type:** Twilio SMS
- **Description:** Sends the alert message via SMS to the user's phone number.
- **Recipient:** Constructed dynamically using `userPhone` data.

### 10. Log User Alerts
- **Type:** MongoDB Insert
- **Description:** Logs each dispatched user alert message with timestamp in the `user_alert_logs` collection for audit and tracking.

### 11. Get Historical Data
- **Type:** MongoDB Find
- **Description:** Retrieves air quality logs from the last 30 days to enable historic data analysis.
- **Query:** Filters `air_quality_logs` collection for recent entries.

### 12. Generate Periodic Health Report
- **Type:** Function
- **Description:** Aggregates historical data to compute average PM2.5 levels and generate a community health impact report with actionable recommendations.
- **Period:** Last 30 days.

### 13. Send Community Report Email
- **Type:** Email Send
- **Description:** Sends the generated monthly health impact report to community or organization emails.
- **From:** `reports@urbanhealth.com`
- **To:** `community@urbanhealth.com`

---

## Credentials Required

- **AI_API:** API key for the AI health risk assessment service.
- **mongodb:** Connection details for MongoDB instance hosting user and air quality logs.
- **twilio:** Credentials for sending SMS alerts.
- **emailSend:** Credentials for sending emails.

---

## Data Flow Summary

1. Air quality data is fetched simultaneously from OpenAQ and Weather.com.
2. Data is merged and normalized.
3. Combined data is logged into MongoDB.
4. User profiles are paired with air quality data for AI analysis.
5. AI API assesses health risks and returns personalized recommendations.
6. Alerts are formatted and sent via email and SMS.
7. User alerts are logged for audit.
8. Historical data is periodically fetched for report generation.
9. Community health impact reports are created and emailed monthly.

---

## Configuration Notes

- Replace placeholder API keys and credentials with actual service keys before deployment.
- User profiles are hardcoded in the demo; adapt as needed for dynamic user management.
- MongoDB collections and database names are configurable.
- AI API endpoint (`https://api.youraihealthservice.com/analyze`) should be replaced with actual AI service endpoint.
- Phone numbers for SMS alerts must be properly assigned in the user profiles.

---

## Summary

This workflow provides a comprehensive solution to monitor real-time air quality, leverage AI for personalized health risk advisories, disseminate alerts via email and SMS, and generate community health impact reports based on historic data. It supports urban health initiatives by enabling informed public health decisions using multi-source data fusion and AI-driven insights.