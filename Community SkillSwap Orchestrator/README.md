# Community SkillSwap Automation Manager

This n8n workflow automates the key processes of the Community SkillSwap platform, managing skill offer/request collection, skill matching, event creation, reminder emails, and feedback processing.

---

## Workflow Overview

### 1. Daily Scheduler
- **Node:** `Daily Scheduler`
- **Type:** Cron Trigger
- **Function:** Triggers the workflow once daily at 09:00 AM to perform all scheduled tasks.

---

### 2. Get Skill Offers and Requests
- **Node:** `Get Skill Offers and Requests`
- **Type:** Google Forms - Get All Responses
- **Form ID:** `community_skillswap_offers_requests`
- **Function:** Fetches all available submissions from the SkillSwap offers and requests form without filters.

---

### 3. Categorize Offers And Requests
- **Node:** `Categorize Offers And Requests`
- **Type:** Function
- **Function:**
  - Parses all form responses.
  - Extracts users’ intent (`offering` or `requesting`), skill requested/offered, and user email.
  - Separates responses into two arrays: `skillOffers` and `skillRequests`.

---

### 4. Match Skills in Real-Time
- **Node:** `Match Skills in Real-Time`
- **Type:** Function
- **Function:**
  - Compares all requests against all offers.
  - Matches requests and offers by identical skill (case insensitive).
  - Generates a list of matched pairs with skill name and both participants' emails.

---

### 5. Create SkillSwap Event in Calendar
- **Node:** `Create SkillSwap Event in Calendar`
- **Type:** Google Calendar - Create Event
- **Settings:**
  - **Calendar:** Primary
  - **Summary:** "SkillSwap Event: [Skill]"
  - **Description:** Details the SkillSwap event connecting requester and offerer.
  - **Start Time:** 3 days from current date, at the current time slot.
  - **End Time:** 1 hour after start time.
  - **Attendees:** Both requester and offerer emails.
- **Function:** Automatically schedules calendar events for each matched skill pair.

---

### 6. Get Upcoming SkillSwap Events
- **Node:** `Get Upcoming SkillSwap Events`
- **Type:** Google Calendar - Get Events
- **Settings:**
  - **Calendar:** Primary
  - **Time window:** From now until 4 days ahead.
  - **Query:** Contains keyword "SkillSwap"
- **Function:** Retrieves all upcoming SkillSwap events within the next 4 days.

---

### 7. Prepare Reminder Emails
- **Node:** `Prepare Reminder Emails`
- **Type:** Function
- **Function:**
  - Iterates through all retrieved upcoming SkillSwap events.
  - Gathers the event summary, start time, event ID, and each attendee’s email.
  - Prepares a reminder email payload for each attendee.

---

### 8. Send Reminder Email
- **Node:** `Send Reminder Email`
- **Type:** Email Send
- **Email Settings:**
  - **From:** community@skillswap.org
  - **To:** Extracted attendee email.
  - **Subject:** Reminder: Upcoming SkillSwap Event [Event Summary]
  - **Body:** Friendly reminder with event details and schedule.
- **Function:** Sends out reminder emails to all attendees for their upcoming SkillSwap events.

---

### 9. Get Feedback Responses
- **Node:** `Get Feedback Responses`
- **Type:** Google Forms - Get All Responses
- **Form ID:** `community_skillswap_feedback`
- **Function:** Fetches all feedback collected about past SkillSwap events.

---

### 10. Parse Feedback
- **Node:** `Parse Feedback`
- **Type:** Function
- **Function:**
  - Extracts key information from feedback responses:
    - Event attended
    - User email
    - Feedback message

---

### 11. Send Feedback to API
- **Node:** `Send Feedback to API`
- **Type:** HTTP Request - POST
- **Endpoint:** `https://api.communityskillswap.org/feedback`
- **Body:** JSON payload containing parsed feedback data.
- **Function:** Posts user feedback to the centralized Community SkillSwap API for further processing or analysis.

---

## Execution Flow Summary

1. At 9:00 AM daily:
   - Fetch skill offers and requests.
   - Categorize them.
   - Match skill seekers with providers.
   - Schedule calendar events for matches.
   - Fetch upcoming events and prepare/send reminder emails.

2. Feedback processing is handled separately by fetching feedback responses, parsing them, and sending to the API.

---

## Integration Points

- **Google Forms:** Used to collect skill offers/requests and feedback from the community.
- **Google Calendar:** Manages scheduling and invites for SkillSwap events.
- **Email:** Sends reminders for upcoming SkillSwap sessions.
- **External API:** Receives user feedback post-event for analysis and improvement.

---

## Important Notes

- Event scheduling assumes events take place 3 days from the time of workflow execution.
- Reminder emails are sent out daily for all SkillSwap events occurring in the next 4 days.
- Ensure that all Google APIs and email sending permissions are correctly configured for the workflow to operate smoothly.
- Form IDs must correspond accurately with your Google Forms setup.

---

## Activation

- Currently, this workflow is inactive (`"active": false`).
- Activate the workflow in n8n interface to enable automatic daily run and processing.

---

## Contact

For support or inquiries regarding the workflow or Community SkillSwap platform, please contact:

**Community SkillSwap Team**  
Email: community@skillswap.org