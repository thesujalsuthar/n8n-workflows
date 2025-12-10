# AI-Driven Personalized Study Planner for Proactive Skill Development

This workflow automates the creation of a personalized study plan by assessing a user's skills, identifying gaps, recommending learning resources, scheduling study sessions in the user's calendar, and sending motivational reminders to keep the user engaged.

---

## Workflow Overview

### 1. Dummy User Profile
- **Type:** Function
- **Purpose:** Provides a sample user profile including name, email, current skills, and learning preferences.
- **Output:** User profile JSON.
  
### 2. AI Skill Assessment
- **Type:** HTTP Request (POST)
- **Endpoint:** `https://api.example-ai.com/v1/skill-assessment`
- **Purpose:** Sends the user profile data to an AI service to perform a skill assessment.
- **Authorization:** API key via header (`Bearer {{$credentials.aiApi.key}}`).
- **Input:** User profile JSON.
- **Output:** Skill assessment results including identified skills to develop and their priorities.

### 3. Fetch Calendar Events
- **Type:** HTTP Request (GET)
- **Endpoint:** Google Calendar API to fetch next 10 upcoming events from the user's primary calendar starting from the current time.
- **Authorization:** Google Calendar API key via header (`Bearer {{$credentials.googleCalendarApi.key}}`).
- **Purpose:** To retrieve existing calendar events to avoid scheduling conflicts.
- **Output:** List of upcoming calendar events.

### 4. Identify Skill Gaps
- **Type:** Function
- **Purpose:** Processes the AI skill assessment output to extract and prioritize skill gaps for development.
- **Output:** Array of skill gaps sorted by priority.

### 5. Fetch Learning Resources
- **Type:** HTTP Request (GET)
- **Endpoint:** `https://api.learningplatforms.com/v1/recommendations`
- **Purpose:** Queries external learning platform APIs for recommended resources matched to identified skill gaps.
- **Authorization:** API key via header (`Bearer {{$credentials.learningPlatformsApi.key}}`).
- **Input:** Comma-separated skills to develop.
- **Output:** Top 5 recommended learning resources per skill.

### 6. Schedule Learning Sessions
- **Type:** Function
- **Purpose:** Suggests available time slots over the next 7 days (9 AM to 5 PM, every 2 hours), limiting to 2 sessions per day, avoiding conflicts with existing calendar events.
- **Scheduling Logic:**
  - Checks user's free time slots by comparing with fetched calendar events.
  - Assigns study sessions by cycling through skill gaps.
- **Output:** Array of skill study sessions with summary, start, and end times.

### 7. Add Events to Calendar
- **Type:** HTTP Request (POST)
- **Endpoint:** Google Calendar API to add new events.
- **Purpose:** Adds the scheduled study sessions as events to the user's calendar.
- **Authorization:** Google Calendar API key.
- **Features:** Retries up to 3 times on failure with fallback continuation.
- **Input:** Each study session as a calendar event (summary, start datetime, end datetime).

### 8. Merge Sessions and Resources
- **Type:** Function
- **Purpose:** Combines the fetched learning resources and scheduled sessions into a single payload.
- **Output:** Merged data including study sessions and learning resources.

### 9. Send Study Plan Email
- **Type:** Email Send
- **Purpose:** Sends a personalized email to the user containing:
  - Skill assessment summary.
  - Scheduled study sessions with dates and times.
  - Recommended learning resources with titles and links.
- **From:** `no-reply@studyplanner.ai`
- **To:** User's email from profile.
- **Subject:** "Your Personalized Study Plan and Learning Resources".

### 10. Prepare Motivational Reminders
- **Type:** Function
- **Purpose:** Creates reminders to be sent 15 minutes before each scheduled study session.
- **Output:** List of reminders with recipient email, message, and scheduled sending time.

### 11. Filter Due Reminders
- **Type:** Function
- **Purpose:** Filters reminders that are due for sending based on current time.
- **Output:** Only reminders due now or past.

### 12. Send Reminder Notification
- **Type:** Notification
- **Purpose:** Sends motivational reminders/notifications to the user's email before study sessions.

---

## Credentials Required

- **AI API Key:** For skill assessment API.
- **Google Calendar API Key:** For reading and writing calendar events.
- **Learning Platforms API Key:** For fetching recommended learning resources.

---

## How It Works - Data Flow Summary

1. **Input:** User profile is initialized.
2. **Skill Assessment:** User data is sent to AI for skill gap analysis.
3. **Calendar Fetch:** Current calendar events are retrieved.
4. **Identify Gaps:** Extract and prioritize which skills to focus on.
5. **Resource Recommendation:** Request learning resources for gaps.
6. **Session Scheduling:** Plan study slots avoiding calendar conflicts.
7. **Calendar Update:** Add new study sessions as calendar events.
8. **Combine Data:** Aggregate sessions and resources.
9. **Email Dispatch:** Send detailed study plan via email.
10. **Reminders:** Schedule and send advance reminders for study sessions.

---

## Notes

- The workflow assumes a valid user profile JSON as input; replace the dummy user profile with actual user data.
- Scheduling logic allocates study sessions 1 hour long, maximum 2 per day between 9 AM and 5 PM.
- Email content dynamically personalizes messages with the user's name, sessions, and resources.
- All external API calls require appropriate API keys configured in the credentials.
- Reminder notifications use the notification node to alert users ahead of scheduled study sessions.

---

## Usage

1. Set up required API credentials.
2. Replace or modify the Dummy User Profile node for real user input.
3. Activate the workflow.
4. The system will assess skills, generate study plans, update calendar, send emails, and deliver reminders proactively.

---

Harness this AI-driven automation to personalize learning journeys and maintain motivation through proactive scheduling and engagement!