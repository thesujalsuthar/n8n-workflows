# EcoAware Daily Sustainability Tips Workflow

This workflow automates the generation and delivery of personalized daily sustainability tips for users, leveraging user data, environmental data, and AI-generated insights. It runs every day at 8:00 AM and provides tailored tips to help users adopt more eco-friendly habits.

---

## Workflow Overview

1. **Daily Trigger**  
   Activates the workflow every day at 8:00 AM.

2. **Get Users**  
   Fetches the list of users from a user database/API using HTTP Basic Authentication.

3. **Prepare User Data**  
   Transforms the raw user data to extract relevant fields such as user ID, name, email, past sustainability habits, and location.

4. **Get Environmental Data**  
   Retrieves environmental data specific to each user's location via an authenticated API call.

5. **Merge Environmental Data**  
   Combines the user data with the corresponding environmental data, attaching the environmental context to each user record.

6. **Generate Sustainability Tips AI**  
   Utilizes the OpenAI GPT-4 model to generate 3 personalized daily sustainability tips for each user, based on their habits and local environmental conditions.

7. **Format Tips Output**  
   Extracts and formats the generated tips from the AI response for further processing and delivery.

8. **Update Habit Tracking**  
   Updates the habit tracking system via API with the daily tips sent to each user, including user ID and the current date for record-keeping.

9. **Send Email**  
   Sends an email to each user with their personalized sustainability tips using SMTP credentials.

---

## Node Details

### 1. Daily Trigger
- **Type:** Schedule Trigger
- **Schedule:** Daily at 08:00 AM
- **Purpose:** Starts the workflow automatically every day.

### 2. Get Users
- **Type:** HTTP Request
- **Operation:** GET user data
- **Authentication:** HTTP Basic Auth via `UserAPI Credentials`
- **Purpose:** Retrieves the full list of users with details required for personalization.

### 3. Prepare User Data
- **Type:** Function
- **Code:** Maps user data to include only `userId`, `name`, `email`, `pastHabits`, and `location`.
- **Purpose:** Simplifies data to focus on relevant fields for tip generation.

### 4. Get Environmental Data
- **Type:** HTTP Request
- **Operation:** GET environmental data filtered by user's location.
- **Authentication:** HTTP Basic Auth via `EnvironmentAPI Credentials`
- **Purpose:** Fetches localized environmental information to make tips contextually relevant.

### 5. Merge Environmental Data
- **Type:** Function
- **Code:** Associates each user with their matching environmental data.
- **Purpose:** Integrates user and environmental data for AI input.

### 6. Generate Sustainability Tips AI
- **Type:** OpenAI (GPT-4)
- **Prompt:**  
  - System: Defines the assistant as "EcoAware AI assistant" providing personalized sustainability tips.  
  - User: Requests 3 personalized tips based on user and environmental data.
- **Model:** GPT-4
- **Temperature:** 0.7 (for creative but coherent responses)
- **Authentication:** OpenAI API credentials
- **Purpose:** Generates customized sustainability advice.

### 7. Format Tips Output
- **Type:** Function
- **Code:** Extracts the actual tips message from AI response.
- **Purpose:** Prepares tips text for habit tracking update and email content.

### 8. Update Habit Tracking
- **Type:** HTTP Request
- **Operation:** Update habit tracking system with user ID, current date, and tips.
- **Authentication:** HTTP Basic Auth via `HabitTrack API Credentials`
- **Purpose:** Logs the daily tips sent to each user for tracking progress.

### 9. Send Email
- **Type:** Email Send
- **To:** User's email
- **Subject:** "Your Daily EcoAware Sustainability Tips"
- **Body:** Personalized message including user's name and their daily tips.
- **Authentication:** SMTP Credentials
- **Purpose:** Delivers the tips directly to the user's inbox.

---

## Credentials Required

- **UserAPI Credentials:** HTTP Basic Auth for accessing user data API.
- **EnvironmentAPI Credentials:** HTTP Basic Auth for accessing environmental data API.
- **OpenAI API:** For AI-powered sustainability tip generation.
- **HabitTrack API Credentials:** HTTP Basic Auth for updating habit tracking system.
- **SMTP Credentials:** For sending personalized emails.

---

## How It Works

- At 8 AM daily, the workflow triggers and pulls all users.
- User data is prepared and combined with location-based environmental data.
- The enriched data is sent to OpenAI's GPT-4 to create three daily tips suited to each user.
- Tips are formatted and logged into the habit tracking API.
- Finally, an email is sent to each user with their personalized sustainability tips.

This workflow ensures users receive relevant, actionable advice to support sustainable living every day.

---

## Important Notes

- Ensure all API credentials are properly set up and have sufficient permissions.
- The environmental API must support filtering by location to provide accurate context.
- OpenAI credentials require access to the GPT-4 model.
- Habit tracking API must accept updates for tracking daily tips sent.
- SMTP server should be configured correctly to send emails without issues.

---

This automation is designed to empower users with daily, personalized sustainability recommendations, promoting eco-awareness and positive habit formation.