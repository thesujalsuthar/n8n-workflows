# Study Session Scheduler & Motivational Reminder Workflow

## Overview

This n8n workflow automates a daily study planning and motivation process. It runs every day at 7:00 AM to generate an optimized study schedule based on the user’s previous study data, analyze the study progress, create an AI-driven motivational message, and send it via Telegram.

---

## Workflow Nodes and Functionality

### 1. Daily Scheduler Trigger

- **Type:** Cron Trigger  
- **Schedule:** Every day at 7:00 AM  
- **Purpose:** Starts the workflow automatically each morning.

---

### 2. User Study Data Input

- **Type:** Function Node  
- **Description:** Provides sample user study data, which includes multiple topics with metadata:
  - `topic`: The study subject/topic name.
  - `durationMinutes`: Suggested study duration per topic.
  - `lastStudied`: Last date/time this topic was studied.
  - `performanceScore`: User's performance score (0-100) representing how well the topic was understood.

- **Sample Data Example:**
  ```json
  [
    {
      "topic": "Math - Algebra",
      "durationMinutes": 60,
      "lastStudied": "2024-06-20T12:00:00Z",
      "performanceScore": 70
    },
    ...
  ]
  ```

---

### 3. Intelligent Scheduler

- **Type:** Function Node  
- **Description:** Using the input study data, this node creates an optimal daily study schedule by:

  - Calculating a priority score for each topic based on:
    - The inverse of the performance score (lower scores = higher priority).
    - The days since the topic was last studied.
  - Sorting topics by priority.
  - Selecting up to 4 sessions, each with a maximum of 45 minutes duration.
  - Preparing the study schedule with topics, durations, and priority scores.

- **Output:** An array of scheduled sessions prioritized for effective study.

---

### 4. Today Study Sessions Input

- **Type:** Function Node  
- **Description:** Supplies sample data of completed study sessions for the current day, including:
  - Session `topic`
  - `durationMinutes` of each session
  - `performanceScore` achieved during the session

- **Sample Data Example:**
  ```json
  [
    { "topic": "Math - Algebra", "durationMinutes": 45, "performanceScore": 72 },
    { "topic": "Physics - Thermodynamics", "durationMinutes": 30, "performanceScore": 80 },
    { "topic": "History - WW2", "durationMinutes": 20, "performanceScore": 65 }
  ]
  ```

---

### 5. Progress Analytics

- **Type:** Function Node  
- **Description:** Analyzes the day's study sessions, providing key metrics:
  - Total time spent studying.
  - Number of distinct topics covered.
  - Average performance score.
  - Number of sessions completed.
  - Date of the summary.

- **Output Example:**
  ```json
  {
    "totalTime": 95,
    "topicsCovered": ["Math - Algebra", "Physics - Thermodynamics", "History - WW2"],
    "averagePerformance": 72.33,
    "sessionCount": 3,
    "date": "2024-06-23T..."
  }
  ```

---

### 6. Motivational Message Generator

- **Type:** Function Node  
- **Description:** Generates a motivational message based on the analytics data that encourages continued progress. It uses randomized templates incorporating:
  - Number of topics studied.
  - Total study time.
  - Average performance score.

- **Sample Messages:**
  - "Awesome! You've made progress by covering 3 topics totaling 95 minutes with an average score of 72.3%. Keep pushing forward!"
  - "Great job! Consistency is your best ally. You've studied 3 topics today for 95 minutes. Your average performance is 72.3%. Stay motivated!"

---

### 7. Send Motivational Reminder

- **Type:** Telegram Node  
- **Description:** Sends the generated motivational message to a Telegram chat.
- **Configuration:**
  - Requires a valid Telegram API credential.
  - Sends message to a defined `chatId` (replace `'YOUR_CHAT_ID'` with actual chat ID in the workflow).
  - Sends the `motivationalMessage` text generated from the previous node.

---

## Workflow Connections Flow

1. **Daily Scheduler Trigger** activates at 7:00 AM →  
2. **User Study Data Input** provides user study data →  
3. **Intelligent Scheduler** creates an optimized study schedule →  
4. **Today Study Sessions Input** provides today’s study session data →  
5. **Progress Analytics** computes performance metrics →  
6. **Motivational Message Generator** crafts an encouraging message →  
7. **Send Motivational Reminder** delivers the motivational text via Telegram.

---

## Setup Instructions

1. Import this workflow into your n8n instance.
2. Configure your Telegram credentials in n8n and set the appropriate `chatId` in the "Send Motivational Reminder" node.
3. Update or replace the sample inputs in the "User Study Data Input" and "Today Study Sessions Input" nodes with your actual data source or API integration.
4. Activate the workflow to enable the daily scheduling and reminders.

---

## Notes

- The scheduler currently limits study sessions to 4 per day and caps individual sessions at 45 minutes.
- The motivational messages are randomized from a preset collection but can be customized or extended.
- The workflow assumes basic user data availability; adapt as needed for real data integrations.

---

By implementing this workflow, users receive daily structured study plans and motivational support to enhance learning efficacy and maintain consistency.