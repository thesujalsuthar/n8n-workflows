# Adaptive AI-Powered Personalized Study Planner

This workflow leverages OpenAI's GPT-4 model to create personalized, adaptive study schedules based on user preferences, past performance, and learning style. It integrates AI-generated recommendations, progress tracking, and database updates to provide an end-to-end study planning system.

---

## Workflow Overview

1. **Webhook Trigger**  
   Listens for incoming POST requests with user data including subjects, study hours, preferred study times, weak points, learning style, and progress data.

2. **AI Generate Study Schedule**  
   Sends a POST request to OpenAI's Chat Completion API (GPT-4) with a system prompt describing the AI as a study planner and user input as context. The AI responds with a personalized study schedule.

3. **Parse AI Response**  
   Extracts the AI-generated study schedule text from the API response for further use.

4. **Update Progress**  
   Updates the user's progress data by appending the current timestamp (`lastUpdated`).

5. **Save Progress to DB**  
   Performs an upsert operation into the PostgreSQL database, updating or inserting the user's progress information based on their `userId`.

6. **Send Notification**  
   Sends a notification message that includes the freshly generated study schedule and confirms that progress has been updated.

---

## Nodes Description

### Webhook Trigger

- **Type:** Webhook  
- **Method:** POST  
- **Path:** `/study-planner`  
- **Purpose:** Entry point to receive user data and trigger the study plan generation process.

### AI Generate Study Schedule

- **Type:** HTTP Request  
- **Method:** POST  
- **Endpoint:** `https://api.openai.com/v1/chat/completions`  
- **API Model:** `gpt-4`  
- **Authentication:** Bearer token in the `Authorization` header (`Bearer YOUR_OPENAI_API_KEY`)  
- **Request Body:** JSON containing system prompt and user input parameters dynamically injected from webhook data:  
  - Subjects  
  - Study hours per day  
  - Preferred study times  
  - Weak points  
  - Learning style  
  - Progress data  
- **Purpose:** Generates a personalized study schedule based on comprehensive user input.

### Parse AI Response

- **Type:** Function  
- **Functionality:** Extracts and returns the AI-generated schedule content from the OpenAI response JSON.

### Update Progress

- **Type:** Function  
- **Functionality:** Updates/augments the user's `progressData` by adding a `lastUpdated` timestamp indicating the update time.

### Save Progress to DB

- **Type:** PostgreSQL  
- **Operation:** Upsert on table `studyProgress`  
- **Key Field:** `userId`  
- **Credentials Required:**  
  - Host  
  - Port  
  - Database name  
  - User  
  - Password  
- **Purpose:** Stores or updates the user's latest study progress into the database.

### Send Notification

- **Type:** Set  
- **Functionality:** Constructs a notification message incorporating the AI-generated schedule and confirmation of progress update, ready to send to the user.

---

## Required Credentials & Setup

- **OpenAI API Key**  
  - Used for authenticating requests to OpenAI's GPT-4 API.  
  - Insert your key in the `Authorization` header of the "AI Generate Study Schedule" HTTP Request node as `Bearer YOUR_OPENAI_API_KEY`.

- **PostgreSQL Database Credentials**  
  - Needed to connect and upsert progress data into your database.  
  - Configure with your database host, port, name, user, and password in the "Save Progress to DB" node.

---

## Input Data Structure (Incoming POST Request)

The workflow expects the following JSON structure on the webhook POST:

```json
{
  "userId": "unique-user-id",
  "subjects": ["Subject1", "Subject2", "..."],
  "studyHoursPerDay": number,
  "preferredTimes": ["morning", "evening", "..."],
  "weakPoints": ["Topic1", "Topic2", "..."],
  "learningStyle": "visual | auditory | kinesthetic | etc.",
  "progressData": {
    // Any existing progress-related data
  }
}
```

---

## Output

- An adaptive, AI-generated study schedule tailored to the user's input.
- Updated progress data saved back to the database.
- Notification message containing the study schedule and update confirmation.

---

## How It Works (Step-by-Step)

1. User submits study preferences and historical progress data via the webhook endpoint.
2. Workflow triggers and sends this data to OpenAI GPT-4 with a prompt instructing the AI to generate a customized study plan.
3. AI response is parsed, and the generated study schedule is extracted.
4. Progress data is updated with the current timestamp.
5. Updated progress is saved in the PostgreSQL database.
6. A notification message containing the personalized study schedule is constructed and ready to be delivered.

---

## Notes

- Replace placeholder values like `YOUR_OPENAI_API_KEY` and database credentials with your actual configuration.
- Ensure your PostgreSQL database has a table named `studyProgress` with a `userId` column for upsert operations.
- Adapt notification delivery according to your messaging or UI system (the current workflow sets the message text for further integration).

---

End of README.md content.