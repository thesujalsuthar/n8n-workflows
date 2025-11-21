# AI-Powered Personalized Study Companion Workflow

This workflow is designed to deliver personalized study reminders and recommendations tailored to a user's unique learning profile, study preferences, and recent performance. It operates on a schedule, providing twice-daily, AI-driven study suggestions and motivational feedback via email, along with updating the user’s study data in a database.

---

## Overview

- **Trigger:** Runs automatically twice a day at 7:00 AM and 6:00 PM.
- **User Profile:** Simulated fetch of user data including learning style, performance metrics, and study preferences.
- **Content Suggestions:** Generates adaptive content recommendations focused on weak topics and tailored to the user’s preferred learning style and subjects.
- **Motivational Feedback:** Creates personalized encouragement based on recent test scores and study habits.
- **Reminder Composition:** Crafts a customized study reminder message including content suggestions and preferred study times.
- **Email Notification:** Sends the personalized study reminder to the user’s email.
- **Database Update:** Saves the latest study session data, content suggestions, and motivational feedback to the database.
- **Dashboard Summary:** Prepares a summary of feedback and key suggestions for potential dashboard integration.

---

## Workflow Nodes and Their Functions

### 1. Scheduled Trigger - Daily Reminders

- **Type:** Schedule Trigger
- **Details:** Initiates the workflow twice daily at 7:00 AM and 6:00 PM.

### 2. Fetch User Profile & Performance

- **Type:** Function
- **Details:** Simulates fetching user data such as:
  - User ID
  - Learning style (visual, auditory, kinesthetic)
  - Performance metrics (last test score, average daily study time, weak topics)
  - Study preferences (preferred study times, subjects)
  - Last study session date

### 3. Generate Content Suggestions

- **Type:** Function
- **Details:** 
  - Uses the user's weak topics and learning style to select relevant learning content from a predefined content pool.
  - Filters suggested topics to include only those within the user's preferred subjects.
  - Content types vary by topic and learning style, e.g., videos, podcasts, interactive exercises.

### 4. Generate Motivational Feedback

- **Type:** Function
- **Details:** 
  - Creates personalized motivational messages based on the last test score and study time habits.
  - Encourages improving weak areas or praises consistent study behaviors.

### 5. Compose Study Reminder

- **Type:** Function
- **Details:** 
  - Builds a reminder message including a list of up to 5 suggested study items.
  - Mentions the user's preferred study times.
  - Message is customized to improve engagement based on learning profile.

### 6. Send Email Reminder

- **Type:** Email Send
- **Details:**
  - Sends the composed study reminder message as an email.
  - Uses a from email of `studybuddy@n8n.ai`.
  - Sends to the user's email constructed from their user ID (`user_12345@example.com`).

### 7. Prepare Summary for Dashboard

- **Type:** Function
- **Details:** 
  - Summarizes motivational feedback and top 3 content suggestions.
  - Intended for integration with any user dashboard or reporting system.

### 8. Update User Study Data in DB

- **Type:** MongoDB
- **Details:** 
  - Upserts user study data into the database collection `userStudyData` including:
    - User ID
    - Timestamp of the current study session
    - Content suggestions
    - Motivational feedback

---

## Data Flow and Connections

- The trigger initiates fetching user data.
- User data splits into generating content suggestions and motivational feedback.
- Content suggestions feed into composing the study reminder and database update.
- Motivational feedback feeds into summary preparation and database update.
- The composed reminder triggers sending an email to the user.
- The database is reliably updated with the latest user study data.

---

## Configuration Details

- **Schedule:** 7:00 AM and 6:00 PM daily.
- **Email:**
  - From: `studybuddy@n8n.ai`
  - To: User's dynamic email based on user ID (`user_<id>@example.com`)
  - Subject: "Your Personalized Study Reminder & Recommendations"
  - Body: Dynamic personalized content suggestions and motivational message.
- **Database:**
  - Resource: MongoDB
  - Collection: `userStudyData`
  - Operation: Upsert (to update or insert study session data)

---

## How to Use

1. Import the workflow into your n8n instance.
2. Configure the MongoDB node with your database credentials.
3. Set up SMTP credentials for the email node.
4. Adjust content pools or study preferences as necessary for your users.
5. Activate the workflow.
6. Users will begin receiving personalized study reminders twice daily.

---

## Notes

- User data fetching is currently simulated; replace with real API or database calls.
- Content pool and motivational messages can be expanded or customized.
- Email recipient addresses are constructed programmatically; update as per your user base.
- Make sure to configure all credentials securely in n8n.
- The workflow can be extended to integrate with mobile push notifications or dashboards.

---

This workflow empowers learners by providing personalized, data-driven study guidance, improving learning outcomes through targeted recommendations and motivational support.