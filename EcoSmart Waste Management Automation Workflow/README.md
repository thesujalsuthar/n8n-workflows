# EcoSmart Waste Management Automation

## Overview

The **EcoSmart Waste Management Automation** workflow automates the process of receiving waste data, analyzing it using AI-driven logic, sending alerts based on defined thresholds, and recording all data results into Google Sheets for monitoring and reporting.

---

## Workflow Components

### 1. Webhook Receive Waste Data

- **Type:** Webhook (POST)
- **Path:** `/waste-data`
- **Function:** Receives incoming waste management data payloads via HTTP POST requests.
- **Response Mode:** Responds immediately upon data receipt.

---

### 2. Analyze Waste Data

- **Type:** Function Node
- **Purpose:** Processes the incoming JSON data to extract key metrics and calculate an alert level.
- **Logic:**
  - Extracts `volume` (waste volume in kilograms) and `recyclingRate` (percentage).
  - Applies AI-inspired rules:
    - If `recyclingRate` is below 30%, alert level is **High**.
    - Else if `wasteVolume` exceeds 500 kg, alert level is **Medium**.
    - Otherwise, alert level is **Normal**.
  - Appends a timestamp in ISO format.

---

### 3. Check Alert Level

- **Type:** If Condition Node
- **Purpose:** Determines the workflow path based on the alert level.
- **Condition:**
  - Proceeds to send an alert if `alertLevel` is anything other than `Normal`.
  - Otherwise, logs normal operation information.

---

### 4. Send Alert Email

- **Type:** Email Send Node
- **Trigger:** Activated when alert level is `High` or `Medium`.
- **Email Settings:**
  - **From:** `alerts@ecosmartwaste.com`
  - **To:** `operations@ecosmartwaste.com`
  - **Subject:** `Urgent: Waste Management Alert - [Alert Level] Level`
  - **Body:** Includes alert level, waste volume (kg), recycling rate (%), and timestamp.
- **Purpose:** Notifies the operations team to take immediate action.

---

### 5. Normal Operation Log

- **Type:** Function Node
- **Trigger:** Activated when alert level is `Normal`.
- **Output:** Logs a message indicating the waste data is within normal parameters with a timestamp.

---

### 6. Record Data to Google Sheets

- **Type:** Google Sheets Append Node
- **Sheet ID:** `1a2b3c4d5e6f7g8h9i0j`
- **Sheet Range:** `Sheet1!A:E`
- **Inputs Recorded:**
  - Timestamp
  - Waste Volume (kg)
  - Recycling Rate (%)
  - Alert Level
  - Message (if any; used for normal operation logs)
- **Purpose:** Maintains a persistent record of all waste data and associated statuses for historical tracking and analysis.

---

## Workflow Execution Flow

1. **Data Input:** The workflow receives waste data via the webhook (`/waste-data`).
2. **Data Analysis:** The received data is analyzed to calculate waste volume, recycling rate, and an alert level.
3. **Decision:**
   - If alert level is `High` or `Medium`:
     - Sends an alert email to operations.
     - After sending the email, logs the entry into Google Sheets.
   - If alert level is `Normal`:
     - Logs a normal operation message.
     - Saves the data and message into Google Sheets.
    
---

## Credentials Required

- **Google Sheets OAuth2 Credential:** Must be configured in n8n to allow appending data to Google Sheets.

---

## Notes

- The alert email sender (`alerts@ecosmartwaste.com`) and recipient (`operations@ecosmartwaste.com`) emails should be updated to valid operational addresses.
- The Google Sheets ID must correspond to a valid sheet accessible via the provided OAuth credentials.
- The AI-driven logic in the analysis node is a simulated example and can be enhanced with real AI or machine learning models as needed.

---

## Activation

This workflow is initially **inactive** and should be activated within n8n after configuring all necessary credentials and environment settings.

---

## Contact & Support

For questions or support regarding this workflow, please contact the EcoSmart Waste Management development team.