# Personalized Automated Learning Scheduler

This workflow automates the process of creating a personalized learning schedule for the upcoming week by analyzing calendar events and task reminders related to learning and study, then generating optimized recommendations using AI.

---

## Workflow Overview

The workflow triggers every day at 7:00 AM and performs the following steps:

1. **Daily Trigger**  
   Initiates the workflow on a daily basis at 7:00 AM.

2. **Get Calendar Events**  
   Retrieves all Google Calendar events scheduled between today and seven days ahead.

3. **Get Task Reminders**  
   Fetches all tasks from Todoist with due dates between today and the next 7 days.

4. **Filter Learning Items**  
   Filters calendar events and tasks to identify only those related to learning or studying by analyzing titles, descriptions, and task content for relevant keywords such as "learning," "study," "course," or "read."

5. **AI Recommendation**  
   Sends the filtered learning items to the OpenAI GPT-4 model to generate a personalized and optimized learning schedule for the coming week. The AI considers progress, focus areas, and study-time balance.

6. **Parse Recommendation**  
   Extracts the AI’s textual schedule recommendation from the API response.

7. **Create Scheduled Event**  
   Creates a new Google Calendar event titled "Personalized Learning Session" with the AI-generated recommendation placed in the description. The event is initially scheduled for one hour starting at the current time.

---

## Nodes Breakdown

### 1. Daily Trigger  
- **Type:** Schedule Trigger  
- **Trigger time:** 7:00 AM every day  

### 2. Get Calendar Events  
- **Type:** Google Calendar (Get All Events)  
- **Date Range:** Today → 7 days later  
- **Credentials:** Google Calendar account required  

### 3. Get Task Reminders  
- **Type:** Todoist (Get All Tasks)  
- **Date Range:** Due after today and before next 7 days  
- **Credentials:** Todoist API key required  

### 4. Filter Learning Items  
- **Type:** Function  
- **Description:**  
  - Flattens and processes input arrays from calendar events and tasks.  
  - Filters calendar events by checking if their title or description contains keywords (`learning`, `study`).  
  - Filters tasks by checking if their content contains keywords (`learn`, `study`, `course`, `read`).  
  - Outputs a combined list of flagged learning events and tasks.

### 5. AI Recommendation  
- **Type:** HTTP Request (OpenAI Chat Completion API)  
- **Model:** GPT-4  
- **Prompt:**  
  - System role instructs the AI to act as an expert learning scheduler.  
  - User role sends JSON string of filtered learning items requesting an optimized schedule with time slots and priorities.  
- **Authentication:** OpenAI API key required  

### 6. Parse Recommendation  
- **Type:** Function  
- **Description:** Extracts the textual recommendation from the AI API response for further use.

### 7. Create Scheduled Event  
- **Type:** Google Calendar (Create Event)  
- **Event Summary:** Personalized Learning Session  
- **Event Description:** AI-generated personalized schedule recommendation  
- **Event Timing:** Starts at current time, lasts 1 hour  
- **Credentials:** Google Calendar account required

---

## Setup and Configuration

1. **Google Calendar API Credentials**  
   - Connect your Google Calendar account via OAuth2 API credentials.  
   - Ensure the workflow has permission to read events and create new ones.

2. **Todoist API Credentials**  
   - Add your Todoist API token to fetch task reminders related to learning.

3. **OpenAI API Credentials**  
   - Provide your API key to authorize HTTP requests to the OpenAI Chat Completion API.

4. **Adjust Trigger Time (Optional)**  
   - Modify the `Daily Trigger` node in the workflow to change the time it runs.

---

## Usage

Once configured, the workflow will:

- Automatically collect all upcoming learning-related events and tasks for the next week daily.
- Use AI to analyze these items and devise an optimal study schedule.
- Create a personalized learning event on your Google Calendar with detailed recommendations.

---

## Notes

- Keywords used to identify learning items can be customized in the `Filter Learning Items` function if needed.
- The scheduled event currently uses the current timestamp; you may adapt the scheduling logic to fit your preferred study times.
- Make sure all credentials are valid and have sufficient API permissions.

---

## License

This workflow is provided as-is for personal automation and learning productivity enhancement. Modify and use freely according to your needs.