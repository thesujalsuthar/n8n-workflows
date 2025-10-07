# AI-Powered Personal Finance & Budget Optimization Workflow

This workflow automates the process of analyzing personal financial data from Google Sheets, generating AI-powered budget optimization recommendations, and sending personalized advice via email.

---

## Workflow Overview

1. **Read Income Data**  
   Connects to a Google Sheet and reads income data from the `Income` sheet (range: `A1:C`).

2. **Read Expenses Data**  
   Connects to the same Google Sheet and reads expense data from the `Expenses` sheet (range: `A1:D`).

3. **Analyze Financial Data**  
   Processes the income and expense data:  
   - Parses and converts dates and amounts  
   - Calculates total income and total expenses  
   - Calculates net savings (`total income - total expenses`)  
   - Aggregates expenses by category  

4. **Generate Budget Recommendations**  
   Uses the OpenAI GPT-4 model as a financial advisor bot to generate:  
   - Personalized budget recommendations  
   - Tips to optimize spending  
   - Alerts for categories with unusually high expenses  

5. **Send Recommendations Email**  
   Sends the generated budget recommendations to the user's email address.

---

## Node Details

### 1. Read Income Data

- **Type:** Google Sheets (OAuth2)  
- **Operation:** Read  
- **Sheet ID:** `your-google-sheet-id` (replace with your actual sheet ID)  
- **Range:** `Income!A1:C`  
- **Credentials:** Google Sheets Account OAuth2 authentication  

### 2. Read Expenses Data

- **Type:** Google Sheets (OAuth2)  
- **Operation:** Read  
- **Sheet ID:** `your-google-sheet-id` (replace with your actual sheet ID)  
- **Range:** `Expenses!A1:D`  
- **Credentials:** Google Sheets Account OAuth2 authentication  

### 3. Analyze Financial Data

- **Type:** Function  
- **Purpose:** Processes and analyzes the input data.  
- **Function Logic:**
  - Parses income and expenses into objects with date and numeric amount values.  
  - Calculates total income, total expenses, and savings.  
  - Aggregates expenses by category for detailed breakdown.

### 4. Generate Budget Recommendations

- **Type:** OpenAI GPT-4  
- **Prompt Roles:**
  - System: Instructions for the AI to act as a financial advisor bot.  
  - User: Provides the financial summary and requests personalized recommendations.  
- **Model Parameters:**  
  - Temperature: 0.7 (balanced creativity)  
  - Max Tokens: 500  
- **Credentials:** OpenAI API Key  

### 5. Send Recommendations Email

- **Type:** Email Send  
- **To:** User email (`{{$json.email}}`) from input data  
- **Subject:** "Your Personalized Budget Recommendations"  
- **Content:** AI-generated recommendations (`{{$json.text}}`)  
- **Credentials:** SMTP Email Account  

---

## Connections & Data Flow

- Income and Expense data nodes both connect as separate inputs into the Analyze Financial Data node.
- The output of the analysis feeds into the AI model to generate recommendations.
- The AI-generated recommendations are sent via email to the user.

---

## Prerequisites

- Google Sheets with two sheets named `Income` and `Expenses`.
  - Income sheet should have columns: Date, Amount, Source  
  - Expenses sheet should have columns: Date, Amount, Category, Description
- OAuth2 credentials setup for Google Sheets API.
- OpenAI API key with access to GPT-4.
- SMTP email account credentials for sending emails.

---

## Usage Instructions

1. **Set up Google Sheets:** Populate the `Income` and `Expenses` sheets with your financial data.
2. **Configure Credentials:** Add your Google Sheets OAuth2 credentials, OpenAI API key, and SMTP email credentials in the workflow.
3. **Replace Sheet ID:** Update the Google Sheets nodes with your actual Google Sheet ID.
4. **Run the Workflow:** Execute to get personalized financial insights delivered right to your inbox.

---

## Notes

- The workflow assumes the presence of an `email` field in the input data for sending the email.
- Customize the AI prompt as needed for different tones or additional financial insights.
- Validate that the date and amount formats in your Google Sheets match expected formats for parsing.

---

## Support

For issues or customization requests, please check the workflow configurations and API credentials or contact the maintainer of this workflow.