# Personal Finance Monthly Budget Workflow

This workflow automates the process of fetching your recent expenses, categorizing them, aggregating the spending by category, and then generating personalized budget advice sent directly to you via Telegram. It runs daily at 8:00 PM to provide up-to-date financial insights.

---

## Workflow Overview

1. **Daily Trigger**  
   Triggers the workflow every day at 20:00 (8:00 PM).

2. **Get Recent Expenses**  
   Connects to your MySQL database (`finance_db`) and retrieves all expense records from the last month using the query:  
   ```sql
   SELECT * FROM expenses WHERE date >= DATE_SUB(CURDATE(), INTERVAL 1 MONTH);
   ```

3. **Categorize Expenses**  
   Categorizes each expense based on keywords found in the expense description. Categories include:  
   - Groceries  
   - Dining  
   - Transport  
   - Housing  
   - Subscriptions  
   - Utilities  
   - Other (default)

4. **Aggregate Expenses by Category**  
   Sums up the total spending amount for each category, providing a clear summary of where money was spent over the past month.

5. **Generate Budget Plan & Tips**  
   Sends the aggregated category data to OpenAI’s GPT-4 model with a system prompt positioning the AI as a personal finance assistant. GPT-4 generates a personalized budget plan and tailored financial advice to help optimize the user's spending.

6. **Send Financial Tips**  
   The personalized budget plan and tips received from GPT-4 are sent as a Telegram message to the user’s chat ID.

---

## Node Details

### 1. Daily Trigger  
- **Type:** Schedule Trigger  
- **Trigger Time:** 20:00 daily  

### 2. Get Recent Expenses  
- **Type:** MySQL  
- **Database:** `finance_db`  
- **Query:**  
  ```sql
  SELECT * FROM expenses WHERE date >= DATE_SUB(CURDATE(), INTERVAL 1 MONTH);
  ```  
- **Credentials:** Requires a configured MySQL credential named `MySQL Finance DB`.

### 3. Categorize Expenses  
- **Type:** Function  
- **Function Logic:**  
  Processes each expense’s description (case-insensitive) to assign a category using regex matching keywords.  
- Example Keyword Mapping:  
  - Grocery-related words → Groceries  
  - Dining or cafe references → Dining  
  - Transport-related terms → Transport  
  - Rent or mortgage → Housing  
  - Subscriptions (Netflix, Spotify, etc.) → Subscriptions  
  - Utility services (electric, gas, water) → Utilities  
  - Others → Other  

### 4. Aggregate Expenses by Category  
- **Type:** Aggregate  
- **Operation:** Sum the `amount` field grouped by `category` field

### 5. Generate Budget Plan & Tips  
- **Type:** OpenAI (Chat Completion)  
- **Model:** GPT-4  
- **System Prompt:**  
  You are a personal finance assistant that helps optimize budgets, analyze spending, and provide actionable personalized financial advice.  
- **User Prompt:**  
  Here is the user's categorized monthly expenses data: {{$json}}. Please generate a personalized budget plan and provide tailored financial tips to optimize their spending.  
- **Options:** Temperature set to 0.7 for balanced creativity and accuracy  
- **Credentials:** Requires OpenAI API key stored as `OpenAI Personal Finance API Key`

### 6. Send Financial Tips  
- **Type:** Telegram  
- **Operation:** Send message to Telegram chat  
- **Chat ID:** Uses the `userChatId` field from the data if available; otherwise defaults to `'user_default_chat_id'`  
- **Message Content:** Uses the generated text from GPT-4 response  
- **Credentials:** Requires Telegram Bot API credential named `Telegram Bot API`

---

## Prerequisites

- **MySQL Database:** Must have a database named `finance_db` with an `expenses` table containing expense records with fields including `date`, `amount`, `description`, etc.
- **n8n Credentials Setup:**  
  - MySQL credentials for connecting to `finance_db`  
  - OpenAI API key configured for GPT-4 access  
  - Telegram Bot API credentials for sending messages  
- **Telegram Bot:** Telegram bot must be set up and user chat ID known or handled in the workflow.

---

## How to Use

1. Import this workflow into your n8n instance.
2. Configure the MySQL, OpenAI, and Telegram credentials.
3. Ensure your database contains up-to-date expense data.
4. Activate the workflow.
5. The workflow runs every day at 8 PM, pulling the last month's expenses, categorizing them, and sending you budget tips through Telegram.

---

## Customization

- **Trigger Time:** Adjust the scheduled trigger time as per your preference.  
- **Expense Categorization:** Modify the regex patterns in the function node to better fit your expense descriptions.  
- **OpenAI Prompt:** Customize the system or user messages in the OpenAI node to change the style or focus of the financial advice.  
- **Telegram Target:** Change the chat ID handling logic to match your preferred messaging setup.

---

## License

This workflow is provided as-is without any warranty. Modify and use it according to your personal finance needs.