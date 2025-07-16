# Automated Virtual Event Networking Assistant

This workflow automates the process of registering participants for a virtual event, matching them based on shared interests and professional backgrounds, scheduling networking video calls, and sending email notifications with call details.

---

## Workflow Overview

1. **Register Participant:**  
   Participants register via an HTTP POST request to the `/register-participant` endpoint. The system acknowledges successful registration and returns the participant ID.

2. **Parse Participant Data:**  
   Extracts and normalizes participant data from the incoming request, including ID, name, email, interests, professional background, and time zone.

3. **Save Participant:**  
   Stores the participant data in the `Participants` collection within the `VirtualEventDB` MongoDB database.

4. **Scheduled Matching Trigger:**  
   Runs periodically every 5 minutes (300 seconds) to initiate the participant matching process automatically.

5. **Get All Participants:**  
   Retrieves all registered participants from the database.

6. **Match Participants:**  
   Executes a matching algorithm that pairs participants based on:
   - Shared interests (weighted score)
   - Overlapping professional backgrounds  
   
   Participants are paired if their similarity score meets a defined threshold.

7. **Schedule Video Calls:**  
   For each matched pair, schedules a video call:
   - Time: 3 PM the next day in Participant A’s time zone
   - Generates a video call URL unique to the pair

8. **Save Scheduled Calls:**  
   Saves scheduled call details into the `ScheduledCalls` collection in MongoDB.

9. **Email Participant A and B:**  
   Sends an email to each participant in the pair, informing them about their scheduled networking call with:
   - Date and time of the call  
   - Video call link  
   - A friendly reminder message

---

## Nodes Description

### 1. Register Participant (HTTP Trigger)
- **Method**: POST  
- **Path**: `/register-participant`  
- **Response**: Confirms registration with success status and participant ID.

### 2. Parse Participant Data (Function)
- Normalizes and structures participant input fields:
  - `id` (from various possible fields or randomly generated)
  - `name`
  - `email`
  - `interests` (array)
  - `professionalBackground` (string)
  - `timeZone` (default `UTC`)

### 3. Save Participant (MongoDB)
- Appends participant data into the `Participants` collection.

### 4. Scheduled Matching Trigger (Schedule Trigger)
- Automatically fires every 5 minutes.
- Starts the matching workflow.

### 5. Get All Participants (MongoDB)
- Fetches all participant documents for matching.

### 6. Match Participants (Function)
- Uses a custom matching algorithm:
  - Scores shared interests higher.
  - Matches professional backgrounds via substring comparison.
  - Creates unique matched pairs.
  - Prevents participants from being matched multiple times in one cycle.

### 7. Schedule Video Calls (Function)
- Uses `moment-timezone` to schedule calls at 3 PM next day in participant A’s timezone.
- Creates video call URLs based on participant IDs.

### 8. Save Scheduled Calls (MongoDB)
- Stores scheduled call information in the `ScheduledCalls` collection.

### 9. Email Participant A & Email Participant B (Email Send)
- Sends reminder emails containing:
  - Partner’s email
  - Scheduled date and time (ISO format)
  - Video call link  
- Uses configured SMTP credentials.

---

## Configuration & Credentials

- **MongoDB:**  
  Configure the `MongoDB Virtual Event DB` credentials to connect to your MongoDB instance containing the `VirtualEventDB` database.
  
- **SMTP:**  
  Supply SMTP credentials under `SMTP Email Account` for sending reminder emails to participants.

---

## Usage

- To register a participant, send a POST request to `/register-participant` with JSON containing at least:
  ```json
  {
    "id": "uniqueId",
    "name": "Participant Name",
    "email": "email@example.com",
    "interests": ["interest1", "interest2"],
    "professionalBackground": "Job title or description",
    "timeZone": "America/New_York"
  }
  ```
- The system will automatically handle data storage, scheduled matching, call scheduling, and emailing.

---

## Notes

- The workflow runs matching checks every 5 minutes and schedules new calls accordingly.
- Scheduling assumes calls are set for 3 PM the next day relative to participant A's timezone.
- Video call URLs are placeholders and can be customized per implementation needs.
- Email templates can be customized in the respective email nodes.

---

## License

This project is provided as-is without warranty. Modify as needed for integration with your virtual event platform.