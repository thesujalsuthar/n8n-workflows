# AI-Powered Local Community Skill Sharing & Bartering Platform Workflow

This workflow automates the process of matching local community users based on their skills offered and needed, facilitates scheduling skill exchange sessions, and sends notifications via email and calendar invites. Built for n8n automation, it integrates with MongoDB, email (SMTP), and Google Calendar.

---

## Workflow Overview

1. **Retrieve Users**  
   Fetches all users from the MongoDB `users` collection, including their skills offered and skills needed.

2. **Match Users for Reciprocal Skill Exchange**  
   Processes all users to find pairs where User A offers skills that User B needs and User B offers skills that User A needs.

3. **Fetch Detailed User Profiles**  
   Retrieves detailed records of each matched pair from MongoDB by `_id`.

4. **Prepare and Send Notification Email of Match**  
   Sends an email to both users notifying them of their skill exchange match, outlining who offers what and what each needs.

5. **Propose Scheduling Date**  
   Calculates the next available Saturday at 10 AM as a default proposed date/time for the skill sharing session.

6. **Create Google Calendar Event**  
   Creates a calendar invite in Google Calendar for both users with session details.

7. **Send Scheduling Confirmation Email**  
   Confirms session scheduling via email, prompting users to check their calendars.

---

## Nodes Description

### 1. Get All Users  
- **Type:** MongoDB  
- **Operation:** Find all documents in `users` collection.  
- **Credentials:** MongoDB Credential.  
- **Purpose:** Fetches all user profiles including skill offerings and needs.

### 2. Match Users for Skill Exchange  
- **Type:** Function  
- **Logic:**  
  - Maps all users’ skills offered and skills needed in lowercase for consistent matching.  
  - Finds reciprocal matches where two users’ skill offerings and needs align.  
  - Outputs matched pairs including user IDs, skills offered, and skills needed.  

### 3. Get User A Details  
- **Type:** MongoDB  
- **Operation:** Find one user document by User A’s `_id`.  
- **Purpose:** To get detailed info on matched User A.

### 4. Get User B Details  
- **Type:** MongoDB  
- **Operation:** Find one user document by User B’s `_id`.  
- **Purpose:** To get detailed info on matched User B.

### 5. Prepare Notification Email  
- **Type:** Function  
- **Logic:**  
  - Combines details from User A, User B, and match info.  
  - Prepares a personalized email to both users with details of skills offered and needed, and instructions for coordination.

### 6. Send Match Notification Email  
- **Type:** Email Send  
- **Parameters:**  
  - From: `community@n8n.io`  
  - To: User A and User B emails  
  - Subject: Skill Exchange Match with skills involved  
  - Body: Text email generated from the previous node.  
- **Credentials:** SMTP Credential.

### 7. Propose Scheduling Date  
- **Type:** Function  
- **Logic:**  
  - Calculates next Saturday at 10:00 AM as default session time.  
  - Returns this proposed date/time alongside match details.

### 8. Create Calendar Event  
- **Type:** Google Calendar  
- **Operation:** Create calendar event in the primary calendar.  
- **Details:**  
  - Title indicates skill sharing session between matched users.  
  - Description includes the skills exchanged.  
  - Event duration is 1 hour starting at proposed date/time.  
- **Credentials:** Google Calendar OAuth2 API.

### 9. Send Scheduling Confirmation Email  
- **Type:** Email Send  
- **Parameters:**  
  - From: `community@n8n.io`  
  - To: User A and User B emails retrieved from the users list  
  - Subject: Confirmation for scheduled skill sharing session  
  - Body: Confirms session scheduling with proposed date/time and calendar reminder.  
- **Credentials:** SMTP Credential.

---

## Credentials Required

- **MongoDB Credential**: Access to the MongoDB instance where the users collection is stored.  
- **SMTP Credential**: For sending notification and confirmation emails.  
- **Google Calendar OAuth2 API Credential**: For creating and managing Google Calendar events.

---

## Data Structure Assumptions

- **Users Collection:**  
  Each user document contains:  
  - `_id`: Unique identifier.  
  - `name`: User's full name.  
  - `email`: Valid email address.  
  - `skills_offered`: Array of skill names the user offers.  
  - `skills_needed`: Array of skill names the user needs.

---

## How the Matching Works

- The workflow identifies pairs of users where User A offers one or more skills that User B needs and vice versa.  
- It ensures reciprocal benefit by checking if User A’s needs are offered by User B.  
- Matches with overlapping skills are compiled for notification and scheduling.

---

## Scheduling Logic

- Default proposed skill sharing sessions are scheduled for the upcoming Saturday at 10 AM local time.  
- The event duration is one hour.  
- Matching users are notified and the session is added to their Google Calendars automatically.

---

## Workflow Triggers

- This workflow can be triggered manually or scheduled to run periodically to find new matches and coordinate sessions continuously.

---

## Summary

This workflow creates an efficient, automated system for a community skill sharing platform where users barter skills locally, fostering collaborative learning and exchange by intelligently matching needs and offers, organizing sessions, and communicating with participants seamlessly.