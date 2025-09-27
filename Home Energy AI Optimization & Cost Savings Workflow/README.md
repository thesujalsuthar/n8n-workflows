# AI-Powered Personalized Home Energy Consumption Optimizer and Cost Reduction Advisor

## Overview

This n8n workflow leverages AI to optimize home energy consumption and provide personalized cost reduction advice. It collects data from smart meters and connected appliances, combines them, analyzes the data using OpenAI's GPT-4, and delivers actionable recommendations to users via email and Telegram.

---

## Workflow Components

### 1. Schedule Daily Optimization (`Schedule Daily Optimization`)
- **Type:** Schedule Trigger
- **Schedule:** Every weekday (Monday to Friday) at 07:00 AM
- **Function:** Automatically triggers the workflow daily for continuous energy monitoring and optimization.

### 2. Fetch Smart Meter Data (`Fetch Smart Meter Data`)
- **Type:** HTTP Request
- **Method:** GET
- **Endpoint:** `https://api.smartmeterprovider.com/latest-consumption?meterId={{meterId}}`
- **Authentication:** Bearer token in header (`YOUR_SMART_METER_API_TOKEN`)
- **Purpose:** Retrieves the latest energy consumption data from the user's smart meter based on their meter ID.

### 3. Fetch Appliances Energy Data (`Fetch Appliances Energy Data`)
- **Type:** Function
- **Functionality:**
  - Iterates over a predefined appliance list: fridge, washing machine, oven, AC, heater.
  - Sends HTTP GET requests to `https://api.smartapplianceprovider.com/appliances/{appliance}/consumption?user={userId}`.
  - Uses Bearer token authentication (`YOUR_SMART_APPLIANCE_API_TOKEN`) for each request.
- **Purpose:** Collects individual energy consumption data from major appliances associated with the user.

### 4. Combine Energy Data (`Combine Energy Data`)
- **Type:** Function
- **Functionality:** Merges smart meter data and appliance-level data into a single combined dataset for comprehensive analysis.

### 5. AI Analyze & Recommend (`AI Analyze & Recommend`)
- **Type:** Function
- **API:** OpenAI GPT-4 Chat Completion endpoint
- **Input:** Combined energy data JSON
- **Prompt:** System message sets the AI as a home energy optimization assistant; user message instructs analysis and recommends actionable energy-saving tips.
- **Authentication:** Uses OpenAI API key (`YOUR_OPENAI_API_KEY`) from n8n credentials.
- **Output:** Personalized energy optimization recommendations and cost reduction advice.
- **Error Handling:** Returns error messages if the AI request fails.

### 6. Send Email Alerts (`Send Email Alerts`)
- **Type:** Email Send
- **From:** energy-advisor@yourdomain.com
- **To:** User’s email address dynamically from the workflow data (`userEmail`)
- **Subject:** Your Personalized Home Energy Optimization Report
- **Body:** AI-generated recommendations text.
- **Purpose:** Delivers personalized energy efficiency reports via email.

### 7. Send Messaging Alert (`Send Messaging Alert`)
- **Type:** Telegram
- **Operation:** Send chat message
- **Chat ID:** User’s Telegram chat ID from workflow data (`userChatId`)
- **Message:** AI-generated recommendations text.
- **Authentication:** Telegram Bot Token (`YOUR_TELEGRAM_BOT_TOKEN`)
- **Purpose:** Provides instant delivery of energy advising messages directly to users on Telegram.

### 8. Receive Energy Data Webhook (`Receive Energy Data Webhook`)
- **Type:** Webhook (GET)
- **Endpoint:** `/energy-data-webhook`
- **Response:** Sends “Data received” upon incoming request.
- **Function:** Allows external data push to trigger or inject energy data if needed manually or from third-party services.

---

## How It Works

1. **Scheduled trigger** initiates the workflow at 7:00 AM on weekdays.
2. The workflow **fetches the latest consumption data** from the user’s smart meter API.
3. Simultaneously, it queries **energy consumption data from key household appliances.**
4. These datasets are **combined into a holistic profile** of the household’s energy usage.
5. The combined data is sent to **OpenAI’s GPT-4 model**, which analyzes it and generates personalized recommendations to improve efficiency and reduce costs.
6. Recommendations are **sent to the user both via email and Telegram**, ensuring timely receipt.
7. The webhook endpoint provides an option for external systems or manual triggers to inject energy data if required.

---

## Setup Requirements

- **n8n instance** to import and run this workflow.
- **API tokens**:
  - Smart Meter Provider API Token (`YOUR_SMART_METER_API_TOKEN`)
  - Smart Appliance Provider API Token (`YOUR_SMART_APPLIANCE_API_TOKEN`)
  - OpenAI API Key (`YOUR_OPENAI_API_KEY`)
  - Telegram Bot Token (`YOUR_TELEGRAM_BOT_TOKEN`)
- **Email configuration** for the email send node (SMTP settings configured in n8n).
- Proper user data context injection (meterId, userId, userEmail, userChatId) either from incoming webhook or external integration.

---

## Notes

- Replace placeholders (`YOUR_SMART_METER_API_TOKEN`, `YOUR_SMART_APPLIANCE_API_TOKEN`, `YOUR_OPENAI_API_KEY`, `YOUR_TELEGRAM_BOT_TOKEN`) with your actual credentials.
- Make sure your API providers allow the required scope and endpoints for data retrieval.
- The AI analyze step uses GPT-4 and is subject to OpenAI API usage policies and cost.
- The appliance list in the function node can be customized based on the user's appliances.

---

## Contact

For any questions or support configuring this workflow, please contact the administrator or visit the n8n community forums.