# AI-Powered Personal Learning Style Analyzer and Adaptive Study Plan Generator

## Overview

This workflow leverages AI to analyze an individual's learning profile and generate a personalized, adaptive study plan. The plan includes evidence-based learning techniques tailored to the user's preferences, strengths, weaknesses, goals, and available study time. The generated study plan is then converted into calendar events and added to the user's Google Calendar for daily task management.

---

## Workflow Steps

### 1. Receive User Input
- **Node Type:** HTTP Trigger
- **Description:** Accepts a POST request at the endpoint `/submit-learning-profile`.
- **Input Data:** JSON payload containing the user's learning preferences, strengths, weaknesses, goals, and available study time.
- **Response Mode:** Returns the output from the last node in the workflow.

### 2. Parse User Learning Profile
- **Node Type:** Function
- **Description:** Extracts and structures the user input into a standardized learning profile object with the following properties:
  - `preferences`: User's preferred learning methods (e.g., visual, auditory)
  - `strengths`: Areas where the user excels
  - `weaknesses`: Areas where the user needs improvement
  - `goals`: Learning objectives or targets
  - `availableTime`: Time slots or hours available for study

### 3. Generate Adaptive Study Plan (AI)
- **Node Type:** OpenAI GPT-4
- **Description:** Acts as an expert AI educational consultant. It analyzes the structured learning profile to create a detailed, adaptive study plan that includes:
  - Daily study tasks
  - Recommended resources linked to platforms like Khan Academy, Coursera, or edX
  - Tips to optimize retention using evidence-based learning techniques
- **Input:** Learning profile data formatted within system & user messages for AI context.

### 4. Parse AI Response
- **Node Type:** Function
- **Description:** Processes the AI-generated response by:
  - Attempting to parse JSON content from the AI’s markdown response
  - Falling back to raw text if JSON parsing fails
- **Output:** Structured study plan JSON object or raw text containing the study plan details.

### 5. Convert Plan to Calendar Events
- **Node Type:** Function
- **Description:** Converts the structured study plan into Google Calendar events by:
  - Extracting daily plans and tasks
  - Creating event entries for each study task with:
    - Summary
    - Description with relevant resources
    - Scheduled start and end times (defaulting to 9 AM to 10 AM for each task)
- **Output:** Array of calendar event objects in the required format.

### 6. Create Calendar Event
- **Node Type:** Google Calendar
- **Description:** Uses OAuth2 authentication to create events in the user's primary Google Calendar.
- **Operation:** Adds study tasks as events, enabling users to follow their daily study plan conveniently.

### 7. Response Success Message
- **Node Type:** Function
- **Description:** Returns a confirmation message to the user indicating that their adaptive study plan has been generated and added to their Google Calendar.

---

## Input Data Schema

The input JSON payload to `/submit-learning-profile` should follow this format:

```json
{
  "preferences": {
    // e.g., learning styles, formats, environments
  },
  "strengths": [
    // List of user strengths
  ],
  "weaknesses": [
    // List of user weaknesses
  ],
  "goals": [
    // List of learning goals
  ],
  "availableTime": {
    // Time windows or number of hours available per day/week
  }
}
```

---

## Output

- A personalized, adaptive study plan scheduled as Google Calendar events.
- Each calendar event includes:
  - Task Title
  - Description with details and resource links
  - Scheduled time slot for the study session

---

## Prerequisites and Setup

- **n8n Instance:** This workflow is designed to run within n8n.
- **OpenAI API Key:** Required for GPT-4 node to generate the study plan.
- **Google Calendar OAuth2 Credentials:** To enable creating events in the user's Google Calendar.
- **HTTP Endpoint Exposure:** Ensure the `/submit-learning-profile` endpoint is accessible to receive user data.

---

## Usage

1. Submit the user's learning profile via a POST request in JSON format to the `/submit-learning-profile` endpoint.
2. The workflow sequences through parsing, AI analysis, plan generation, and calendar event creation.
3. The user receives a confirmation message once the plan is successfully generated and added to their calendar.
4. The user can then follow their personalized adaptive study schedule directly via Google Calendar.

---

## Notes

- The schedule defaults study tasks to one-hour slots starting at 9:00 AM for simplification; this can be adjusted in the "Convert Plan to Calendar Events" node as needed.
- The AI-generated study plan includes recommended resources linked to popular educational platforms, ensuring access to quality learning materials.
- Error handling is included to manage parsing issues or missing data, with fallbacks to raw content delivery.

---

## Contact & Support

For questions, issues, or customization requests, please refer to the n8n community or your platform administrator.