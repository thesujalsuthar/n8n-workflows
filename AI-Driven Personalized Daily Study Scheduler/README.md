# Adaptive AI-Powered Study Planner and Engagement System

## Overview

This workflow is designed to create personalized daily study schedules and motivational plans for users by leveraging AI. It reads user progress and preferences, generates tailored study strategies using GPT-4, and delivers these plans directly to users via Slack.

---

## Workflow Components

### 1. Daily Trigger (`Daily Trigger`)

- **Type:** Interval Trigger
- **Function:** Starts the workflow every day at 7:00 AM.
- **Purpose:** Ensures that a new personalized study and motivational plan is generated for the user every morning.

---

### 2. Fetch User Data (`Fetch User Data`)

- **Type:** Read Binary File
- **Function:** Reads user data from a JSON file located at `users/{{ $json.userId }}.json`.
- **Details:** Each user’s data file contains their current progress and study preferences.
- **Note:** This node expects the user ID to be passed or available in the workflow context.

---

### 3. Extract User Data (`Extract User Data`)

- **Type:** Function Node
- **Function:** Extracts user progress and preferences from the fetched JSON file.
- **Code Logic:**
  ```javascript
  const progress = items[0].json.progress || 0;
  const preferences = items[0].json.preferences || {};
  return [{ json: { progress, preferences } }];
  ```
- **Purpose:** Structures essential user data to be sent to the AI model.

---

### 4. Generate Personalized Plan (`Generate Personalized Plan`)

- **Type:** OpenAI Node
- **Model:** GPT-4
- **Parameters:**
  - **System Message:** "You are an AI assistant that creates personalized study schedules and motivational strategies."
  - **User Message:** Requests a study schedule and motivational plan using dynamic placeholders for user progress and preferences:
    ```
    Create a study schedule and motivational plan for a user. The user's progress is: {{$json["progress"]}}%. Preferences: {{$json["preferences"]}}.
    ```
  - **Temperature:** 0.7
  - **Max Tokens:** 500
- **Purpose:** Generates a customized study and motivation plan based on the user's latest progress and preferences.

---

### 5. Parse Plan Output (`Parse Plan Output`)

- **Type:** Function Node
- **Function:** Extracts the AI-generated plan from the raw OpenAI API response.
- **Code Logic:**
  ```javascript
  return [{ json: { schedule: $items("Generate Personalized Plan")[0].json.choices[0].message.content } }];
  ```
- **Purpose:** Formats the output so it can be sent in a user-friendly message.

---

### 6. Send Plan to User (`Send Plan to User`)

- **Type:** Slack Node
- **Channel:** `user-study-planning`
- **Message:**
  ```
  Here is your personalized study schedule and motivational plan:

  {{$json["schedule"]}}
  ```
- **Purpose:** Delivers the personalized plan directly to the user’s Slack channel for easy access and daily engagement.

---

## Execution Flow

1. **Triggered daily at 7:00 AM (`Daily Trigger`).**
2. **Fetch user-specific data from the filesystem (`Fetch User Data`).**
3. **Extract progress and preferences (`Extract User Data`).**
4. **Send data to GPT-4 for generating a customized study and motivational plan (`Generate Personalized Plan`).**
5. **Parse the raw AI response into a clean schedule message (`Parse Plan Output`).**
6. **Send the final personalized plan to the user on Slack (`Send Plan to User`).**

---

## Requirements

- **User Data:** Stored as JSON files in the `users/` directory, named according to user IDs, including:
  - `progress` (number): The user’s current study progress percentage.
  - `preferences` (object): User’s study preference details.
- **Slack Integration:** A Slack workspace and bot with access to the `user-study-planning` channel.
- **OpenAI API Access:** GPT-4 model enabled for generating personalized plans.
- **n8n Setup:** Configured to run this workflow with file system access and Slack credentials.

---

## Customization Tips

- **Trigger Time:** Adjust the `Daily Trigger` node's time for different daily notification schedules.
- **Slack Channel:** Change the channel in the `Send Plan to User` node to target a different Slack channel or user.
- **User Data Storage:** Modify the `Fetch User Data` node path or its data source depending on your storage method.
- **AI Prompt:** Update the system or user messages in the `Generate Personalized Plan` node to refine the output style or content.

---

## Summary

This fully automated, AI-driven workflow empowers users with personalized study schedules tailored to their progress and preferences, delivered consistently through Slack to maximize daily engagement and educational outcomes.