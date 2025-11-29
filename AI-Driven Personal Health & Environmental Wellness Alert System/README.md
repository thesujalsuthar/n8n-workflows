# AI-Powered Personal Health and Wellness Tracker with Real-Time Environmental Alerts

This workflow uses multiple APIs and services to provide personalized health and wellness recommendations combined with real-time environmental alerts via email and SMS.

---

## Overview

The workflow aggregates data from fitness and sleep trackers, along with air quality and noise level sensors. It then analyzes this data using custom logic and sends personalized recommendations and environmental alerts to the user via email and SMS.

---

## Workflow Components

### 1. Fitness Tracker API

- **Type**: HTTP Request (OAuth2 Authentication)
- **Purpose**: Fetches user fitness data such as steps count.
- **Authentication**: OAuth2 credentials named `fitnessTrackerOAuth2`.
- **Output**: JSON containing fitness metrics.

### 2. Sleep Data API

- **Type**: HTTP Request (OAuth2 Authentication)
- **Purpose**: Retrieves user sleep metrics including hours slept.
- **Authentication**: OAuth2 credentials named `sleepDataOAuth2`.
- **Output**: JSON with sleep details.

### 3. Air Quality Sensor API

- **Type**: HTTP Request (No authentication)
- **Purpose**: Gets the latest air quality index (AQI).
- **Endpoint**: `https://api.airquality.example.com/latest`
- **Output**: JSON containing AQI data.

### 4. Noise Level Sensor API

- **Type**: HTTP Request (No authentication)
- **Purpose**: Fetches current environmental noise levels in decibels.
- **Endpoint**: `https://api.noiselevel.example.com/current`
- **Output**: JSON with noise level data.

### 5. AI-Powered Analysis (Function Node)

- **Type**: Function
- **Purpose**: Analyzes aggregated data and generates actionable recommendations and alerts.
- **Logic Overview**:
  - **Fitness Data**: Recommends increasing daily steps if below 5000.
  - **Sleep Data**: Advises on sleep duration if less than 7 hours.
  - **Air Quality**: Issues alert if AQI exceeds 100.
  - **Noise Levels**: Issues alert if noise levels exceed 70 dB.
- **Output**: JSON object containing arrays for both `recommendations` and `alerts`.

### 6. Send Email

- **Type**: Email Send
- **Purpose**: Emails the user personalized recommendations and alerts.
- **From**: `your-email@example.com` (replace with your actual sender email)
- **To**: User email dynamically fetched from data (`userEmail`), fallback to `user@example.com`.
- **Subject**: "Personal Health Recommendations and Environmental Alerts"
- **Body**:
  - Includes health recommendations if any.
  - Lists environmental alerts if present.
  - Defaults to a positive message if no alerts exist.
- **Credentials**: SMTP credentials identified by `smtpCredentialsId`.

### 7. Send SMS

- **Type**: Twilio
- **Purpose**: Sends real-time environmental alerts as SMS.
- **From**: +1234567890 (replace with your Twilio number)
- **To**: User phone number (`userPhone`), defaults to `+10000000000` if missing.
- **Message**:
  - Sends alert messages concatenated by semicolons if any.
  - Sends a message indicating no alerts if none are present.
- **Credentials**: Twilio API credentials identified by `twilioCredentialsId`.

---

## Data Flow and Connections

1. **Fitness Tracker API**, **Sleep Data API**, **Air Quality Sensor API**, and **Noise Level Sensor API** run independently to fetch user and environment data.
2. All outputs are sent to the **AI-Powered Analysis** node which processes the combined data.
3. The analysis results are simultaneously sent to both the **Send Email** and **Send SMS** nodes.
4. The user receives health recommendations and environmental alerts via both email and SMS in near real-time.

---

## Setup Instructions

1. **OAuth2 Credential Setup:**
   - Configure OAuth2 authentication for Fitness Tracker API (`fitnessTrackerOAuth2`) and Sleep Data API (`sleepDataOAuth2`) with your respective provider credentials.

2. **SMTP Configuration:**
   - Add your SMTP credentials to `smtpCredentialsId`.
   - Update the `fromEmail` address to your verified sender email.

3. **Twilio Configuration:**
   - Input your Twilio API credentials into `twilioCredentialsId`.
   - Replace the `from` phone number with your Twilio phone number.

4. **User Contact Data:**
   - Ensure the fetched API data includes either `userEmail` and `userPhone` or adjust default fallback values accordingly.

5. **API Endpoints:**
   - Verify and update the Air Quality and Noise Level sensor API URLs as necessary.

---

## Notes

- This workflow is designed to run as a scheduled or trigger-based automation in n8n.
- Modify thresholds and recommendation messages in the function node as needed based on user needs or updated health guidelines.
- Ensure all credentials are securely stored in n8n credential manager.

---

## License

This workflow template is provided as-is for personal and commercial use. Please comply with all third-party API terms of use.