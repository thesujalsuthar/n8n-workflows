# AI-Driven Community SkillSwap Scheduler and Matcher

## Overview
This workflow is designed to facilitate skill-sharing within a community by scheduling and matching members based on the skills they offer and those they want to learn. It automates event creation, sends notifications, and collects feedback to enhance future sessions.

---

## Workflow Steps

### 1. Fetch Members and Skills
- **Node type:** Database Query
- **Description:** Retrieves community members along with the skills they offer and are interested in learning.
- **Query:**  
  ```sql
  SELECT member_id, member_name, skills_offered, skills_interested FROM community_members
  ```
- **Output:** List of members with their skills data.

### 2. Match Members by Skill
- **Node type:** Function
- **Description:** Matches members for SkillSwap sessions by checking if one member's skills offered align with another's interests and vice versa.
- **Code:** Matches pairs only if the interested skill matches an offered skill mutually between two members.
  
```javascript
function matchMembers(members) {
  const matches = [];
  const visited = new Set();

  members.forEach(member => {
    member.skills_interested.forEach(skill => {
      members.forEach(potentialPartner => {
        if (member.member_id !== potentialPartner.member_id && !visited.has(member.member_id + '-' + potentialPartner.member_id)) {
          if (potentialPartner.skills_offered.includes(skill) &&
              potentialPartner.skills_interested.some(s => member.skills_offered.includes(s))) {
            matches.push({
              member1: member,
              member2: potentialPartner,
              skill: skill
            });
            visited.add(member.member_id + '-' + potentialPartner.member_id);
            visited.add(potentialPartner.member_id + '-' + member.member_id);
          }
        }
      });
    });
  });
  return matches;
}

return matchMembers(items);
```

- **Input:** Output of step 1 (list of members).
- **Output:** List of matched pairs with the skill for SkillSwap.

### 3. Create SkillSwap Events
- **Node type:** Calendar Event Creation
- **Description:** Creates events in the community calendar for each matched pair.
- **Parameters:**  
  - `calendarId`: `"community_skills_calendar"`  
  - `events`: Created per match with:  
    - **Title:** SkillSwap: [Skill] ([Member1] & [Member2])  
    - **Attendees:** Member email addresses  
    - **DateTime:** Next available slot at 6 PM next day (see `getNextAvailableSlot` function)  
    - **Description:** Session overview with names and skill name
- **Input:** Output from step 2 (match list).

### 4. Send Event Notifications
- **Node type:** Messaging / Notification
- **Description:** Sends email or message notifications to all attendees about the scheduled SkillSwap session.
- **Parameters:**  
  - **Recipients:** Event attendees emails  
  - **Subject:** SkillSwap Session Scheduled  
  - **Body:**  
    ```
    Hello! Your SkillSwap session for {{event.title}} is scheduled on {{event.dateTime}}. Please check your calendar.
    ```
- **Input:** Events created in step 3.

### 5. Collect Session Feedback
- **Node type:** Feedback Collection
- **Description:** After the session ends, collects participant feedback based on predefined questions.
- **Trigger:** When event ends (`event.ends`).
- **Questions:**  
  1. How useful was the session?  
  2. What would you like to improve?  
  3. Any additional comments?
- **Input:** Event data from step 4.

### 6. Store Feedback
- **Node type:** Database Insert
- **Description:** Stores collected feedback into the `session_feedback` database table.
- **Parameters:**  
  - **Table:** `session_feedback`  
  - **Data:** Feedback collected from step 5.
- **Input:** Feedback collected from step 5.

---

## Helper Functions

### `getNextAvailableSlot`
- Provides the date and time for scheduling events.
- Returns the next day at 6:00 PM in ISO string format.
```javascript
function getNextAvailableSlot() {
  const now = new Date();
  now.setDate(now.getDate() + 1);
  now.setHours(18, 0, 0, 0);
  return now.toISOString();
}
```

---

## Summary
This workflow streamlines the process of:
- Fetching members' skills and interests,
- Matching members for mutual learning opportunities,
- Creating calendar events for sessions,
- Sending notifications to participants,
- Collecting session feedback for continuous improvement,
- Storing feedback for future analysis.

All these steps improve community engagement and skill development through efficient automation.