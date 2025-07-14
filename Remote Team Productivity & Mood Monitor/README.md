# Real-Time Remote Team Productivity and Mood Tracker

This workflow is designed to monitor and evaluate the productivity and mood of a remote team in real-time. It aggregates data from Slack messages, Google Sheets feedback, and a task management system to provide actionable insights and alerts to team managers.

---

## Workflow Overview

The workflow runs four times daily (9:00, 12:00, 15:00, and 18:00) and performs the following main actions:

1. **Fetch Slack messages** from a specific channel to measure collaboration activity.
2. **Retrieve team mood feedback** from a Google Sheets document.
3. **Get completed tasks** from a task management platform within the last day.
4. **Calculate metrics** based on gathered data.
5. **Evaluate alerts** based on these metrics.
6. **Send notifications** through email or Slack if any alerting conditions are met.

---

## Nodes Description

### 1. Scheduler  
- **Type:** Schedule Trigger  
- **Schedule:** Fires at 9:00, 12:00, 15:00, and 18:00 every day  
- **Purpose:** Initiates the workflow multiple times a day to keep metrics up-to-date.

### 2. Slack Get Messages  
- **Type:** Slack API — List Messages  
- **Authentication:** OAuth2 using Slack Account credentials  
- **Operation:** Fetches up to 100 messages from a specified `CHANNEL_ID` on Slack  
- **Filter:** Retrieves messages starting from midnight of the current day (Unix timestamp used)  
- **Purpose:** Measures collaboration and communication activity by counting messages.

### 3. Google Sheets Get Feedback  
- **Type:** Google Sheets — Read Range  
- **Credentials:** Google API account  
- **Details:** Reads data from the sheet named `Feedback`, range `A2:C`  
- **Data Format:** Expected format is rows where each contains `[employee, moodScore, comment]`  
- **Purpose:** Collects mood and feedback scores from team members for mood analytics.

### 4. Task Management Get Completed Tasks  
- **Type:** HTTP Request  
- **Authentication:** HTTP Basic Auth with user and password  
- **API call:** Retrieves tasks with status `"completed"` updated within the last 24 hours for the project `GCP_PROJECT_ID`  
- **Purpose:** Measures productivity through the count of task completions.

### 5. Calculate Metrics  
- **Type:** Function Node  
- **Function:**  
  - Aggregates:  
    - Number of completed tasks  
    - Average mood score from feedback  
    - Count of feedback entries  
    - Slack message count  
- **Output:** Returns metrics for further analysis.

### 6. Evaluate Alerts  
- **Type:** Function Node  
- **Logic:**  
  - Checks if tasks completed in the last day are fewer than 5 → alert for low productivity  
  - Checks if average mood score is below 3 → alert for low team mood  
  - Checks if Slack message count is fewer than 10 → alert for low collaboration  
- **Output:** An array of alert messages and a boolean flag indicating if any alert exists.

### 7. Send Email Alert (Disabled by Default)  
- **Type:** Email Send  
- **From:** alerts@company.com  
- **To:** manager@company.com  
- **Subject:** Team Productivity and Mood Alert  
- **Content:** The joined alert messages, sent as plain text  
- **Credentials:** SMTP service  
- **Purpose:** Sends alert notifications via email.

### 8. Slack Alert Message (Disabled by Default)  
- **Type:** Slack API — Post Message  
- **Authentication:** OAuth2 using Slack Account credentials  
- **Channel:** Same `CHANNEL_ID` used in fetching messages  
- **Message:** Posts alerts in Slack formatted with warning emoji and newline separation  
- **Purpose:** Sends alert notifications directly to Slack for quick awareness.

---

## Connections Flow

- **Scheduler → Slack Get Messages, Google Sheets Get Feedback, Task Management Get Completed Tasks**  
  Triggers parallel data fetching at scheduled times.

- **Slack Get Messages, Google Sheets Get Feedback, Task Management Get Completed Tasks → Calculate Metrics**  
  Aggregates all fetched data into unified metrics.

- **Calculate Metrics → Evaluate Alerts**  
  Applies alert rules to the calculated team metrics.

- **Evaluate Alerts → Send Email Alert, Slack Alert Message**  
  Sends alerts if thresholds are breached. Both alert outputs are disabled by default and can be enabled as needed.

---

## Setup Instructions

1. **Slack Configuration**  
   - Create and configure a Slack app with OAuth2 to enable API access.  
   - Replace `CHANNEL_ID` with your team's Slack channel ID.  
   - Provide Slack OAuth2 credentials in n8n.

2. **Google Sheets Configuration**  
   - Create a Google Sheet named with a `Feedback` tab.  
   - Collect team feedback in columns A (Employee), B (Mood Score), and C (Comment).  
   - Replace `GOOGLE_SHEET_ID` with your Google Sheet ID.  
   - Provide Google Sheets API credentials in n8n.

3. **Task Management API**  
   - Configure your task management system to allow API access.  
   - Replace `GCP_PROJECT_ID`, `API_USER`, and `API_PASSWORD` accordingly.  
   - Ensure the API response includes tasks with a completion status and updated date.

4. **Email/SMS Alerts (Optional)**  
   - Set up SMTP credentials for email alerts if required.  
   - Enable the **Send Email Alert** node accordingly.  
   - Alternatively, enable **Slack Alert Message** for Slack-based notifications.

5. **Adjust Thresholds**  
   - Modify alerting thresholds in the **Evaluate Alerts** node's function code depending on organizational needs.

---

## Summary

This workflow supports proactive management of remote teams by continuously monitoring productivity and morale using integrated data sources and delivering timely alerts to managers via email or Slack. The modular design allows for easy customization and scaling.