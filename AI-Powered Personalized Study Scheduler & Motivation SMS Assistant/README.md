# AI-Driven Personalized Study Planner & Motivation Tracker

This workflow uses AI to create a customized 7-day study schedule based on user preferences and past performance data, generates daily motivational messages, adds study sessions to a calendar, and sends motivation via SMS.

---

## Workflow Overview

### 1. Initialize User Data

- **Node:** Initialize User Data (Function)
- **Purpose:** Sets up user information with default values if not provided.
- **Details:**
  - Reads `userId`, `preferences` (subjects, daily study hours, study start time, motivation message time).
  - Loads performance data.
  - Defaults:
    - Subjects: Math, Physics, Chemistry
    - Daily Study Hours: 3
    - Study Start Time: 18:00
    - Motivation Time: 08:00

### 2. AI - Generate Study Schedule

- **Node:** AI - Generate Study Schedule (HTTP Request)
- **API:** OpenAI Chat Completion (model: GPT-4)
- **Purpose:** Creates a personalized 7-day study plan based on the user's preferences and past performance.
- **Inputs:**
  - User preferences
  - User performance data
- **Output:** JSON object with date, subject, and session duration (minutes).

### 3. Parse Schedule JSON

- **Node:** Parse Schedule JSON (Function)
- **Purpose:** Parses the JSON string returned by the AI into individual study session objects.
- **Output:** Array of study sessions.

### 4. Format Calendar Events

- **Node:** Format Calendar Events (Function)
- **Purpose:** Converts study sessions into calendar event format.
- **Details:**
  - Computes exact start and end times using study start time and session duration.
  - Prepares event details such as summary, description, start, and end datetime in ISO format.

### 5. Create Calendar Events

- **Node:** Create Calendar Events (HTTP Request)
- **API:** External Calendar API
- **Purpose:** Sends the formatted study sessions as events to the user’s calendar.
- **Authentication:** Uses Calendar API Key credential.

### 6. AI - Generate Motivation Message

- **Node:** AI - Generate Motivation Message (HTTP Request)
- **API:** OpenAI Chat Completion (model: GPT-4)
- **Purpose:** Generates a daily motivational message tailored to user preferences and study habits.
- **Output:** Motivation message text.

### 7. Extract Motivation Message

- **Node:** Extract Motivation Message (Function)
- **Purpose:** Extracts the motivational message content from the AI response.

### 8. Send Motivation SMS

- **Node:** Send Motivation SMS (Twilio)
- **Purpose:** Sends the motivational message to the user’s phone via SMS.
- **Details:**
  - Defaults to phone number `+1234567890` if none is provided.
  - Uses Twilio API credentials.

---

## Credentials Required

- **OpenAI API Key:** For AI-powered study schedule and motivational message generation.
- **Calendar API Key:** To create events in the user's calendar.
- **Twilio API:** To send SMS messages with daily motivational quotes.

---

## Data Flow Summary

1. User data is initialized and defaulted if necessary.
2. AI generates a 7-day personalized study schedule.
3. Schedule JSON is parsed and formatted for calendar events.
4. Calendar events are created on the user's calendar.
5. AI generates a daily motivational message.
6. Motivational message is extracted.
7. Message is sent to the user’s phone as an SMS.

---

## Customization

- Adjust user preferences in the "Initialize User Data" node to personalize subjects, study hours, and times.
- Configure API credentials for OpenAI, Calendar, and Twilio nodes.
- Modify AI prompts within the HTTP Request nodes to tweak schedule and messaging styles.

---

## Useful Links

- [OpenAI API Documentation](https://platform.openai.com/docs/api-reference/chat/create)
- [Twilio SMS API](https://www.twilio.com/docs/sms)
- [Calendar API Documentation](Replace with your calendar API docs URL)

---

## Notes

- Ensure all API keys are securely stored and configured in the n8n credentials manager.
- Time zone considerations may require adjustments based on user location.
- SMS recipient number should be validated to ensure message delivery.
- Error handling and retries can be added for production readiness.