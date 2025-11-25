# Expense Categorization and Daily Summary Workflow

This workflow automates the process of submitting expense entries, categorizing them using AI, storing them in Google Sheets, and sending a daily summary via email and Slack.

---

## Workflow Overview

### 1. Expense Submission via Webhook
- **Node:** `Webhook`
- **Method:** POST
- **Endpoint:** `/submit-expense`
- **Purpose:** Receives expense data (`description` and `amount`) via HTTP POST requests.

### 2. Prompt Preparation for AI Categorization
- **Node:** `Prepare Prompt`
- **Function:** Extracts the `description` and `amount` from the incoming data and creates a prompt for the AI model to categorize the expense.

### 3. AI Categorization
- **Node:** `AI Categorize Expense`
- **Model:** `gpt-4o-mini`
- **Role:** Acts as an assistant to categorize expenses into one of the following categories:
  - Food
  - Transportation
  - Utilities
  - Entertainment
  - Health
  - Other
- **Input:** Prompt generated in the previous node.
- **Temperature:** Set to 0 (deterministic responses)

### 4. Extracting Category from AI Response
- **Node:** `Extract Category`
- **Function:** Parses the AI response to extract the category text and merges it back into the workflow data.

### 5. Storing Expense in Google Sheets
- **Node:** `Append to Google Sheets`
- **Operation:** Append new expense entries to Google Sheets.
- **Sheet:** Replace `YOUR_GOOGLE_SHEET_ID` with your actual Google Sheet ID.
- **Range:** `Sheet1!A:D`
- **Columns Stored:**
  - Date (ISO format, e.g., 2024-06-01)
  - Description
  - Amount
  - Category
- **Credentials:** Requires Google Sheets OAuth2 setup (`GOOGLE_SHEETS_CREDENTIALS`).

---

## Daily Expense Summary

### 6. Daily Trigger
- **Node:** `Daily Trigger`
- **Schedule:** Runs once daily at 23:59 (11:59 PM) using a cron expression.

### 7. Prepare Summary Date
- **Node:** `Prepare Summary Date`
- **Function:** Retrieves the current date in ISO format to filter expense entries.

### 8. Retrieve All Expenses from Google Sheets
- **Node:** `Get All Expenses`
- **Operation:** Fetches all expense data from the same Google Sheet.
- **Sheet:** Replace `YOUR_GOOGLE_SHEET_ID` with your Google Sheet ID.
- **Range:** `Sheet1!A:D`
- **Credentials:** Uses the same Google Sheets OAuth2 credentials.

### 9. Generate Daily Summary
- **Node:** `Generate Summary`
- **Function:**
  - Filters expense entries matching the current date.
  - Aggregates amounts by category.
  - Constructs a textual summary formatted as:
    ```
    Daily Expense Summary for YYYY-MM-DD
    Food: $XX.XX
    Transportation: $XX.XX
    Utilities: $XX.XX
    Entertainment: $XX.XX
    Health: $XX.XX
    Other: $XX.XX
    ```

### 10. Send Summary via Email
- **Node:** `Send Email Summary`
- **From:** `no-reply@yourdomain.com` (replace with your sending address)
- **To:** `USER_EMAIL` (replace with recipient’s email)
- **Subject:** `Daily Expense Summary`
- **Body:** The generated daily summary text.
- **Credentials:** Requires SMTP credentials (`SMTP_CREDENTIALS`).

### 11. Send Summary via Slack
- **Node:** `Send Slack Summary`
- **Channel:** `YOUR_SLACK_CHANNEL` (replace with your Slack channel)
- **Text:** The generated daily summary.
- **Credentials:** Requires Slack API credentials (`SLACK_CREDENTIALS`).

---

## Credentials and Setup

- **Google Sheets OAuth2 API:** Setup is required with access to the target spreadsheet.
- **SMTP Credentials:** For sending emails.
- **Slack API Credentials:** For posting messages to your Slack workspace.

---

## Configuration Steps

1. Replace all placeholder values in nodes:
   - `YOUR_GOOGLE_SHEET_ID` with your Google Sheets ID.
   - `USER_EMAIL` with the recipient email address.
   - `no-reply@yourdomain.com` with your valid sending email address.
   - `YOUR_SLACK_CHANNEL` with your Slack channel name or ID.

2. Configure and add required credentials:
   - Google Sheets OAuth2 credentials as `GOOGLE_SHEETS_CREDENTIALS`.
   - SMTP credentials as `SMTP_CREDENTIALS`.
   - Slack API credentials as `SLACK_CREDENTIALS`.

3. Deploy and activate the workflow in n8n.

---

## Usage

- Submit expenses by sending a POST request to your n8n webhook endpoint `/submit-expense` with JSON payload:
  ```json
  {
    "description": "Lunch at cafe",
    "amount": 15.50
  }
  ```
- The workflow will automatically categorize and store the expense.

- At 23:59 daily, the workflow sends a summary of the day's expenses both via email and Slack.

---

## Summary

This n8n workflow provides an automated solution to:

- Accept expense entries via webhook.
- Categorize expenses using AI.
- Store records in Google Sheets.
- Produce and send a daily expense summary by email and Slack.

It helps streamline expense tracking and reporting with minimal manual effort.