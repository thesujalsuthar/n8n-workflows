# AI-Powered Personal Data Privacy Audit & Recommendation System

This workflow automates a comprehensive personal data privacy audit and provides personalized recommendations using AI. It integrates connected accounts, scans user data for privacy risks, analyzes findings through OpenAI's GPT-4, generates a detailed report, saves the report, sends it via email, schedules periodic privacy reminders, and sends push notifications for critical risks.

---

## Workflow Overview

### 1. Cron Trigger (`Cron`)
- **Purpose**: Initiates the workflow automatically every day at 09:00 AM.
- **Configuration**: Scheduled using a cron node at 9:00 each day.

### 2. Retrieve Connected Accounts (`Get Connected Accounts`)
- **Purpose**: Fetches the list of all connected social or online accounts authenticated via OAuth2 for the user.
- **Operation**: Lists all user accounts to scan for privacy audit.

### 3. Scan User Data (`Scan User Data`)
- **Purpose**: Performs a detailed scan on the user's connected accounts.
- **Scan Options**:
  - Identifies **privacy risks**.
  - Detects **exposed personal data**.
  - Flags any **unsafe data sharing**.
- **Input**: Accounts fetched from the previous node.
- **Type**: Custom Data Scanner node.

### 4. AI-Powered Analysis (`Analyze Findings AI`)
- **Purpose**: Uses GPT-4 to analyze the scanned data for vulnerabilities and privacy risks.
- **Prompt Highlights**:
  - Acts as an AI privacy auditor.
  - Reviews scan results.
  - Outputs a JSON object with three fields:
    - `summary`: Overall assessment of privacy.
    - `vulnerabilities`: List of identified risks.
    - `recommendations`: Customized actionable advice.

- **Parameters**:
  - Model: GPT-4.
  - Temperature: 0.3 (focus on accuracy).
  - Max tokens: 1000.

### 5. Generate Privacy Audit Report (`Generate Report`)
- **Purpose**: Creates a human-readable report from AI analysis.
- **Template Structure**:
  - **Summary**
  - **Identified Vulnerabilities** (listed)
  - **Recommendations** (listed)
- **Format**: Text report with clear sections.

### 6. Save Report (`Save Report`)
- **Purpose**: Saves the generated report as a text file.
- **Filename**: `Privacy_Audit_Report_YYYYMMDD_HHmm.txt` indicating date and time of generation.
- **Storage**: Utilizes the configured storage resource for file upload.

### 7. Send Report via Email (`Send Report Email`)
- **Purpose**: Emails the saved privacy audit report to the user's registered email address.
- **Email Details**:
  - Subject: "Your Personal Data Privacy Audit Report"
  - Message: Friendly notification with instructions to review the report.
  - Attachment: The generated privacy report file.

### 8. Schedule Weekly Privacy Reminder (`Schedule Reminder`)
- **Purpose**: Sets up a weekly reminder every Monday at 09:00 AM to encourage users to review and improve their privacy settings.
- **Reminder Content**:
  - Title: "Review Your Privacy Settings"
  - Message: Encouragement to maintain personal data safety.

### 9. Notify Critical Privacy Risks (`Notify Critical Risk`)
- **Purpose**: Sends an immediate push notification to the user if any critical vulnerabilities are detected.
- **Trigger Condition**: Only fires when the AI analysis reports one or more vulnerabilities.
- **Notification Content**:
  - Title: "Critical Privacy Risk Detected"
  - Message: Urges the user to check their latest privacy audit report promptly.

---

## Workflow Connections

- **Cron** triggers both **Get Connected Accounts** and **Schedule Reminder** simultaneously each day at 09:00.
- **Get Connected Accounts** feeds the list of accounts into **Scan User Data**.
- **Scan User Data** results are passed to **Analyze Findings AI** for AI assessment.
- **Analyze Findings AI** branches to:
  - **Generate Report** for report creation.
  - **Notify Critical Risk** if vulnerabilities exist.
- **Generate Report** sends output to **Save Report**.
- **Save Report** then triggers **Send Report Email** to deliver the report to the user.

---

## Usage Notes

- **Authentication**: OAuth2 authentication is required for accessing user accounts.
- **Custom Nodes**: Includes custom nodes for data scanning and reminder scheduling.
- **AI Model**: Utilizes OpenAI GPT-4 for analysis to ensure detailed and accurate privacy insights.
- **Scheduling**: Automatic daily runs and weekly reminders keep the user aware and proactive about their data privacy.
- **Notifications**: Immediate alerts for critical privacy risks keep users informed in real-time.

---

## Summary

This end-to-end automated solution empowers users to maintain their personal data privacy by conducting regular audits, delivering AI-generated insights, and facilitating actionable improvements—all through seamless integration and scheduled automation.