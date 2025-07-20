# AI-Driven Personal Finance and Budget Optimization Assistant

This workflow is designed to help users record their expenses, retrieve spending data, and receive AI-driven insights and personalized budgeting advice via OpenAI's GPT-4 model. It leverages HTTP endpoints for interaction, stores data in MongoDB, categorizes expenses, and uses AI to generate financial insights and optimization strategies.

---

## Features

- **Record Expenses:** Users can post expense data which is validated and stored.
- **Retrieve Expenses:** Fetch user's all recorded expenses.
- **Categorize Expenses:** Automatic categorization based on amount thresholds.
- **Summarize Spending:** Aggregates total spend by category.
- **AI Finance Insight:** Generates a summary of spending habits, budgeting tips, and savings strategies through GPT-4.
- **Budgeting & Savings Advice:** Provides detailed tips and strategies upon request from another endpoint.

---

## Workflow Nodes Overview

### 1. HTTP Request - Record Expense

- **Type:** HTTP Trigger (POST)
- **Endpoint:** `/record-expense`
- Accepts JSON containing at least:
  - `amount` (required)
  - `userId` (required)
  - `description` (optional)
  - `date` (optional; defaults to current ISO date)
- Responds immediately with success confirmation.

---

### 2. Validate & Format Expense

- Validates presence of `amount` and `userId`.
- Constructs an expense object:
  - `id` - timestamp-based unique identifier.
  - `amount`
  - `category` - initialized as `null`.
  - `description`
  - `date`
  - `userId`
- Throws an error if any required fields are missing.

---

### 3. Save Expense to Database

- **Type:** MongoDB Upsert
- Saves or updates the expense in the `expenses` collection using the generated `id`.
- Requires configured MongoDB credentials.

---

### 4. HTTP Request - Get Expenses

- **Type:** HTTP Trigger (GET)
- **Endpoint:** `/get-expenses`
- Accepts query parameter `userId`.
- Responds with all user expenses fetched from MongoDB.

---

### 5. Fetch User Expenses

- Retrieves expenses for the given `userId`.
- Sorts results by date in descending order.
- Limits to 1000 records.

---

### 6. Categorize Expenses

- Categorizes expenses based on `amount`:
  - `< $20` → Small Purchase
  - `< $100` → Moderate Spend
  - `< $500` → Large Expense
  - `>= $500` → Major Investment
- Updates expense items with a new `category` property.

---

### 7. Summarize Spending by Category

- Aggregates total amounts spent per category.
- Constructs an object mapping category names to their corresponding amounts.

---

### 8. AI Finance Insight

- Sends the aggregated spend data to OpenAI GPT-4.
- Prompt instructs the AI to provide:
  - A summary of spending habits.
  - Tailored budgeting tips.
  - Personalized savings optimization strategy.
- Outputs a JSON object with:
  - `summary`
  - `budgetingTips`
  - `savingsStrategy`
- Requires OpenAI API credentials.

---

### 9. HTTP Request - Get Finance Advice

- **Type:** HTTP Trigger (GET)
- **Endpoint:** `/finance-advice`
- Accepts query parameter `userId`.
- Responds with AI-generated detailed budgeting tips and savings strategies.

---

### 10. Fetch Expenses For Advice

- Queries MongoDB for all expenses for the provided `userId`.

---

### 11. Categorize for Advice

- Same categorization logic as Node 6 applied to expenses fetched for advice.

---

### 12. Summarize for Advice

- Aggregates spending by category as in Node 7.

---

### 13. Build AI Prompt

- Constructs a customized prompt string incorporating summarized spending data.
- Requests an AI response JSON including:
  - `budgetingTips`
  - `savingsStrategy`

---

### 14. OpenAI Budgeting & Savings Advice

- Sends the built prompt to GPT-4 with similar parameters as Node 8.
- Returns AI-generated budgeting and savings advice JSON.
- Requires OpenAI API credentials.

---

## Setup Instructions

1. **MongoDB Credentials:** Configure MongoDB credentials in n8n with access to the database containing the `expenses` collection.

2. **OpenAI API Credentials:** Configure OpenAI API key for GPT-4 integration.

3. **Deploy Workflow:** Import and activate the workflow in n8n.

4. **Interacting With Endpoints:**

   - **Record expense:**

     ```http
     POST /record-expense
     Content-Type: application/json

     {
       "amount": 50,
       "userId": "user123",
       "description": "Groceries",
       "date": "2024-06-14T10:00:00Z" // optional
     }
     ```

   - **Get expenses:**

     ```http
     GET /get-expenses?userId=user123
     ```

   - **Get finance advice:**

     ```http
     GET /finance-advice?userId=user123
     ```

---

## Notes

- Expense `id` is generated based on current timestamp to ensure uniqueness.
- Both AI-related nodes utilize GPT-4 with temperature set to 0.7 for balanced creativity.
- The workflow limits fetched records to 1000 for performance considerations.
- Budget categorization is simplistic and can be customized in the function nodes as needed.

---

This workflow provides a comprehensive personal finance assistant by combining automated data handling and powerful AI-driven financial insights.