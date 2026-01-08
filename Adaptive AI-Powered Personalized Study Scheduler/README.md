# Adaptive AI-Driven Personalized Study Scheduler

## Overview

This workflow creates a personalized, adaptive study schedule for users by leveraging AI to optimize study sessions according to user priorities, deadlines, past study habits, and preferences. The schedule is automatically added to the user's Google Calendar, and notifications including emails and reminders are sent to keep the user on track.

---

## Workflow Nodes and Functionality

### 1. Webhook - Receive Study Data

- **Type:** Webhook (POST)
- **Path:** `/submit-study-data`
- **Purpose:** Entry point that receives user study data including subjects, study history, preferences, calendar ID, user ID, and email.

### 2. Extract Study Input

- **Type:** Function
- **Purpose:** Processes and formats incoming JSON data from the webhook for downstream nodes.
- **Expected Input Example:**
  ```json
  {
    "subjects": [
      {"name": "Math", "priority": 3, "deadlines": ["2024-06-15"]}
    ],
    "pastStudySessions": [
      {"subject": "Math", "duration": 60, "date": "2024-06-10"}
    ],
    "preferences": {
      "dailyStudyHours": 4,
      "preferredHours": [18, 19, 20]
    },
    "calendarId": "user_calendar_id",
    "userId": "12345",
    "userEmail": "user@example.com"
  }
  ```

### 3. Generate Adaptive Schedule

- **Type:** OpenAI (gpt-4)
- **Purpose:** Uses the AI model to generate a personalized study schedule for the upcoming week.
- **Prompt Summary:** The AI plans daily time blocks to maximize retention, respect deadlines, and adapt study duration according to priority and historical behavior.
- **Output Format:**
  ```json
  {
    "2024-06-14": [
      {"subject": "Math", "startTime": "18:00", "endTime": "19:00"},
      ...
    ],
    ...
  }
  ```

### 4. Parse Schedule JSON

- **Type:** Function
- **Purpose:** Parses the AI-generated JSON schedule to a usable format for subsequent steps.

### 5. Create Calendar Events

- **Type:** Google Calendar Node
- **Operation:** Create events for each study session in the user's specified Google Calendar.
- **Details:**
  - Event summary: `{subject} study session`
  - Event description: `Adaptive AI generated study session for {subject}`
  - Timezone: America/New_York (adjust as needed)
- **Credentials:** Requires a connected Google Calendar OAuth2 account.

### 6. Send Schedule Email Notification

- **Type:** Email Send
- **Purpose:** Sends an email to the user confirming the schedule has been added to their calendar.
- **Credentials:** Requires SMTP credentials for sending emails.

### 7. Prepare Reminder Notifications

- **Type:** Function
- **Purpose:** Creates scheduled reminder notifications for each study session with the following data:
  - Recipient email
  - Subject line (e.g., "Reminder: Study Math at 18:00")
  - Text reminder message
  - Scheduled datetime as ISO string

### 8. Send Reminder Emails

- **Type:** Email Send
- **Purpose:** Sends reminder emails to the user for upcoming study sessions using the prepared notifications.
- **Credentials:** Requires SMTP credentials.

### 9. Scheduler Trigger

- **Type:** Schedule Trigger
- **Frequency:** Every 60 minutes
- **Purpose:** Regularly triggers the workflow to fetch latest user study data and update the study schedule accordingly.

### 10. Fetch Latest User Data

- **Type:** HTTP Request
- **Purpose:** Retrieves the latest study data and user preferences from an external API to reflect updates and changes.
- **Credentials:** HTTP Basic Auth with username and password for API access.

---

## Workflow Data Flow Summary

1. User submits study data via webhook.
2. Data is extracted and formatted.
3. AI generates an adaptive, personalized weekly study schedule.
4. The generated schedule JSON is parsed.
5. Calendar events are created for each study session.
6. User receives a confirmation email about their new schedule.
7. Reminder notifications are prepared and sent ahead of each study session.
8. Independently, a scheduled trigger runs every hour to fetch updated user data, regenerating the study schedule automatically.

---

## Prerequisites & Credentials

- **Google Calendar OAuth2 API**: To create calendar events.
- **SMTP Account**: To send emails for notifications and reminders.
- **External API Access**: For fetching updated user data with HTTP Basic authentication.
- **OpenAI API Key**: For access to GPT-4 to generate adaptive study schedules.

---

## Input Data Schema

- `subjects`: Array of objects containing:
  - `name` (string): Name of the subject.
  - `priority` (integer): Importance level for scheduling.
  - `deadlines` (array of strings): Important due dates in `YYYY-MM-DD` format.
- `pastStudySessions`: Array of past study sessions with subject, duration (minutes), and date.
- `preferences`:
  - `dailyStudyHours` (integer): Max hours preferred per day.
  - `preferredHours` (array of integers): Preferred start times (24h format).
- `calendarId`: Google Calendar ID to add study sessions.
- `userId`: Unique user identifier.
- `userEmail`: User's email address for notifications.

---

## Timezone Considerations

- Calendar events are created using `"America/New_York"` timezone by default.
- Adjust timezone in the "Create Calendar Events" node if your users are in different regions.

---

## Customization Tips

- Modify AI prompt in the "Generate Adaptive Schedule" node to tailor scheduling logic or output format.
- Adjust schedule trigger frequency as needed.
- Customize email templates in the email nodes.
- Add multi-timezone support by dynamically setting timezone in calendar event creation.
- Expand external API integration for richer user data inputs.

---

## License

This workflow and accompanying documentation are provided as-is without warranty. Adapt and extend as per your needs.