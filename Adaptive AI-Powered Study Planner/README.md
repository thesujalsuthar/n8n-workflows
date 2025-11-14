# Adaptive Study Schedule Workflow

This workflow automates the generation of a personalized, adaptive study schedule by integrating your calendar events, task list, and learning progress, leveraging AI to optimize your study plan and send motivational notifications daily.

---

## Workflow Overview

1. **Daily Trigger**  
   Runs every day at 07:00 AM to initiate the workflow.

2. **Get Upcoming Calendar Events**  
   Fetches your Google Calendar events scheduled within the next 7 days from your primary calendar using OAuth2 authentication.

3. **Get Task List**  
   Retrieves your current tasks from Todoist, dynamically selecting the task list based on input or defaults.

4. **Aggregate Inputs**  
   Combines fetched tasks and calendar events with static placeholders for learning progress and preferred study time slots.

5. **Generate Adaptive Study Schedule**  
   Sends the aggregated data to OpenAI's GPT-4 model with a prompt specialized in personalized study planning. The AI produces a detailed, adaptive study schedule prioritizing difficult or overdue tasks and includes motivational prompts.

6. **Parse AI Schedule & Motivation**  
   Parses the AI response (expected in JSON format) into discrete schedule items and motivation message, handling possible parsing errors.

7. **Create Calendar Events**  
   Creates study sessions as new events in your Google Calendar according to the AI-generated schedule.

8. **Create/Update Tasks**  
   Adds or updates tasks in Todoist aligned with the AI’s recommendations, including due dates and priority.

9. **Send Motivation Notification**  
   Sends a motivational message to your preferred Slack channel to encourage and keep you focused on your study goals.

---

## Node Breakdown

### 1. Daily Trigger  
- **Type:** Schedule Trigger  
- **Trigger Time:** Daily at 07:00  
- **Purpose:** Starts the workflow every day to ensure refreshed scheduling.

### 2. Get Upcoming Calendar Events  
- **Type:** Google Calendar Node  
- **Operation:** Get events  
- **Calendar:** Primary calendar  
- **Time Window:** From current time to 7 days ahead  
- **Authentication:** OAuth2 (Google Calendar OAuth2 API credentials)

### 3. Get Task List  
- **Type:** Todoist Node  
- **Operation:** Get tasks  
- **Task List:** Uses dynamic input or defaults  
- **Authentication:** Todoist API credentials

### 4. Aggregate Inputs  
- **Type:** Function Node  
- **Function:** Combines tasks and calendar events with static learning progress and preferred study times:  
  - `progress` simulates subject-wise learning progress (Math, History, Physics).  
  - `preferredTimes` simulates preferred daily study periods (e.g., 18:00-20:00, 08:00-10:00).

### 5. Generate Adaptive Study Schedule  
- **Type:** OpenAI Node  
- **Model:** GPT-4  
- **Prompt:**  
  - System role specifies task: generate an optimized and adaptive study schedule considering user's tasks, calendar events, learning progress, and preferred study hours.  
  - User message provides the aggregated data and requests prioritization for difficult or overdue tasks with motivational prompts.  
- **Options:** Temperature set to 0.7 to balance creativity and coherence.  
- **Authentication:** OpenAI API credentials

### 6. Parse AI Schedule & Motivation  
- **Type:** Function Node  
- **Function:** Parses AI response JSON with two keys:  
  - `schedule` (array of study session objects with title, start/end time, description, due date, priority)  
  - `motivationMessage` (string)  
- Splits schedule items for calendar/task creation; separates motivational text for notification.  
- Handles parse errors gracefully.

### 7. Create Calendar Events  
- **Type:** Google Calendar Node  
- **Operation:** Create event  
- **Details:** Uses AI schedule items to create study events with title, description, start and end times on primary Google Calendar.

### 8. Create/Update Tasks  
- **Type:** Todoist Node  
- **Operation:** Create task  
- **Details:** Creates or updates Todoist tasks with content, due date, and priority as per AI recommendations.

### 9. Send Motivation Notification  
- **Type:** Slack Node  
- **Operation:** Send message  
- **Channel:** Dynamic, defaults to `"default"` if not provided  
- **Message:** Motivational prompt from AI output or fallback message

---

## Credentials Required

- **Google Calendar OAuth2 API**: For reading and creating calendar events.
- **Todoist API**: For fetching and updating tasks.
- **OpenAI API**: For generating adaptive study schedules.
- **Slack API**: For sending motivational notifications.

---

## Execution Flow

1. Trigger fires daily at 7 AM.
2. Parallel fetching of calendar events and task list.
3. Aggregated data passed to AI for schedule generation.
4. AI response parsed into actionable calendar events and tasks.
5. New study sessions created in calendar and tasks updated in Todoist.
6. Motivational message delivered via Slack to encourage user.

---

## Customization Notes

- **Learning Progress & Preferred Times:** Currently static placeholders in the Aggregate Inputs node; replace with dynamic data sources as needed.  
- **Task List Selection:** Modify the expression in the Get Task List node to specify relevant Todoist project or label IDs.  
- **Slack Channel:** Adjust in Send Motivation Notification node to target desired workspace/channel.

---

## Error Handling

- AI response parsing includes error catch to prevent workflow failure and provides raw AI output for debugging.

---

## Purpose

This workflow empowers learners to proactively manage study time, adapt to upcoming commitments and progress, automate plan adjustments, and stay motivated daily with personalized notifications.