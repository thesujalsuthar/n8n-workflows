# AI-Powered Local Volunteer Event Finder and Organizer

## Overview
This workflow is designed to help users find local volunteer events that match their interests and availability, facilitate event sign-ups, send reminder emails before the events, and follow up with thank-you emails post-event. It leverages AI-driven matching logic and automates communication through email.

---

## Workflow Breakdown

### 1. Initialize Input
- **Node Type:** Function
- **Purpose:** Starts the workflow by prompting for user input.
- **Expected Inputs:**
  - `interests`: Array of tags representing the user's volunteering interests (e.g., `["environment", "community"]`).
  - `availability`: Array of availability time slots with `start` and `end` in ISO8601 format (e.g., `[{"start":"2024-07-05T08:00:00Z", "end":"2024-07-05T12:00:00Z"}]`).
  - `email`: User's email address.
  - `name`: User's name.

---

### 2. Find Matching Volunteer Events
- **Node Type:** Function
- **Description:** Matches the user's interests and availability against a mock list of nearby volunteer events.
- **Process:**
  - Contains a predefined set of events with tags and scheduled dates.
  - Filters events based on whether:
    - The event’s tags match any of the user’s interests.
    - The event’s date fits into any of the user's provided availability periods.
- **Output:** List of `matchedEvents` that satisfy both interests and availability.

---

### 3. Output Matched Events
- **Node Type:** Function
- **Description:** Checks if any matching events are found.
- **Behavior:**
  - If no matches are found, returns a message indicating no available events.
  - Otherwise, sends back the list of available matched events.

---

### 4. User Sign-up for Event
- **Node Type:** Function
- **Purpose:** Simulates the user signing up for a specific event.
- **Mechanism:**
  - Receives `eventId` and `user` information.
  - Stores sign-ups in an in-memory global list `global.signups`.
  - Returns confirmation of the sign-up.
- **Note:** This is mock storage; replace with a proper database for production.

---

### 5. Send Reminder Email
- **Node Type:** HTTP Request
- **Purpose:** Sends a reminder email to the user about their upcoming volunteering event.
- **Email Service:**
  - Endpoint: `https://api.emailservice.fake/send` (Replace with real service URL)
  - Method: POST
  - Body Parameters:
    - `to`: User's email.
    - `subject`: "Reminder: Upcoming Volunteer Event"
    - `text`: Personalized message including user's name, event title, and event date.

---

### 6. List All Sign-ups
- **Node Type:** Function
- **Description:** Retrieves all user sign-ups stored in memory.
- **Output:** Returns the current list of sign-ups as JSON.

---

### 7. Generate Follow-Up Email
- **Node Type:** Function
- **Purpose:** Creates a personalized thank-you email after the event has passed.
- **Content:**
  - Subject: "Thank you for volunteering!"
  - Text: Thanks the user for their participation, referencing the event title.
  - Includes recipient's email.

---

### 8. Send Follow-Up Email
- **Node Type:** HTTP Request
- **Purpose:** Sends the generated follow-up email to the volunteer.
- **Service:**
  - Endpoint: `https://api.emailservice.fake/send` (Replace with actual endpoint).
  - Method: POST
  - Body Parameters:
    - `to`: Email address.
    - `subject`: Email subject.
    - `text`: Email body text.

---

## How to Use This Workflow

1. **Start Workflow:** Provide user inputs including their name, email, volunteering interests, and availability.
2. **Find Events:** The workflow searches for volunteer opportunities matching user input.
3. **Display Matching Events:** Users see their matched events or a message if none are available.
4. **Event Sign-Up:** User selects an event to sign up, storing the sign-up.
5. **Send Reminder:** Prior to the event, users receive an automatic reminder email.
6. **Post-Event Follow-Up:** After the event, the system sends a thank-you email to the volunteer.

---

## Important Notes

- **Email Service:** The workflow uses a placeholder email service endpoint (`https://api.emailservice.fake/send`). Replace this with a real email API (e.g., SendGrid, Mailgun) with proper authentication.
- **Data Persistence:** User sign-ups are currently stored in-memory using `global.signups`. For a production environment, replace with persistent storage such as a database.
- **Event Data:** Events are currently hardcoded as mock data. Integrate with a real events database or API for dynamic listings.
- **Time Zones:** Event and availability dates/times use UTC ISO8601 format. Ensure user input accounts for time zones correctly.
- **Scalability:** For scaling, consider adding user authentication, data validation, and robust error handling.

---

## Node Connection Flow

- **Initialize Input** → **Find Matching Volunteer Events** → **Output Matched Events** → **User Sign-up for Event** → **Send Reminder Email**
- Separate branch after events: **Generate Follow-Up Email** → **Send Follow-Up Email**

---

## Summary

This workflow automates the volunteer event discovery and management process, providing personalized matching, sign-ups, and communication to improve volunteer engagement and streamline event coordination with AI-powered logic.