# Neighborhood AI-Powered Community Safety and Alert System

## Overview

This workflow aggregates real-time data from multiple public safety and weather sources, analyzes neighborhood risk levels using AI, and sends tailored SMS alerts to residents and local authorities. It aims to enhance community awareness and prompt timely actions to ensure safety.

---

## Workflow Steps

### 1. Fetch Crime Data

- **Node Type:** HTTP Request
- **Purpose:** Collects data from three key sources:
  - Local crime incidents (`https://api.localcrime.com/v1/incidents`)
  - Public safety reports (`https://api.publicsafetydata.org/v2/reports`)
  - Weather-related alerts (`https://api.weatheralerts.gov/v1/alerts`)

The node performs parallel API requests to fetch the latest data necessary for risk assessment.

---

### 2. Aggregate Data

- **Node Type:** Function
- **Purpose:** Combines the fetched data into a unified dataset with three arrays:
  - `crimeData` (local crime incidents)
  - `safetyReports` (public safety reports)
  - `weatherAlerts` (weather alerts)

This aggregation simplifies downstream processing by structuring disparate datasets into one JSON object.

---

### 3. AI Risk Assessment & Insights

- **Node Type:** OpenAI (GPT-4)
- **Purpose:** Analyzes the aggregated data to assess neighborhood risk levels and generate actionable insights.
- **Details:**
  - **Input:** Unified dataset of crime incidents, safety reports, and weather alerts.
  - **Output:** JSON object containing:
    - `riskLevel`: One of `Low`, `Moderate`, `High`, or `Critical`
    - `insights`: Array of unique actionable insights based on crime trends, hotspots, time of day, and current alerts
    - `hotspotAreas`: List of identified high-risk locations (names or coordinates)
    - `recommendedActions`: Specific recommendations targeting residents and local authorities

The AI model operates with a low temperature (0.3) for consistent and factual analysis, with up to 1000 tokens for detailed output.

---

### 4. Filter Recipients By Role

- **Node Type:** Function
- **Purpose:** Defines and filters the recipient list for notifications.
- **Recipients List:**
  - Residents: phone numbers tagged with role `resident`
  - Local Authorities: phone numbers tagged with role `local_authority`

This node filters recipients based on their role for tailored messaging.

---

### 5. Split Residents and Authorities

- **Node Type:** Split In Batches
- **Purpose:** Splits the filtered recipient list into batches for separate handling of residents and authorities.

---

### 6. Tailor Notifications

- **Node Type:** Function
- **Purpose:** Creates customized SMS notification messages tailored for each recipient role.
- **Logic:**
  - For **Residents**:
    - Displays neighborhood risk level
    - Lists recommendations specific to residents
    - Highlights identified safety hotspots
  - For **Authorities**:
    - Displays neighborhood risk level
    - Lists recommended actions for authorities
    - Indicates hotspot areas for resource deployment
  - For other roles (default):
    - Provides a general neighborhood update with risk level and insights

The messages combine AI-generated insights with static role-based filters to ensure relevance.

---

### 7. Send SMS Notifications

- **Node Type:** Twilio
- **Purpose:** Sends the tailored SMS alerts to each recipient using Twilio API.
- **Details:**
  - Operation: Send message
  - Recipient phone number dynamically injected based on prepared messages
- **Credential:** Uses configured Twilio API credentials under `twilio_account`.

---

## Summary

This workflow provides a scalable and intelligent community safety system that:

- Integrates multiple public data sources for comprehensive situational awareness.
- Utilizes AI to interpret complex datasets and estimate neighborhood risk dynamically.
- Sends targeted SMS alerts to residents and local authorities with actionable recommendations.
- Facilitates proactive safety measures through timely communication.

---

## Prerequisites

- API access to:
  - Local crime incidents
  - Public safety report services
  - Weather alert feeds
- OpenAI API access (GPT-4) with appropriate credentials
- Twilio account configured for SMS sending
- Recipient contact list updated with roles (residents & authorities)

---

## Configuration Notes

- API URLs in "Fetch Crime Data" node can be customized to fit local data sources.
- Recipient list in "Filter Recipients By Role" node should be updated with valid contacts.
- Twilio phone number and credentials must be properly set in the "Send SMS Notifications" node.
- AI prompt and parameters can be adjusted for different risk analysis needs.

---

## License

This workflow and associated code snippets are provided as-is for community safety automation purposes. Modify and adapt as needed to serve your local neighborhood requirements.