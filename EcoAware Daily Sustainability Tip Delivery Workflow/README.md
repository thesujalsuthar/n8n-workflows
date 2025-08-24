# Daily Personalized Sustainability Tips Workflow

This workflow automates the process of sending personalized daily sustainability tips to users based on their preferences and habit data. It leverages AI to generate actionable tips and integrates with a database and SMS service for delivery and tracking.

---

## Workflow Overview

1. **Daily Trigger**  
   The workflow is triggered every day at 9:00 AM.

2. **Fetch Users**  
   Retrieves all users from the `users` table in the database.

3. **Fetch Habit Data**  
   For each user, fetches their habit data from the `habits` table filtered by the user's ID.

4. **Generate Tip**  
   Sends user preferences and habit data to OpenAI's GPT-4 model to generate one personalized, actionable sustainability tip for the day.

5. **Prepare Notification**  
   Formats the generated tip and associates it with the user's ID to prepare for sending.

6. **Send SMS**  
   Uses Twilio to send the personalized sustainability tip as an SMS to the user's phone number.

7. **Record Habit Data**  
   Inserts a new record in the `habit_tracking` table to log the tip sent today along with the user ID, current date, and sets the habit progress as "pending".

---

## Node Details

### 1. Daily Trigger  
- Type: Schedule Trigger  
- Activation Time: Every day at 09:00 AM  
- Purpose: Starts the workflow execution daily.

### 2. Fetch Users  
- Type: Database (Find Many)  
- Table: `users`  
- Purpose: Retrieves all user records for personalization.

### 3. Fetch Habit Data  
- Type: Database (Find Many)  
- Table: `habits`  
- Filter: `user_id` equals the current user's ID  
- Purpose: Fetches habit history for each user to tailor tips accordingly.

### 4. Generate Tip  
- Type: OpenAI (GPT-4 model)  
- Prompt:  
  System message defines the assistant role to generate personalized sustainability tips.  
  User message includes user preferences and habit data for context.  
- Parameters:  
  - Temperature: 0.7  
  - Max Tokens: 150  
- Purpose: Generates a unique, actionable sustainability tip personalized per user.

### 5. Prepare Notification  
- Type: Set  
- Values:  
  - `message`: The generated tip content from GPT-4 response  
  - `userId`: The current user's ID  
- Purpose: Prepares the message payload to send and track.

### 6. Send SMS  
- Type: Twilio  
- Phone Number: User's phone number from the database  
- Message: Prefixed with "EcoAware Tip:" followed by the generated tip  
- Purpose: Delivers the daily sustainability tip directly to the user via SMS.

### 7. Record Habit Data  
- Type: Database (Insert)  
- Table: `habit_tracking`  
- Fields:  
  - `user_id`: Current user's ID  
  - `date`: Current date/time  
  - `tip_sent`: The sustainability tip message sent  
  - `habit_progress`: Set as `"pending"`  
- Purpose: Logs the sent tip and marks habit progress tracking.

---

## Database Schema Assumptions

- **users** table: Contains user details including `id`, `preferences`, and `phone`.
- **habits** table: Contains habit entries with a foreign key `user_id`.
- **habit_tracking** table: Stores daily records of sent tips and progress with fields `user_id`, `date`, `tip_sent`, and `habit_progress`.

---

## External Service Requirements

- **OpenAI API**: Access to GPT-4 model for generating tips.
- **Twilio**: SMS sending configured with a valid phone number and credentials.
- **Database**: Supports operations to read from `users` and `habits`, and insert into `habit_tracking`.

---

## Execution Flow Summary

At 9:00 AM daily, the workflow fetches all users, pulls their habit data, then uses AI to generate a customized sustainability tip. This tip is sent via SMS, and the interaction is recorded in the database for tracking progress.

---

## Contact / Support

For questions or support implementing this workflow, please refer to the official n8n documentation or contact your system administrator.