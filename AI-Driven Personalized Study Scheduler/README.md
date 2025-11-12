# Adaptive AI-Powered Personalized Study Planner

This workflow creates a personalized weekly study plan tailored to a user's subjects, goals, and learning habits using AI, and then integrates the plan into the user's Google Calendar.

---

## Workflow Overview

The workflow consists of the following steps:

1. **Receive User Input:**  
   Accepts user-submitted data including subjects, goals, and learning habits via an HTTP POST request.

2. **Generate Prompt for AI:**  
   Constructs a detailed prompt based on the user input to request a customized study plan from the AI model.

3. **AI Generate Study Plan:**  
   Sends the prompt to OpenAI's GPT-4 model to generate a personalized weekly study plan with optimized daily time blocks, including breaks and review sessions.

4. **Parse AI Response:**  
   Extracts the AI-generated study plan content from the response.

5. **Extract Study Blocks:**  
   Attempts to parse the plan text into structured study blocks, each representing a study session with subject, topic, start time, and end time.

6. **Create Calendar Events:**  
   For each study block, creates corresponding events in the user's primary Google Calendar.

7. **Return Plan to User:**  
   Sends the final study plan back to the user as an HTTP response.

---

## Detailed Node Descriptions

### 1. Receive User Input (`httpTrigger`)

- **Method:** POST  
- **Endpoint:** `/submitStudyData`  
- **Function:** Receives JSON payload that must include:  
  - `subjects`: List of subjects to study  
  - `goals`: Learning goals  
  - `habits`: User's study habits

### 2. Generate Prompt for AI (`function`)

- Constructs a prompt string dynamically using the received user input.
- The prompt asks the AI to generate a personalized, optimized weekly study plan considering retention improvement and progress toward goals, including recommendations for breaks and review sessions.

### 3. AI Generate Study Plan (`openAi`)

- **Model:** GPT-4  
- **Resource:** Chat completion API  
- **Input:** The prompt generated in the previous step.  
- **Output:** AI-generated response containing the personalized study plan.

### 4. Parse AI Response (`function`)

- Extracts the AI's text response (the study plan) from the JSON structure received from OpenAI.
- Passes this content forward for parsing.

### 5. Extract Study Blocks (`function`)

- Attempts to parse the study plan text as JSON to convert into an array of study block objects.
- Each study block should ideally contain:  
  - `subject`  
  - `topic`  
  - `startTime` (ISO 8601 timestamp)  
  - `endTime` (ISO 8601 timestamp)  
- If parsing fails (e.g., the response is plain text), produces an empty array to avoid errors.
- Emits each study block as a separate item to be used for calendar event creation.

### 6. Create Calendar Events (`googleCalendar`)

- Authenticated with service account credentials.
- Creates events in the user's primary Google Calendar for each study block.  
- **Event fields:**  
  - **Summary:** `"Study: <Subject> - <Topic>"` (with fallback text if missing)  
  - **Description:** Full study plan text  
  - **Start/End:** Corresponding study block start and end times  
  - **Timezone:** UTC  
- Continues workflow execution even if event creation fails to avoid total failure.

### 7. Return Plan to User (`httpResponse`)

- Returns a HTTP 200 status with the generated study plan content back to the user, completing the request-response cycle.

---

## Input Data Format Example

```json
{
  "subjects": ["Math", "History", "Physics"],
  "goals": ["Improve algebra skills", "Understand WWII events", "Master basic physics concepts"],
  "habits": {
    "studyTime": "morning",
    "sessionLength": 60,
    "preferredBreaks": true
  }
}
```

---

## Expected AI Output Format

The AI is expected to return a weekly study plan in JSON format as an array of study blocks, each containing:

- `subject`: Study subject  
- `topic`: Specific topic to focus on  
- `startTime`: ISO 8601 string indicating session start time  
- `endTime`: ISO 8601 string indicating session end time

Example:

```json
[
  {
    "subject": "Math",
    "topic": "Algebra",
    "startTime": "2024-06-01T09:00:00Z",
    "endTime": "2024-06-01T10:00:00Z"
  },
  {
    "subject": "History",
    "topic": "WWII",
    "startTime": "2024-06-01T10:15:00Z",
    "endTime": "2024-06-01T11:15:00Z"
  }
]
```

---

## Prerequisites & Setup

- **n8n Automation Platform**: Installed and running.  
- **OpenAI API Access:** API key with GPT-4 model usage enabled.  
- **Google Calendar API Access:** Service account credentials with calendar write access, linked to the desired Google Calendar.  
- **HTTP Endpoint Exposure:** The workflow HTTP trigger endpoint `/submitStudyData` must be accessible to the clients submitting study data.

---

## How to Use

1. Send a POST request with JSON body containing `subjects`, `goals`, and `habits` to `/submitStudyData`.  
2. The workflow processes the data, generates the personalized study plan using AI, schedules study sessions in Google Calendar, and returns the plan in the response.  
3. Check your Google Calendar for newly created study session events.

---

## Error Handling

- Event creation continues on failure to avoid blocking response delivery.  
- If AI response is not in expected JSON format, no calendar events are created, but the plan is still returned.

---

## License

This workflow template is provided as-is without warranty. Customize as needed for your use case.