# AI-Powered Personalized Carbon Footprint Reduction Plan Generator

This workflow is designed to generate a personalized carbon footprint reduction plan for users based on their activity data. It integrates with external APIs to fetch user activity, calculates the carbon footprint, and uses AI to provide tailored recommendations. Additionally, it supports weekly reminder scheduling to encourage consistent progress review.

---

## Workflow Overview

### 1. Webhook - Fetch User Activity
- **Node Type:** Webhook (POST)
- **Path:** `/fetch-activity-data`
- **Purpose:** Entry point for receiving user information (`userId` and `dateRange`) to initiate activity data fetching.

### 2. HTTP Request - Get Activity Data
- **Node Type:** Function
- **Action:** Fetches user's activity data from an external Habit Tracking API.
- **Details:**
  - Uses `userId` and `dateRange` to request activities.
  - Requires `Habit Track API Key` credentials.
  - Calls `https://api.habittrack.com/v1/activities` with authorization header.

### 3. Function - Aggregate Activities
- **Node Type:** Function
- **Action:** Processes raw activity data.
- **Details:**
  - Aggregates activities by their type and totals amounts.
  - Prepares data for carbon footprint calculation.

### 4. HTTP Request - Calculate Carbon Footprint
- **Node Type:** HTTP Request
- **Action:** Sends aggregated activity data to Carbon Footprint API for calculation.
- **Details:**
  - Endpoint: `https://api.carbonfootprint.com/v2/calculate`
  - Method: POST with JSON body containing activities.
  - Requires `Carbon Footprint API Key` credentials.
  - Receives carbon footprint data in response.

### 5. Function - Generate AI Prompt
- **Node Type:** Function
- **Action:** Creates a prompt for AI based on carbon footprint data.
- **Details:**
  - Formats user's footprint data into a contextual prompt.
  - Requests a personalized reduction plan focusing on top emission sources.

### 6. OpenAI - Generate Reduction Plan
- **Node Type:** OpenAI
- **Model:** GPT-4
- **Action:** Generates a personalized carbon footprint reduction plan based on the prompt.
- **Parameters:**
  - Temperature: 0.7
  - Maximum Tokens: 400

### 7. Function - Extract Plan Text
- **Node Type:** Function
- **Action:** Extracts the textual plan from OpenAI's response.
- **Details:**
  - Retrieves AI-generated content from the response object.

---

## Weekly Reminder Setup

### 8. Webhook - Setup Reminder
- **Node Type:** Webhook (POST)
- **Path:** `/subscribe-weekly-reminder`
- **Purpose:** Endpoint to subscribe users to weekly reminders for reviewing their carbon footprint plan.

### 9. Function - Prepare Reminder Data
- **Node Type:** Function
- **Action:** Extracts user ID and preferred reminder time.
- **Defaults:** 09:00 AM if no specific time provided.

### 10. Cron - Weekly Reminder
- **Node Type:** Cron
- **Schedule:** Every Monday at 09:00 AM.
- **Purpose:** Triggers weekly reminder workflow.

### 11. Function - Reminder Message
- **Node Type:** Function
- **Action:** Creates a reminder message encouraging plan review and progress update.

### 12. Notification - Send Reminder
- **Node Type:** Notification
- **Operation:** Sends reminder messages to users.
- **Details:** Sends custom notification message to the user ID.

---

## Credentials Required

- **Habit Track API Key:** For accessing user activity data.
- **Carbon Footprint API Key:** For calculating carbon footprint from activities.

---

## How to Use

1. **Initiate plan generation:**
   - Send a POST request to `/fetch-activity-data` with JSON including `userId` and `dateRange`.
   - The workflow fetches and processes data, calculates footprint, generates AI plan, and returns the plan.

2. **Subscribe for weekly reminders:**
   - Send a POST request to `/subscribe-weekly-reminder` with user info and optional reminder time.
   - The user will receive weekly notifications to review their carbon footprint reduction plan.

---

## Notes

- Make sure API keys for Habit Track and Carbon Footprint services are configured in the credentials.
- OpenAI integration requires API access for GPT-4.
- This workflow assumes proper user ID mapping and notification system to send reminders.

---

This automated workflow helps users better understand and reduce their carbon footprint with AI-powered customized recommendations and ongoing engagement.