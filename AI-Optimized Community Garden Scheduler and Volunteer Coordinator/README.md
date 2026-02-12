# AI-Powered Community Garden Resource Scheduler and Volunteer Coordination System

## Overview

This workflow automates the scheduling and coordination of community garden resources and volunteer tasks using AI. It optimizes weekly assignments based on resource availability, volunteer availability and skills, task priorities, and weather forecasts, then distributes schedules and notifications to involved parties.

---

## Workflow Components

### 1. **Get Weather Forecast**
- **Node Type:** HTTP Request
- **Description:** Retrieves 7-day weather forecast data via WeatherAPI for the community garden location.
- **Key Parameters:**
  - API Key sourced from credentials (`weatherApiKey`)
  - Location set as "Community Garden Location"
  - Days forecasted: 7

### 2. **Get Volunteer Availability & Skills**
- **Node Type:** Google Sheets
- **Description:** Fetches volunteer details including availability and skills from a Google Sheets document.
- **Credentials:** Uses Google Sheets OAuth2 API.

### 3. **Get Garden Resource Inventory**
- **Node Type:** Google Sheets
- **Description:** Retrieves current inventory and availability of garden resources such as tools, water, and fertilizers.
- **Credentials:** Uses Google Sheets OAuth2 API.

### 4. **Get Garden Tasks & Priorities**
- **Node Type:** Google Sheets
- **Description:** Loads a list of garden tasks along with their priorities from Google Sheets to be scheduled.
- **Credentials:** Uses Google Sheets OAuth2 API.

### 5. **Prepare AI Input**
- **Node Type:** Function
- **Description:** Consolidates fetched data (volunteers, resources, tasks, weather) into a single structured JSON object to be sent to the AI model.

### 6. **AI Optimize Schedule**
- **Node Type:** OpenAI GPT-4
- **Description:** Sends the consolidated data as a prompt to the AI model which returns an optimized weekly schedule, assigning resources and volunteers to tasks while alerting for shortages or conflicts.
- **Key Prompt Highlights:**
  - Input: Resource availability, volunteer availability and skills, task priority, and weather conditions.
  - Output: Weekly optimized schedule indicating assignments and alerts.
- **Credentials:** OpenAI API key via header authentication.
- **Settings:** Model `gpt-4`, temperature 0.5, response max tokens 1000.

### 7. **Parse AI Schedule**
- **Node Type:** Function
- **Description:** Parses the AI JSON response (schedule) into individual task objects for downstream processing.

### 8. **Create Calendar Event**
- **Node Type:** Google Calendar
- **Description:** Adds each scheduled task as an event to the primary Google Calendar with details:
  - Start and end times
  - Task name and assigned volunteer
  - Resources allocated
  - Location set to "Community Garden"
  - Invites volunteer by email
- **Credentials:** Google Calendar OAuth2 API.

### 9. **Send Volunteer SMS Notification**
- **Node Type:** Twilio
- **Description:** Sends SMS reminders to volunteers about their upcoming tasks, including time and assigned resources.
- **Credentials:** Twilio API.

### 10. **Send Volunteer Email Notification**
- **Node Type:** SMTP Email
- **Description:** Sends email notifications to each volunteer with task details, scheduled time, resources, and contact information.
- **Credentials:** SMTP account.

### 11. **Notify Team Messaging Channel**
- **Node Type:** Slack
- **Description:** Posts messages in the Slack channel `garden-volunteers` containing upcoming task details and resource needs for team awareness.
- **Credentials:** Slack API.

---

## Connections & Data Flow

1. Volunteer availability, resource inventory, task details, and weather data are fetched individually.
2. All fetched data feeds into the **Prepare AI Input** node to create a unified JSON payload.
3. The unified data is sent to **AI Optimize Schedule** to generate an optimized task assignment schedule.
4. The AI’s response is parsed in **Parse AI Schedule** to split tasks for parallel processing.
5. Each scheduled task is then used to:
   - Create calendar events for volunteers.
   - Send SMS reminders.
   - Send email notifications.
   - Notify the team via Slack.

---

## Prerequisites and Setup

- **APIs and Credentials:**
  - OpenAI API key with GPT-4 access.
  - WeatherAPI key for weather forecasts.
  - Google OAuth Credentials for Google Sheets (volunteer, resources, tasks) and Google Calendar.
  - Twilio account for SMS messaging.
  - SMTP email account for sending emails.
  - Slack API credentials for posting messages in team channels.

- **Google Sheets Setup:**
  - Volunteers sheet: should include volunteer names, emails, phone numbers, availability, and skills.
  - Resources sheet: should detail all available garden resources and their quantities.
  - Tasks sheet: should include all garden tasks along with priority levels and any additional notes.

- **Slack Setup:**
  - Create a channel named `garden-volunteers` for team notifications.

---

## How to Use

1. Ensure all API credentials and tokens are added to the workflow credentials in your automation platform.
2. Keep Google Sheets updated with the latest volunteer, resource, and task details.
3. Run the workflow weekly or schedule it to automatically run in preparation for the upcoming week.
4. The AI will generate an optimized schedule, add tasks to calendars, and send notifications to volunteers and the team.

---

## Benefits

- Streamlined and optimized scheduling of resources and volunteers.
- Proactive conflict and shortage detection.
- Automated calendar event creation and multi-channel volunteer notifications.
- Increased team communication and coordination via Slack.
- Efficient use of garden resources aligned with weather forecasts.

---

## Support

For issues or improvements with this workflow, please contact the Community Garden Team or submit issues to the repository maintainer.