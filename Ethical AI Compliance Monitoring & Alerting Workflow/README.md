# Ethical AI Usage Compliance Monitor & Alert System

This workflow automates the monitoring, logging, and alerting process for ethical AI usage compliance within an organization. It continuously fetches AI usage metrics, detects potential guideline violations, logs incidents to Google Sheets, and sends alert emails to compliance officers for immediate action.

---

## Workflow Overview

| Step                      | Node Name                     | Description                                                                                       |
|---------------------------|-------------------------------|---------------------------------------------------------------------------------------------------|
| 1. Fetch AI Usage Metrics  | Fetch AI Usage Metrics         | Retrieves AI tool usage metrics from the organization’s API endpoint in JSON format.               |
| 2. Detect Violations       | Detect Violations              | Analyzes usage data to identify breaches against ethical AI policies such as usage limits and banned features/datasets. |
| 3. Format Incident Logs    | Format Incident Logs           | Converts detected violations into structured log entries with timestamps.                          |
| 4. Log Incidents           | Log Incidents to Google Sheets | Records the incidents into a specified Google Sheets spreadsheet for tracking and audit purposes. |
| 5. Prepare Alert Messages  | Prepare Alert Messages         | Formats alert emails based on each violation, targeting the compliance officer's email address.    |
| 6. Send Alert Email        | Send Alert Email               | Sends notification emails to the compliance officer to prompt timely review and corrective measures. |

---

## Node Details

### 1. Fetch AI Usage Metrics  
- **Node Type:** HTTP Request  
- **Description:**  
  Fetches AI usage metrics from the API endpoint `https://api.organization.com/ai-usage/metrics`.  
- **Response Format:** JSON  

### 2. Detect Violations  
- **Node Type:** Function  
- **Purpose:**  
  Processes the retrieved metrics to detect possible ethical violations based on predefined rules:  
  - Usage counts exceeding a daily limit (threshold set at 1000).  
  - Use of banned AI features such as `facial_recognition`.  
  - Use of unapproved sensitive datasets.  
- **Output:** List of violation objects, each including the tool name and issue description.  

### 3. Format Incident Logs  
- **Node Type:** Function  
- **Purpose:**  
  Converts detected violations into log entries formatted with ISO 8601 timestamps, tool names, and issue descriptions.  
- **Note:** Skips output if no violations are detected.  

### 4. Log Incidents to Google Sheets  
- **Node Type:** Google Sheets  
- **Operation:** Create row(s)  
- **Target:** Spreadsheet named `"AI Compliance Incidents"`  
- **Fields Logged:** `timestamp`, `toolName`, `issue`  
- **Credentials Required:** Google Sheets OAuth2 credentials  

### 5. Prepare Alert Messages  
- **Node Type:** Function  
- **Purpose:**  
  Prepares individualized alert email content for each violation for the compliance officer. Default recipient can be set in global workflow static data or fallback to `compliance@organization.com`.  
- **Email Subject Template:**  
  `ALERT: Ethical AI Usage Violation Detected in [Tool Name]`  

### 6. Send Alert Email  
- **Node Type:** Email Send  
- **Purpose:**  
  Sends the alert email regarding detected violations to the compliance officer.  
- **Credentials Required:** SMTP credentials to send emails.  
- **Email Content:**    
  ```
  Dear Compliance Officer,

  The system has detected potential ethical AI guideline violations in the organization's AI tool usage:

  [Issue] in [Tool Name] at [Timestamp].

  Please review these incidents and take necessary corrective actions.

  Best regards,
  Ethical AI Compliance Monitor
  ```  

---

## Configuration Instructions

### 1. API Endpoint  
- Ensure the URL in the **Fetch AI Usage Metrics** node points to your organization’s AI usage metrics API.

### 2. Google Sheets  
- Replace `"YOUR_SPREADSHEET_ID"` in **Log Incidents to Google Sheets** with your actual Google Sheets ID.  
- Ensure the sheet name `AI Compliance Incidents` exists with columns: `timestamp`, `toolName`, `issue`.  
- Configure Google Sheets OAuth2 credentials in n8n.

### 3. Email Settings  
- Provide valid SMTP credentials in **Send Alert Email** node.  
- Set the compliance officer’s email in the workflow’s global static data under `complianceOfficerEmail` or default fallback will be used.

---

## Operational Notes

- The workflow runs sequentially, starting with data fetch, moving through violation detection, logging, and alerting.  
- If no violations are found, logging and alerting steps are skipped to avoid unnecessary notifications.  
- Logs in Google Sheets provide an audit trail for compliance review and historical analysis.  
- Alert emails enable prompt response to ethical AI compliance incidents by designated personnel.

---

## License

This workflow is provided as-is for organizations seeking to enforce ethical AI usage monitoring and alerting. Customize validation rules and notification settings as needed to fit your compliance policies.