# Daily Expense Tracker Workflow

This workflow automates the process of extracting daily expense information from your Gmail inbox and recording it into a Google Sheets document for easy tracking and management.

---

## Workflow Overview

1. **Gmail Get Receipts**  
   Fetches unread emails from the Gmail inbox that contain the keyword "receipt" in the subject line.

2. **Extract & Categorize**  
   Extracts date, amount, vendor, and category information from the email content using custom parsing logic. Categorizes expenses into predefined groups based on keywords.

3. **Filter Valid Expenses**  
   Filters out any emails that do not contain a valid expense amount.

4. **Add Row to Google Sheets**  
   Adds the extracted and categorized expense data as a new row in a specified Google Sheet.

5. **Mark Emails Read**  
   Marks the processed emails as read in Gmail to avoid duplicate processing.

---

## Nodes Description

### 1. Gmail Get Receipts

- **Type:** Gmail node  
- **Operation:** Get all messages  
- **Filters:**  
  - Subject contains "receipt"  
  - Emails are unread  
- **Limit:** 20 emails at a time  
- **Attachments:** Not downloaded  
- **Credentials:** Requires Gmail OAuth2 API credentials  
- **Purpose:** Retrieve the latest receipt emails for processing.

### 2. Extract & Categorize

- **Type:** Function node  
- **Function:**  
  - Parses each email’s plain text or HTML content to extract the transaction amount using regex (supports `$` currency symbols).  
  - Extracts vendor name from the sender or the subject line.  
  - Extracts the date from the email metadata or uses the current date if unavailable.  
  - Categorizes each expense into one of the following based on keywords:  
    - Food (restaurant, cafe, coffee, food)  
    - Transport (uber, lyft, taxi, bus, metro, train)  
    - Utilities (electricity, water, gas, internet, utility)  
  - Defaults to "Uncategorized" if no keywords match.

### 3. Filter Valid Expenses

- **Type:** Filter node  
- **Condition:** Passes only emails where the amount extracted is not empty or null.  
- **Purpose:** Ensures only valid expenses are recorded.

### 4. Add Row to Google Sheets

- **Type:** Google Sheets node  
- **Operation:** Append values to a Google Sheet  
- **Sheet ID:** Replace `"YOUR_GOOGLE_SHEETS_ID"` with your actual Google Sheets ID  
- **Range:** `Sheet1!A:D` (Date, Amount, Category, Vendor)  
- **Value Input Mode:** User entered (allows formulas and number formatting)  
- **Data Added:**  
  - Date  
  - Amount  
  - Category  
  - Vendor  
- **Credentials:** Requires Google Sheets OAuth2 API credentials  
- **Purpose:** Records each validated expense entry into the spreadsheet.

### 5. Mark Emails Read

- **Type:** Gmail node  
- **Operation:** Modify message to mark it as read  
- **Credentials:** Requires Gmail OAuth2 API credentials  
- **Purpose:** Marks processed emails as read to prevent re-processing on the next workflow run.

---

## Setup Instructions

1. **Gmail API Credentials**  
   - Set up OAuth2 credentials for Gmail and add them to your n8n credentials as `Gmail OAuth2 API`.  
   - Ensure necessary Gmail API scopes are enabled for reading and modifying messages.

2. **Google Sheets API Credentials**  
   - Create OAuth2 credentials for Google Sheets and add them to n8n as `Google Sheets OAuth2 API`.  
   - Share the target Google Sheet with the account authorized by the credentials.

3. **Google Sheet Preparation**  
   - Create a new Google Sheet with at least four columns: Date, Amount, Category, Vendor.  
   - Note the Sheet ID (found in the Google Sheet URL) and replace `"YOUR_GOOGLE_SHEETS_ID"` in the workflow node.

4. **Workflow Deployment**  
   - Import this workflow JSON into n8n.  
   - Set up the necessary credentials.  
   - Activate the workflow or run it manually to start processing expense emails.

---

## Customization

- **Category Keywords:** Modify the `categoryList` array in the "Extract & Categorize" function to add or change expense categories and related keywords.
- **Email Filters:** Adjust the Gmail node filter settings to widen or narrow the emails fetched (e.g., change folder, subject keyword).
- **Sheet Range:** Adjust the Google Sheets node `range` if your sheet structure differs.

---

## Notes

- The amount extraction uses basic regex supporting USD and similar currency formats with `$` symbol only.
- The workflow only processes up to 20 unread receipt emails per run.
- Emails without a detected amount will be ignored and left unread.
- Always review permissions and security when granting OAuth2 access to your accounts.

---

## License

This workflow is provided as-is for personal and commercial use. No warranties implied. Modify according to your needs.