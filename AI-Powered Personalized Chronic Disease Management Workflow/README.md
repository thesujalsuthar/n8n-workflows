# AI-Driven Chronic Disease Management and Personalized Care Plan Workflow

## Overview
This workflow automates the process of managing chronic diseases by utilizing AI to generate personalized care plans for patients. It collects and aggregates patient health data, symptom monitoring information, and medication adherence records. Using this comprehensive dataset, it leverages an AI model (GPT-4) to create individualized care plans that include insights on symptoms, medication adherence recommendations, monitoring schedules, and risk alerts. Urgent risk alerts are sent to healthcare providers in real time to enable immediate interventions. The workflow also updates the patient's health record with the newly generated care plan.

---

## Workflow Nodes Description

### 1. Get Patient Health Data
- **Type:** HTTP Request
- **Purpose:** Fetches the core health data of a specified patient from an external resource.
- **Key Parameter:** `patientId` — dynamically obtained from the input JSON.

### 2. Get Recent Symptom Monitoring Data
- **Type:** HTTP Request
- **Purpose:** Retrieves the last 10 symptom monitoring records for the patient.
- **Key Parameter:** `patientId`, `limit` (set to 10).

### 3. Get Medication Adherence Records
- **Type:** HTTP Request
- **Purpose:** Retrieves the last 10 medication adherence records for the patient.
- **Key Parameter:** `patientId`, `limit` (set to 10).

### 4. Aggregate Patient Data
- **Type:** Function
- **Purpose:** Aggregates data from the three previous nodes (health data, symptom data, medication adherence) into a unified JSON object for AI processing.

### 5. Generate Personalized Care Plan (AI)
- **Type:** OpenAI Node (GPT-4)
- **Purpose:** Uses the aggregated patient data as input to generate a personalized chronic disease management and care plan.
- **Model:** GPT-4
- **Temperature:** 0.4 (balanced creativity and precision)
- **Max Tokens:** 1200
- **Prompt Details:**
  - Instructs the AI to create a structured JSON care plan with keys: 
    - `personalizedPlan`
    - `riskAlerts`
    - `medicationAdjustments`
    - `monitoringSchedule`
  - Includes symptom insights, medication recommendations, monitoring plans, and warnings for urgent care.

### 6. Parse AI Care Plan and Extract Alerts
- **Type:** Function
- **Purpose:** Parses the AI-generated JSON care plan and extracts any risk alerts.
- **Logic:** If risk alerts exist, passes them to the next node for real-time notification; otherwise, no action is taken.

### 7. Send Real-Time Alerts to Provider
- **Type:** Email Send
- **Purpose:** Sends an urgent email notification containing any risk alerts to the healthcare provider for immediate intervention.
- **Email Recipient:** `healthcare_provider@example.com`
- **Email Subject:** "Urgent Patient Risk Alerts for Intervention"
- **Message Body:** Includes the patient's ID and details of the alerts in JSON format.

### 8. Update Patient Record with Care Plan
- **Type:** HTTP Request
- **Purpose:** Updates the external patient record resource with the complete AI-generated personalized care plan.
- **Key Parameter:** `patientId` and care plan payload.

---

## Data Flow and Connections

- **Data Collection:** The workflow starts by fetching three separate datasets: health data, recent symptom data, and medication adherence records for the patient.
- **Data Aggregation:** These datasets are merged into a single JSON object summarizing the patient's current status.
- **AI Processing:** The combined data is processed by GPT-4 to generate a detailed, structured care plan.
- **Risk Alert Handling:** Extracted risk alerts, if any, trigger immediate notifications to healthcare providers.
- **Record Update:** The full personalized care plan is saved back to the patient's health record for future reference and care management.

---

## Usage

- **Trigger:** The workflow is designed to operate with an input JSON containing a `patientId`.
- **Integration:** It integrates with external patient data resources and utilizes OpenAI's GPT-4 API.
- **Alerts:** Real-time email alerts facilitate quick clinical responses to urgent risk factors.
- **Personalized Care:** Providers receive a tailored approach for each patient's chronic disease management, improving outcomes and adherence.

---

## Requirements

- Access to patient health data API supporting `get` and `update` operations.
- Access to symptom monitoring and medication adherence APIs.
- OpenAI API key with GPT-4 access.
- SMTP or email service configured for sending notifications.
- Valid patient identifiers to initiate the workflow for targeted data retrieval.

---

## Summary

This workflow combines multi-source patient data with AI-driven analysis to produce comprehensive and actionable care plans. It enhances chronic disease management by delivering personalized recommendations and real-time risk alerts directly to healthcare providers, helping improve patient outcomes and enabling proactive care.