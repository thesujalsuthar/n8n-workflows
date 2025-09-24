# Personalized Real-Time AI Air Quality Health Advisory System

## Overview
This workflow provides a personalized, real-time health advisory based on air quality data tailored to individual user health conditions. It retrieves live air quality information, analyzes potential health impacts considering user-specific conditions, and delivers customized alerts via email, SMS, and push notifications on an hourly basis.

---

## Workflow Components

### 1. Fetch Real-Time Air Quality Data
- **Type:** HTTP Request
- **Function:** Fetches the latest air quality data from the OpenAQ API.
- **Request Details:**
  - **Endpoint:** `https://api.openaq.org/v2/latest`
  - **Parameters:**
    - `city`: Determined dynamically based on user input (`userCity`), a default city (`defaultCity`), or fallback to `"San Francisco"`.
    - `limit`: Set to `1` to fetch the most recent record.
- **Purpose:** To obtain up-to-date environmental data for analysis.

---

### 2. Analyze Health Impact
- **Type:** Function
- **Function:** Analyzes air quality data alongside user health conditions to generate personalized health advisories.
- **Inputs:**
  - User conditions (defaults applied if missing):
    - Asthma (boolean)
    - Heart Disease (boolean)
    - Allergies (boolean)
    - Age (number; default 30)
  - Recent air quality measurements focusing on:
    - PM2.5 (`pm25`)
    - Ozone (`o3`)
- **Logic:**
  - Extracts `pm25` and `o3` values from API response.
  - Compares values against defined thresholds to determine health impact severity.
  - Personalizes advisories considering user health factors:
    - Asthma, heart disease, allergies, and senior age risks.
- **Output:** 
  - A combined advisory message.
  - Air quality details with measured PM2.5 and O3 values.
  - Original user conditions.

---

### 3. Send Email Notification
- **Type:** Email Send
- **Purpose:** Sends the personalized advisory to the user's email address.
- **Configurations:**
  - From: `alerts@aqhealthadvisor.com`
  - To: User’s email (`userEmail`), defaulting to `user@example.com` if not provided.
  - Subject: `"Personalized Air Quality Health Advisory"`
  - Body: Includes advisory message along with PM2.5 and O3 levels.

---

### 4. Send SMS Notification
- **Type:** Twilio SMS
- **Purpose:** Sends the advisory as an SMS message.
- **Configurations:**
  - Recipient phone number: User’s phone (`userPhone`), defaulting to `+1234567890`.
  - Message: Concatenated advisory including PM2.5 and O3 air quality values.

---

### 5. Send Push Notification
- **Type:** Pushover Notification
- **Purpose:** Delivers the advisory directly as a push notification on the user’s device.
- **Configurations:**
  - Device ID: User’s push device ID (`userPushDeviceId`), default `"device123"`.
  - Title: `"Air Quality Advisory"`
  - Message: The personalized advisory message.
  - Additional Data: Includes PM2.5 and O3 measurements.

---

### 6. Wait 1 Hour
- **Type:** Wait
- **Purpose:** Pauses the workflow for 1 hour (3600000 ms) before triggering the next cycle.
- **Function:** Enables continuous hourly updates of air quality monitoring and health advisories.

---

## Workflow Execution Flow
1. **Fetch Real-Time Air Quality Data** — Obtain latest air quality for the specified city.
2. **Analyze Health Impact** — Generate user-personalized advisories based on air quality and health data.
3. Parallel notifications:
   - **Send Email Notification**
   - **Send SMS Notification**
   - **Send Push Notification**
4. After sending notifications, workflow **Waits for 1 Hour**.
5. Upon completion of waiting, the workflow loops back to fetching air quality data and repeats continuously.

---

## Customization and Defaults
- **City selection:** User city priority → default city → `"San Francisco"` fallback.
- **User health profile:** Can be customized with `userConditions` including asthma, heart disease, allergies, and age.
- **Contact info:** Email, phone number, and push device ID default to placeholders if not provided.
- **Advisory thresholds:** Includes defined cutoffs for PM2.5 and ozone to gauge health risk severity.

---

## Use Case
Ideal for individuals wanting continuous updates on air quality risks, tailored according to their specific health sensitivities, delivered directly across multiple communication channels. This system supports proactive health management against environmental hazards.

---

## Requirements
- Access to the [OpenAQ API](https://openaq.org/) for air quality data.
- Configured email service with sender address `alerts@aqhealthadvisor.com`.
- Twilio account tied to a verified sender phone number for SMS.
- Pushover account for push notifications including device registration.

---

## Notes
- The workflow loops indefinitely at hourly intervals allowing ongoing monitoring.
- All notifications carry the same advisory content ensuring consistent messaging.
- User data and defaults safeguard smooth operation even when some inputs are missing.

---

*End of README*