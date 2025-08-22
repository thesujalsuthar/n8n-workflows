# AI-Powered Personal Finance Automated Savings & Investment Planner

This workflow automates the process of analyzing your personal financial transactions and generates personalized savings and investment recommendations using AI. It fetches your transaction data, analyzes your income and expenses, and then uses OpenAI's GPT-4 to create a tailored financial plan, which is emailed to you daily.

---

## Workflow Overview

The workflow consists of the following main steps:

1. **Daily Trigger**  
   Runs the workflow automatically every day at 07:00 AM.

2. **Fetch Transactions**  
   Connects to your bank's API to fetch all transactions from the previous 7 days until today.

3. **Analyze Spending Patterns**  
   Processes the fetched transaction data to calculate total income, total expenses, and categorize income and expense amounts.

4. **Generate Savings & Investment Recommendations**  
   Sends the processed financial data to OpenAI’s GPT-4 model to generate personalized savings and investment advice.

5. **Send Recommendations Email**  
   Emails the AI-generated financial plan directly to the user.

---

## Detailed Node Descriptions

### 1. Daily Trigger  
- **Type:** Schedule Trigger  
- **Schedule:** Every day at 07:00 AM  
- **Purpose:** Automatically initiates the workflow daily without manual intervention.

### 2. Fetch Transactions  
- **Type:** HTTP Request  
- **API Endpoint:** `https://api.yourbank.com/v1/transactions`  
- **Auth:** Bearer token via `${{credentials.BankAPI.apiKey}}`  
- **Query Parameters:**  
  - `from_date`: 7 days ago (dynamic)  
  - `to_date`: current date (dynamic)  
- **Purpose:** Retrieves your bank transactions for the last 7 days for analysis.

### 3. Analyze Spending Patterns  
- **Type:** Function  
- **Functionality:**  
  - Separates income (transactions with positive amounts) and expenses (negative amounts).  
  - Calculates total income and total expenses.  
  - Categorizes income and expenses by their respective categories and sums amounts per category.  
- **Outputs:**  
  - `totalIncome`  
  - `totalExpenses`  
  - `incomeCategories` (category-wise income sums)  
  - `expenseCategories` (category-wise expense sums)  
  - Raw transactions data  

### 4. Generate Savings & Investment Recommendations  
- **Type:** OpenAI (Chat Completion)  
- **Model:** GPT-4  
- **Prompt:** Provides the AI with total income, total expenses, breakdown by categories, and transactions, requesting a personalized savings plan and investment suggestions based on spending habits.  
- **Purpose:** Leverages AI to create a tailored financial strategy considering your unique financial data.

### 5. Send Recommendations Email  
- **Type:** Email Send  
- **From:** `financebot@yourdomain.com`  
- **To:** User's email extracted either from transaction JSON or stored credentials  
- **Subject:** "Your Personalized Savings & Investment Plan"  
- **Content:** Contains the full AI-generated recommendations message.  
- **Purpose:** Delivers your personalized savings and investment plan directly to your inbox.

---

## Credentials Required

- **BankAPI:** API credentials to access your bank’s transaction data (API key).  
- **OpenAI API:** API key to utilize OpenAI's GPT-4 chat completion service.  
- **SMTP:** Email SMTP credentials used to send recommendation emails.

---

## How it Works

- Every day at 7 AM, the workflow triggers automatically.
- It fetches your recent 7 days’ transactions from your bank via the API.
- The data is analyzed to understand your income, expenses, and the categories of spending.
- This financial summary is passed to OpenAI's GPT-4 model, which generates personalized advice on how much you should be saving monthly and suggestions for investment plans tailored to your habits.
- Finally, the AI-generated plan is emailed to your inbox for your review and action.

---

## Setup Instructions

1. **Add Credentials:**  
   - Configure your Bank API key under `BankAPI` credentials.  
   - Add your OpenAI API key under `OpenAI API` credentials.  
   - Set up SMTP email credentials to enable sending emails.

2. **Update API URLs and Emails:**  
   - Make sure the transaction API endpoint and email sender addresses reflect your service/data provider.

3. **Activate Workflow:**  
   - Enable the workflow within your n8n instance to start daily automation.

---

## Customization

- **Trigger Time:** Adjust the `Daily Trigger` node time to suit your preferred schedule.  
- **Transaction Period:** Modify the `from_date` and `to_date` parameters in the `Fetch Transactions` node for different analysis windows.  
- **Email Content & Formatting:** Customize the email node subject or template for personalized communication style.

---

Use this workflow to maintain proactive control over your finances with AI-powered insights delivered right to your inbox daily!