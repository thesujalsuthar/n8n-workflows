# AI-Powered Personal Productivity and Focus Enhancer

## Overview

This workflow integrates your Google Calendar, Todoist tasks, and Trello boards to leverage AI-driven analysis and provide personalized productivity enhancements. Using OpenAI's GPT-4, it synthesizes your schedule, tasks, and projects to generate a prioritized to-do list, customized productivity tips, scheduled break reminders, and ambient focus music recommendations. Results are sent directly to you via Telegram and logged in MongoDB for tracking and future improvements.

---

## Workflow Nodes Description

### 1. Google Calendar
- **Type:** Google Calendar Node
- **Purpose:** Fetches all events from the user's primary Google Calendar using Service Account authentication.
- **Configuration:**
  - Authentication: Service Account credentials
  - Calendar ID: `primary`
  - Returns: All calendar events

### 2. Todoist
- **Type:** Todoist Node
- **Purpose:** Retrieves all tasks from the user's primary Todoist task list.
- **Configuration:**
  - Operation: `getTasks`
  - Filter: All tasks
  - Task List ID: `primary`

### 3. Trello
- **Type:** Trello Node
- **Purpose:** Collects relevant Trello card data to incorporate project management insights.
- **Configuration:**
  - Used to extract events or cards based on date or other properties (parameters to be customized)
  - Requires Trello API credentials

### 4. AI Analysis and Recommendations
- **Type:** OpenAI Node (GPT-4)
- **Purpose:** Processes inputs from Google Calendar, Todoist tasks, and Trello cards to deliver:
  1. A prioritized to-do list
  2. Personalized productivity tips
  3. Scheduled break reminders
  4. Ambient focus music recommendations
- **Setup:**
  - Model: GPT-4
  - System Prompt: Defines the assistant role focused on productivity enhancement.
  - User Prompt: Includes dynamic data from the previous nodes.

### 5. Send Recommendations
- **Type:** Telegram Node
- **Purpose:** Sends the AI-generated recommendations and insights to the user via Telegram chat.
- **Configuration:**
  - Uses Telegram Bot API credentials
  - Sends message content extracted from AI node output
  - `chatId` placeholder to be replaced with your specific Telegram chat ID

### 6. Log Analysis
- **Type:** MongoDB Node
- **Purpose:** Logs all input data and AI-generated analysis for record-keeping, tracking progress, and future analytics.
- **Configuration:**
  - Inserts a record containing calendar data, task data, Trello data, and the AI result into a MongoDB collection.
  - Requires MongoDB credentials

---

## Connections Flow

- **Google Calendar**, **Todoist**, and **Trello** nodes all feed their data into the **AI Analysis and Recommendations** node simultaneously.
- The **AI Analysis and Recommendations** node then branches out to:
  - **Send Recommendations** node (sending output via Telegram)
  - **Log Analysis** node (storing the data in MongoDB)

---

## Prerequisites

- **Google API Service Account** with access to Google Calendar API.
- **Todoist API Token** for fetching tasks.
- **Trello API Key and Token** for accessing Trello boards/cards.
- **OpenAI API Key** with GPT-4 access.
- **Telegram Bot API Token** and the chat ID where messages will be sent.
- **MongoDB Account** with a ready database and collection to store logs.

---

## Setup Instructions

1. **Configure Credentials:**
   - Add credentials for Google Calendar, Todoist, Trello, OpenAI, Telegram, and MongoDB in your n8n environment.

2. **Replace Placeholders:**
   - Replace `"user-chat-id-placeholder"` in the Telegram node with your actual Telegram chat ID.
   - Ensure the API keys and tokens are correctly referenced in each node.

3. **Customize Trello Node:**
   - Modify the Trello node parameters (dates, boards, cards) to fit your organizational setup.

4. **Activate Workflow:**
   - Turn on the workflow in n8n to begin automatic fetching, analysis, messaging, and logging.

---

## Usage

Run this workflow on a schedule or trigger it manually to receive daily or real-time personalized productivity insights, helping you stay on top of your schedule and tasks with AI assistance.

---

## Future Enhancements

- Add integration with other task/project management tools.
- Enable user feedback loop to refine AI recommendations.
- Support multimedia notifications (audio break reminders, music streaming links).

---

## License

This workflow template is provided as-is without warranty. Customize and extend it according to your needs.