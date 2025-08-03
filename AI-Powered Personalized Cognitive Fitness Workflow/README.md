# AI-Driven Personalized Mental Fitness and Cognitive Exercise Planner

This workflow is designed to generate personalized mental fitness and cognitive exercises tailored to each user's profile, log these exercises to Google Sheets for tracking, and email the user their personalized exercises for the day.

---

## Workflow Overview

The workflow consists of the following steps:

1. **Start (Manual Trigger)**  
   Initiates the workflow manually to generate exercises for a specific user.

2. **Get User Profile**  
   Retrieves the user's profile information using an OAuth2 authenticated HTTP request. The user ID is dynamically supplied, enabling retrieval of personalized data such as name and email.

3. **Generate Personalized Exercises**  
   Generates a set of 3 personalized cognitive exercises based on the user profile.  
   - Exercises target cognitive abilities such as memory, attention, language, problem-solving, and flexibility.  
   - For demonstration, exercises are randomly selected from a predefined list, simulating an AI-driven recommendation.

4. **Log Exercises to Google Sheets**  
   Appends the generated exercises along with the date and user ID to a specified Google Sheets spreadsheet for record-keeping and tracking progress over time.

5. **Send Exercise Email**  
   Sends an email to the user with their personalized cognitive exercises for the day, encouraging continued mental fitness practice.

---

## Node Details

### 1. Start  
- **Type:** Manual Trigger  
- **Purpose:** Begin the workflow execution for a given user.

### 2. Get User Profile  
- **Type:** HTTP Request  
- **Authentication:** OAuth2  
- **Operation:** GET user information by user ID (`{{$json["userId"]}}`)  
- **Output:** User's profile including fields like name and email to enable personalized exercise generation and communication.

### 3. Generate Personalized Exercises  
- **Type:** Function  
- **Logic:**  
  - Defines a fixed array of cognitive exercises targeting different skills.  
  - Randomly selects 3 exercises to personalize the session for the user.  
  - Outputs an array `personalizedExercises` containing the selected exercises, each with `type`, `task`, and `duration`.

### 4. Log Exercises to Google Sheets  
- **Type:** Google Sheets Append  
- **Parameters:**  
  - `sheetId`: Replace `"YOUR_SHEET_ID_HERE"` with your Google Sheet ID.  
  - Data appended includes:  
    - Date (ISO format, current day)  
    - User ID  
    - JSON stringified list of personalized exercises  
- **Authentication:** Google Sheets OAuth2

### 5. Send Exercise Email  
- **Type:** Email Send  
- **Parameters:**  
  - `toEmail`: User's email from the profile (`{{$json["email"]}}`)  
  - `subject`: "Your Personalized Cognitive Exercises for Today"  
  - `message`: A personalized message including the user's name and a formatted list of exercises with task descriptions and estimated durations.  
- **Authentication:** SMTP credentials (configure in your n8n instance for sending emails)

---

## Setup Instructions

1. **Configure OAuth2 Credentials**  
   - Set up OAuth2 credentials for your user profile API in n8n.  
   - Provide these credentials in the "Get User Profile" node.

2. **Google Sheets Integration**  
   - Create or identify the Google Sheets worksheet to log exercises.  
   - Obtain the Sheet ID and replace `"YOUR_SHEET_ID_HERE"` in the "Log Exercises to Google Sheets" node.  
   - Configure Google Sheets OAuth2 credentials in n8n and assign them to this node.

3. **SMTP Setup**  
   - Configure SMTP credentials in n8n for sending emails.  
   - Assign the credentials to the "Send Exercise Email" node.

4. **Triggering Workflow**  
   - Provide or map the appropriate `userId` when triggering the workflow manually or from automated triggers.  
   - The `userId` will be used to retrieve user information and personalize the exercise recommendations.

---

## Exercise Generation Logic

- Predefined exercises include tasks that improve:  
  - Memory (e.g., sequence recall)  
  - Attention (e.g., letter searching in text)  
  - Language (word formation)  
  - Problem Solving (simple math problem)  
  - Flexibility (task switching)  
- Three exercises are randomly selected per session to keep the routine varied and engaging.

---

## Data Logged in Google Sheets

Each row contains:  
- **Date:** The execution date in YYYY-MM-DD format  
- **UserId:** The user identifier  
- **Exercises:** JSON string containing an array of the exercises generated with task descriptions and durations

---

## Email Format

The email includes:  
- Personalized greeting with the user’s name  
- List of exercises formatted as:  
  `- (Exercise Type) Task Description (approx. Duration mins)`  
- Encouragement message for maintaining cognitive health  
- Signature from the Mental Fitness Team

---

## Notes

- The exercise generation in this workflow is a simple demonstrative example. You may extend or replace the function node’s code with calls to AI APIs or more sophisticated logic for true personalization.  
- Ensure all credentials (OAuth2 for user data and Google Sheets, SMTP for email) are securely configured in your n8n environment before running the workflow.  
- Replace placeholder values like `YOUR_SHEET_ID_HERE` with real values before deployment.

---

This workflow streamlines the process of engaging users in daily cognitive exercises using AI-driven recommendations, systematic logging, and direct email communication, supporting improved mental fitness and personalized cognitive development.