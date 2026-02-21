# AI-Driven Personalized Daily Sustainability Challenge and Progress Tracker

This workflow automates the process of assigning personalized daily sustainability challenges to users, tracking their completion status, sending reminders, and delivering weekly summary reports with actionable tips. It utilizes an AI-driven approach to tailor challenges based on individual user preferences.

---

## Workflow Overview

### Key Features
- Assigns daily personalized sustainability challenges based on user preferences.
- Sends daily challenge emails to users every morning.
- Stores assigned challenges and completion status in a PostgreSQL database.
- Sends evening reminders about challenge completion status.
- Delivers a weekly summary email every Monday with progress and tips.

---

## Workflow Details

### 1. Daily Trigger (`Daily Trigger`)
- Scheduled to run daily at **8:00 AM**.
- Initiates the process of assigning daily challenges.

### 2. Get Users (`Get Users`)
- Retrieves a static list of users with their preferences for sustainability categories:
  - Waste Reduction
  - Energy Saving
  - Water Conservation
- Example users:
  - Alice: prefers waste reduction and energy saving.
  - Bob: prefers energy saving and water conservation.

### 3. Assign Challenge (`Assign Challenge`)
- Contains predefined sustainability challenges across the three categories.
- Filters challenges based on each user's preferences.
- Randomly assigns one suitable challenge to each user.
- If no challenge matches a user's preferences, a message is returned indicating no available challenge.

### 4. Send Challenge Email (`Send Challenge Email`)
- Sends an email to each user with their personalized daily sustainability challenge.
- Email includes challenge title and detailed description.
- Sender email: `noreply@sustainabilityapp.com`.
- Subject: "Your Daily Sustainability Challenge".

### 5. Store Challenge In DB (`Store Challenge In DB`)
- Upserts the assigned challenge into the `user_challenges` table in PostgreSQL.
- Stores fields:
  - `userId`
  - `challengeId`
  - `challengeTitle`
  - `challengeDescription`
  - `dateAssigned` (current date)
  - `completed` (default `false`)
- Uses the key column `userId` to update or insert.

### 6. Daily Completion Check Trigger (`Daily Completion Check Trigger`)
- Scheduled to run daily at **8:00 PM**.
- Initiates the daily check for challenge completion.

### 7. Get Today's Challenges From DB (`Get Today's Challenges From DB`)
- Queries PostgreSQL database to retrieve today’s assigned challenges for all users.
- Retrieves data including completion status and user email.

### 8. Prepare Completion Reminder (`Prepare Completion Reminder`)
- For each user, checks if the daily challenge is marked as completed.
- Prepares a personalized reminder message:
  - If completed: congratulates the user.
  - If not completed: reminds the user to complete the challenge.

### 9. Send Completion Reminder Email (`Send Completion Reminder Email`)
- Sends the personalized completion status reminder to each user via email.
- Subject: "Reminder: Daily Sustainability Challenge Completion".

### 10. Weekly Summary Trigger (`Weekly Summary Trigger`)
- Scheduled to run every Monday at **9:00 AM**.
- Initiates the process to compile weekly sustainability challenge summaries for users.

### 11. Get Last Week Challenges (`Get Last Week Challenges`)
- Queries PostgreSQL for all user challenges assigned between 7 days ago and today.
- Retrieves challenge completion data for the past week.

### 12. Generate Weekly Summary (`Generate Weekly Summary`)
- Aggregates user challenges and calculates:
  - Number of challenges assigned.
  - Number of completed challenges.
  - Completion rate percentage.
- Generates personalized tips based on completion rate:
  - **< 50%**: Suggests setting reminders and journaling.
  - **50% - 80%**: Encourages completing every challenge.
  - **> 80%**: Recommends sharing progress to inspire others.

### 13. Add User Info to Summary (`Add User Info to Summary`)
- Adds user email and name to each weekly summary item to personalize email content.

### 14. Send Weekly Summary Email (`Send Weekly Summary Email`)
- Sends the weekly summary and tips via email to each user.
- Subject: "Your Weekly Sustainability Summary & Tips".

---

## Database Schema

### Table: `user_challenges`
| Column             | Type      | Description                        |
|--------------------|-----------|----------------------------------|
| userId             | VARCHAR   | Unique identifier for the user   |
| challengeId        | VARCHAR   | Unique challenge identifier       |
| challengeTitle     | TEXT      | Title of the challenge            |
| challengeDescription | TEXT    | Detailed description of challenge |
| dateAssigned       | DATE      | Date the challenge was assigned   |
| completed          | BOOLEAN   | Completion status of the challenge|
| email *(optional)* | VARCHAR   | User email for reminders          |

---

## Email Configuration

- All emails are sent from: `noreply@sustainabilityapp.com`
- Personalization uses user-specific details such as name, challenge title, descriptions, and completion feedback.
- Supports sending:
  - Daily challenge assignment.
  - Evening completion reminders.
  - Weekly progress summaries and tips.

---

## Scheduling Summary

| Event                     | Time (Local) | Frequency   |
|---------------------------|--------------|-------------|
| Assign daily challenges   | 08:00 AM     | Daily       |
| Send completion reminders | 08:00 PM     | Daily       |
| Send weekly summary       | 09:00 AM     | Weekly (Mon)|

---

## Notes

- User data currently loaded statically within the workflow and can be replaced by dynamic user management systems.
- Challenges are predefined and categorized; additional challenges can be added to the list as needed.
- PostgreSQL database credentials must be configured in n8n with the name **Postgres SustainabilityDB**.
- The workflow assumes users can mark challenges as completed externally; completion status is reflected in the database.
- Email node configurations require valid SMTP credentials for sending emails.

---

Thank you for contributing to a sustainable future with this AI-driven personalized engagement workflow!