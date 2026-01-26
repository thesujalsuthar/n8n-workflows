# AI-Driven Community SkillSwap Automation System

This workflow automates the coordination and management of SkillSwap sessions within a community by leveraging AI for member matching, scheduling events, and sending notifications. It runs daily to facilitate seamless SkillSwap experiences.

---

## Workflow Overview

The system consists of two main automated processes:

1. **SkillSwap Session Scheduling (Morning Run at 9:00 AM)**
2. **Daily Follow-Up Emails (Evening Run at 6:00 PM)**

---

## Detailed Node Description

### 1. Daily Trigger (9:00 AM)

- **Type:** Schedule Trigger  
- **Function:** Initiates the workflow every day at 9:00 AM to start the SkillSwap matching and scheduling process.

---

### 2. Fetch Community Members

- **Type:** HTTP Request  
- **Authentication:** API Key (`CommunityAPIKey`)  
- **Function:** Queries the community API to retrieve up to 50 member profiles with the search term `"skillswap profile"`.  
- **Output:** JSON list of member profiles including their skills offered, skills wanted, email, name, and location.

---

### 3. Match Members

- **Type:** Function  
- **Function:** Processes fetched profiles to find SkillSwap matches by:
  - Grouping members by skills they offer and want.
  - Matching members where:
    - One member offers a skill another wants.
    - Both members are in the same location.
    - They are different individuals.
- **Output:** List of matched pairs with details:
  - Skill
  - Offerer profile
  - Wanter profile

---

### 4. Schedule Event

- **Type:** Google Calendar Node  
- **Authentication:** Google Calendar OAuth2 (`Google Calendar OAuth2`)  
- **Function:** For each matched pair, creates a calendar event titled:
  - `SkillSwap Session: [Skill] with [Offerer Name] & [Wanter Name]`
- **Event Details:**
  - Start time: Current time at execution
  - End time: One hour later
  - Attendees: Offerer and Wanter emails
  - Description includes offerer and wanter names and skill involved

---

### 5. Send Confirmation Email

- **Type:** Email Send  
- **Authentication:** SMTP (`Community SMTP`)  
- **Function:** Sends a confirmation email to both offerer and wanter post event creation containing:
  - Subject: "Your SkillSwap session is scheduled!"
  - Body: Confirmation message with skill and participant names encouraging them to check the calendar.

---

### 6. Daily Follow-Up Trigger (6:00 PM)

- **Type:** Schedule Trigger  
- **Function:** Initiates the follow-up process daily at 6:00 PM to engage members after their SkillSwap sessions.

---

### 7. Fetch Yesterday Events

- **Type:** HTTP Request  
- **Authentication:** API Key (`CommunityAPIKey`)  
- **Function:** Retrieves events from the community API created in the last 24 hours (from yesterday). Focuses on events within the date range between 24 hours ago and now.

---

### 8. Parse Yesterday Events

- **Type:** Function  
- **Function:** Filters the fetched events to only include those containing `"SkillSwap Session"` in the summary and extracts:
  - Event ID
  - Skill name
  - Attendees list

---

### 9. Send Follow-Up Email

- **Type:** Email Send  
- **Authentication:** SMTP (`Community SMTP`)  
- **Function:** Sends a thank-you email to all attendees of the SkillSwap sessions held yesterday, encouraging feedback and continued participation.  
- **Email Content:**
  - Subject: "Thanks for participating in your SkillSwap session!"
  - Body: Gratitude message mentioning the skill from the session.

---

## Workflow Connections

- **Morning Sequence:**  
  `Daily Trigger` → `Fetch Community Members` → `Match Members` → `Schedule Event` → `Send Confirmation Email`

- **Evening Sequence:**  
  `Daily Follow-Up Trigger` → `Fetch Yesterday Events` → `Parse Yesterday Events` → `Send Follow-Up Email`

---

## Required Credentials

- **Community API Key:** Access for HTTP requests to community data.  
- **Google Calendar OAuth2:** Permissions to create and manage calendar events for matched members.  
- **Community SMTP:** For sending emails to participants for confirmations and follow-ups.

---

## Summary

This AI-driven SkillSwap Automation System optimizes community skill exchanges by:

- Automating daily skill-based matching of members.
- Scheduling SkillSwap sessions with calendar invites.
- Sending timely confirmation and follow-up emails.
- Driving member engagement and continuous participation in community skill-sharing.

Schedule triggers ensure that the workflow runs unattended twice daily, maintaining a consistent and organized SkillSwap program.