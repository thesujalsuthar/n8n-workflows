# AI-Powered Smart Urban Garden Pest Monitoring & Eco-Friendly Treatment Scheduler

## Overview

This workflow automates the monitoring of pests in an urban garden using AI-powered pest detection and schedules eco-friendly treatments accordingly. It integrates pest detection API data, analyzes pest severity, schedules treatment events in Google Calendar, and sends alerts via SMS and email to the user.

---

## Workflow Steps

### 1. Set Garden Location
- **Type:** Set
- **Purpose:** Defines the garden location to be monitored.
- **Default Value:** `123 Main St, Example City, Country`
- **Output Variable:** `gardenLocation`

### 2. Set User Email
- **Type:** Set
- **Purpose:** Defines the email address for sending alert notifications.
- **Default Value:** `user@example.com`
- **Output Variable:** `userEmail`

### 3. Set User Phone Number
- **Type:** Set
- **Purpose:** Defines the phone number for sending SMS alerts.
- **Default Value:** `+1234567890`
- **Output Variable:** `userPhoneNumber`

### 4. Pest Detection API
- **Type:** HTTP Request (GET)
- **Purpose:** Queries an AI-powered pest detection API to analyze the garden location.
- **Endpoint:** `https://api.pestdetection.example.com/v1/detect`
- **Parameters:**
  - `location`: Uses value from `gardenLocation`
  - `timestamp`: Current ISO date-time
- **Output:** JSON containing detected pest data and severity levels.

### 5. Analyze Pest Data
- **Type:** Function
- **Purpose:** Processes pest data from the API to determine pest presence, severity, and required treatment.
- **Logic:**
  - No pests → No alert; routine monitoring recommended.
  - High severity pests (severity ≥ 7) → Trigger "corrective" treatment.
  - Other pests → Trigger "preventive" treatment.
- **Output:**
  - `alertNeeded` (boolean)
  - `treatmentType` (`corrective` or `preventive`)
  - `alertMessage` (custom text based on pest status)
  - `pestsDetected` (list of detected pests)

### 6. Schedule Treatment
- **Type:** Google Calendar (create event)
- **Calendar ID:** `eco-friendly-garden-treatments`
- **Purpose:** Automatically create an event for the eco-friendly pest treatment based on AI analysis.
- **Event Details:**
  - Summary: "Immediate Eco-friendly Treatment" or "Preventive Eco-friendly Treatment" based on treatment type.
  - Start Time: Current timestamp
  - End Time: Two hours after start time
  - Description: Contains treatment type and targeted pests.
  - Status: Confirmed
- **Requires:** Google Calendar API credentials.

### 7. Send SMS Alert
- **Type:** Twilio
- **Purpose:** Sends an SMS alert to the user with the pest alert message.
- **Recipient:** Phone number from `userPhoneNumber`
- **Message:** Generated alert message from pest analysis.
- **Requires:** Twilio API credentials.

### 8. Send Email Alert
- **Type:** Email Send
- **Purpose:** Sends an email alert to the user containing the pest alert and scheduled treatment details.
- **Recipient:** Email address from `userEmail`
- **Subject:** "Smart Urban Garden Pest Alert and Treatment Schedule"
- **Body:** Alert message, treatment type, and targeted pests.
- **Requires:** SMTP service credentials.

---

## Connections & Data Flow

1. **Set Garden Location** → **Set User Email**
2. **Set User Email** → **Set User Phone Number**
3. **Set User Phone Number** → **Pest Detection API**
4. **Pest Detection API** → **Analyze Pest Data**
5. **Analyze Pest Data** → **Schedule Treatment**, **Send SMS Alert**, **Send Email Alert**

---

## Prerequisites

- Access to the Pest Detection API with appropriate credentials (API URL and parameters).
- A Google account with API access to the calendar `eco-friendly-garden-treatments`.
- Twilio account credentials for SMS messaging.
- SMTP server account for sending email alerts.

---

## Customization

- Update the garden location, email, and phone number in the respective "Set" nodes.
- Modify API endpoints or parameters as required based on your pest detection service.
- Tailor alert messages or treatment scheduling durations in the Function node or Google Calendar event settings.
- Configure credentials for Google Calendar, Twilio, and SMTP services in the workflow.

---

## Summary

This workflow provides an end-to-end automation system for smart urban garden pest monitoring:
- Collects real-time pest data via an AI API.
- Evaluates pest severity and recommends treatment type.
- Schedules eco-friendly pest treatment on Google Calendar.
- Sends timely alerts via SMS and email, ensuring proactive garden care.

Implementing this enables efficient, sustainable pest management with minimal manual intervention.