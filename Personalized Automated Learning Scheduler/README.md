# Automated Personal Learning Path Generator

## Overview
This workflow automates the generation of a personalized learning path for a user by integrating data from an education platform, generating recommendations, and organizing the learning schedule within a Google Calendar and note-taking app. It runs daily at 9:00 AM to keep the learning plan updated and actionable.

---

## Workflow Breakdown

### 1. Daily Trigger
- **Type:** Cron
- **Schedule:** Runs every day at 9:00 AM
- **Purpose:** Initiates the workflow at the set time to fetch user data and generate updated learning recommendations.

### 2. Get User Profile
- **Type:** HTTP Request
- **API Endpoint:** `https://api.education-platform.com/v1/user/profile`
- **Authentication:** Bearer Token via header (uses `educationPlatformApi` credentials)
- **Purpose:** Retrieves user's profile information including interests and goals needed for personalized recommendations.

### 3. Get User Progress
- **Type:** HTTP Request
- **API Endpoint:** `https://api.education-platform.com/v1/user/progress`
- **Authentication:** Bearer Token via header (uses `educationPlatformApi` credentials)
- **Purpose:** Fetches the current learning progress data for the user to tailor the learning path accordingly.

### 4. Merge User Data
- **Type:** Merge Node
- **Operation:** Merge by index
- **Purpose:** Combines outputs from both "Get User Profile" and "Get User Progress" nodes to unify profile and progress data streams.

### 5. Combine Profile and Progress
- **Type:** Function
- **Code Summary:** Extracts JSON from both inputs and creates a single object containing `profile` and `progress`
- **Purpose:** Prepares combined user data to be sent as input for generating recommendations.

### 6. Generate Recommendations
- **Type:** HTTP Request (POST)
- **API Endpoint:** `https://api.education-platform.com/v1/recommendations`
- **Headers:**  
  - Authorization: Bearer Token  
  - Content-Type: application/json
- **Body:** JSON payload containing:
  - `interests` from user profile
  - `goals` from user profile
  - `progress` from user progress data
- **Authentication:** Bearer Token (uses `educationPlatformApi` credentials)
- **Purpose:** Sends user data to the recommendations endpoint to receive personalized learning recommendations.

### 7. Split Recommendations
- **Type:** Function
- **Code Summary:** Extracts the recommendations array and splits it into individual items for downstream processing.
- **Purpose:** To handle each recommendation separately for scheduling and note creation.

### 8. Schedule in Calendar
- **Type:** Google Calendar Node
- **Operation:** Create Event in user's primary Google Calendar
- **Authentication:** OAuth2 via `googleCalendarOAuth2Api` credentials
- **Event Details:**  
  - Summary: "Learning: {recommendation title}"  
  - Description: "Part of your personalized learning path generated automatically."  
  - Start/End DateTime: Uses scheduling info from each recommendation or defaults to the current time plus one hour.
- **Purpose:** Automatically schedules each recommended learning activity in the user's calendar.

### 9. Create/Update Notes
- **Type:** HTTP Request (POST)
- **API Endpoint:** `https://api.note-taking-app.com/v1/notes`
- **Headers:**  
  - Authorization: Bearer Token  
  - Content-Type: application/json
- **Body:** JSON containing:  
  - Title for the note: "Learning Path: {recommendation title}"  
  - Content listing the recommendations with their titles and descriptions formatted as bullet points
- **Authentication:** Bearer Token (uses `noteTakingApi` credentials)
- **Purpose:** Creates or updates notes in the user’s note-taking app for easy reference and review of the personalized learning path.

---

## Credentials Required
- **Education Platform API:**  
  Required to authenticate requests to fetch user profile, user progress, and generate recommendations.
- **Google Calendar OAuth2:**  
  Required to create events in the user's Google Calendar.
- **Note Taking API:**  
  Required to create or update notes in the user's note-taking application.

---

## Summary
The "Automated Personal Learning Path Generator" workflow efficiently orchestrates data retrieval, recommendation generation, calendar scheduling, and note-taking to keep users on track with their personal learning goals — all operated automatically every day at 9 AM.