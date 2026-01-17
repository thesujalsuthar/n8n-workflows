# AI-Powered Personalized Work-Life Balance Optimizer

## Overview

This workflow leverages your Google Calendar events and wearable activity data to analyze and optimize your daily work-life balance. It extracts your scheduled work, break, and personal events, combines this with your physical activity data from a wearable device, and then provides personalized insights and alerts delivered directly via Telegram.

---

## Workflow Nodes

### 1. Get Today's Events
- **Type:** Google Calendar Trigger
- **Calendar:** Primary user calendar
- **Purpose:** Fetches all calendar events occurring from the current time up to 24 hours later.
- **Parameters:**
  - `timeMin`: Current time in ISO format.
  - `timeMax`: Current time + 24 hours in ISO format.
  - Fetches single events and orders them by start time.

### 2. Get Wearable Activity Data
- **Type:** HTTP Request
- **Purpose:** Retrieves activity data from a connected wearable device for the current day.
- **Parameters:**
  - `deviceId`: Set dynamically based on event JSON or environment variable `WEARABLE_DEVICE_ID`.
  - `dataType`: "activity"
  - `fromDate` and `toDate`: Both set to today's date (YYYY-MM-DD)
- **Authentication:** Uses HTTP Basic Auth credentials configured under "Wearable API Auth".

### 3. Analyze Work-Life Balance
- **Type:** Function (JavaScript)
- **Purpose:** Processes calendar event data and wearable activity to:
  - Categorize events into **work**, **break**, and **personal** based on event summaries.
  - Compute total hours spent on each category.
  - Calculate total physical active hours from wearable data (walking, running, exercise).
  - Compare actual hours with recommended daily targets:
    - Work: 8 hours
    - Breaks: 1.5 hours
    - Personal time: 3 hours
    - Physical activity: at least 1 hour
  - Generate actionable alerts if any category deviates from recommendations.
- **Output:** Summary statistics and an alerts array with suggestions or confirmations.

### 4. Notify User
- **Type:** Telegram Node
- **Purpose:** Sends the generated alerts or a positive confirmation message to the user via Telegram.
- **Parameters:**
  - Sends a message joining all alerts if any exist.
  - Otherwise, sends a motivational message for maintaining good balance.
  - Uses environment variable `USER_CHAT_ID` to target the user.
- **Authentication:** Uses Telegram API credentials configured under "Telegram API".

---

## Connections Flow

1. **Get Today's Events** triggers on new/updated events and sends data to **Analyze Work-Life Balance**.
2. **Get Wearable Activity Data** retrieves activity data and sends alongside calendar data to **Analyze Work-Life Balance**.
3. **Analyze Work-Life Balance** processes all inputs and passes the results to **Notify User**.
4. **Notify User** sends the final message to the user on Telegram.

---

## Setup Instructions

1. **Google Calendar Trigger**
   - Ensure OAuth credentials for Google Calendar are set up in your n8n instance.
   - Use `primary` calendar or specify another if needed.

2. **Wearable API**
   - Configure the wearable device API endpoint and credentials.
   - Set the wearable device ID via environment variable `WEARABLE_DEVICE_ID`.
   - Ensure the API returns daily activity data including `type` and `durationMinutes`.

3. **Telegram Notification**
   - Setup Telegram bot API credentials.
   - Obtain your Telegram Chat ID and set it in environment variable `USER_CHAT_ID`.

4. **Environment Variables**
   - `WEARABLE_DEVICE_ID`
   - `USER_CHAT_ID`

5. **Activate the Workflow**
   - Enable the workflow to run on your schedules or triggers.

---

## Recommended Daily Targets

| Category         | Recommended Hours |
|------------------|-------------------|
| Work             | 8                 |
| Breaks           | 1.5               |
| Personal Time    | 3                 |
| Physical Activity| ≥ 1               |

---

## Alerts Examples

- "You have worked 9.0 hours today, which exceeds the recommended 8 hours."
- "You have only taken 1.0 hours of breaks. Try to take at least 1.5 hours of breaks."
- "Your personal time today is 2.0 hours. Aim for at least 3 hours."
- "You have only 0.5 hours of physical activity today. Consider adding some exercise."

---

## Summary Output Example

```
Work: 7.5h, Breaks: 1.0h, Personal: 2.5h, Active: 0.7h
```

---

## Notes

- The workflow dynamically calculates durations in hours using event start and end times.
- Activity types considered for physical activity include walking, running, and exercise.
- Alerts intelligently notify to rebalance your day when targets are not met or exceeded.
- Data privacy depends on your integrations and credentials security.

---

Thank you for using the AI-Powered Personalized Work-Life Balance Optimizer! Stay balanced and productive.