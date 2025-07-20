# AI-Powered Personalized Study Plan Generator and Progress Tracker

This workflow automates the creation of personalized study plans for students and tracks their daily study progress, sending motivational reminders to keep them engaged.

---

## Overview

The workflow uses the OpenAI GPT-4 model to generate a customized, detailed day-wise study schedule based on student inputs such as subjects, exam dates, and study preferences. It follows spaced repetition principles, balances subjects over time, and includes motivational messages. The progress is tracked daily, logged into Google Sheets, and motivational reminders are sent via Telegram.

---

## Workflow Nodes & Description

### 1. **Webhook for Inputs**
- **Type:** Webhook (POST)
- **Function:** Accepts student data submission including:
  - Subjects with exam dates and importance levels
  - Study preferences (daily available hours, preferred study times, study methods)
- Acts as the entry point to trigger the workflow.

### 2. **Generate Study Plan**
- **Type:** OpenAI GPT-4
- **Function:** Takes student input and generates a detailed personalized study plan.
- **Prompt Highlights:**
  - Balance subjects over the exam period
  - Use spaced repetition principles
  - Include motivational messages
- **Output:** Day-wise study plan with time blocks.

### 3. **Initialize Progress**
- **Type:** Function
- **Function:** Parses the generated study plan to initialize a progress tracking object.
- Extracts study tasks by date and prepares a structure to log task completion status.

### 4. **Daily Trigger**
- **Type:** Time Trigger
- **Schedule:** Twice daily at 07:00 and 19:00
- **Function:** Automatically initiates daily progress checks and task retrieval.

### 5. **Get Today's Tasks**
- **Type:** Function
- **Function:** Retrieves the study tasks scheduled for the current day from the initialized progress.
- If no tasks exist, returns a message indicating a day off.
- Simulates task completion for demonstration purposes.

### 6. **Create Motivational Message**
- **Type:** Function
- **Function:** Generates an encouraging message based on task completion status:
  - Congratulates if all tasks are completed
  - Provides motivation and lists pending tasks if not completed

### 7. **Log Progress to Google Sheets**
- **Type:** Google Sheets Append
- **Function:** Logs the date, task, and completion status into a Google Sheets file for persistent tracking and reporting.
- **Sheet Range:** `StudyProgress!A:C`

### 8. **Send Motivational Reminder**
- **Type:** Telegram Message
- **Function:** Sends the motivational message to the student's Telegram chat.
- Uses chat ID provided or defaults to a placeholder.

### 9. **Wait 1 Minute**
- **Type:** Wait Node
- **Function:** Introduces a 1-minute delay to manage timing or rate limits between nodes (optional timing control).

### 10. **Start**
- **Type:** No Operation
- **Function:** Placeholder node signaling the workflow start.

---

## Data Flow Summary

1. Student submits study info via webhook.
2. GPT-4 generates a personalized day-wise study plan.
3. Progress is initialized by parsing the study plan.
4. Daily triggers get today’s tasks twice a day.
5. Motivational messages are created based on task completion.
6. Progress is logged into Google Sheets.
7. Motivational reminders are sent via Telegram.

---

## Required Integrations

- **OpenAI:** For generating the study plan.
- **Google Sheets:** For logging progress.
- **Telegram:** For sending motivational messages.
- **Webhook:** To accept student input.

---

## Setup Instructions

1. **Configure Webhook URL** for students to submit their study data.
2. **Set OpenAI API credentials** and ensure access to GPT-4.
3. **Connect Google Sheets** account and provide the target spreadsheet ID and sheet name.
4. **Set up Telegram bot** and obtain the chat ID to send messages.
5. Customize node parameters if needed (e.g., study preferences, exam dates).
6. Activate the workflow to start processing.

---

## Usage Notes

- The study plan generation adapts dynamically to student inputs.
- Progress tracking updates twice daily based on the defined time triggers.
- Motivational messages encourage continuous engagement and completion.
- Google Sheets provide a historical log for review and insights.

---

This workflow offers a seamless AI-driven assistant to support students in effective and personalized study management leading up to exams.