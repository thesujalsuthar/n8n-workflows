# EcoAware AI: Personalized Sustainability Tips & Habit Motivation

## Overview

EcoAware AI is an automated workflow designed to send daily personalized sustainability tips and motivational messages to users via their preferred communication platforms (Telegram or Email). The workflow leverages real-time environmental data to tailor tips and encourages eco-friendly habits.

---

## Workflow Breakdown

### 1. Daily Trigger

- **Type:** Schedule Trigger
- **Function:** Initiates the workflow once every day at 08:00 AM.
- **Details:** Ensures that users receive their daily sustainability tips at a consistent time each morning.

### 2. Get Users

- **Type:** Function Node
- **Function:** Fetches a list of users with their details.
- **Details:**  
  This node currently uses a hardcoded user list:
  ```js
  const users = [
    { id: 1, name: 'Alice', platform: 'telegram', platformId: '12345678' },
    { id: 2, name: 'Bob', platform: 'email', email: 'bob@example.com' }
  ];
  ```
  Each user object contains:
  - `id`: Unique user ID
  - `name`: User's name
  - `platform`: Preferred communication method ('telegram' or 'email')
  - `platformId` or `email`: Contact details depending on platform

### 3. Fetch Environmental Data

- **Type:** HTTP Request
- **Function:** Retrieves current weather data for San Francisco using OpenWeather API.
- **Details:**
  - API endpoint: `https://api.openweathermap.org/data/2.5/weather`
  - Query parameters:
    - Location: San Francisco (`q=San Francisco`)
    - Units: Metric (`units=metric`)
  - Authentication uses a Bearer token with OpenWeather API key from credentials.

### 4. Generate Personalized Messages

- **Type:** Function Node
- **Function:** Generates tailored sustainability tips and motivational messages for each user based on the fetched weather data.
- **Details:**
  - Analyzes temperature and weather conditions.
  - Applies custom logic to select appropriate tips:
    - Rain: Reminder to use reusable raincoats or umbrellas.
    - Warm (>25 °C): Suggests using fans instead of AC.
    - Cold (<10 °C): Advises to dress warmly to reduce heating.
    - Otherwise: Selects a tip randomly from a predefined list.
  - Randomly picks motivational phrases to encourage eco-friendly habits.
  - Constructs a message combining the tip and motivation.
  - Outputs an object per user with:
    - User identification data
    - Personalized message text

### 5. Split for Each User

- **Type:** SplitInBatches
- **Function:** Processes messages one user at a time.
- **Details:** Ensures that message sending nodes handle individual users separately to improve reliability and error handling.

### 6. Send Telegram Message

- **Type:** Telegram Node
- **Function:** Sends the personalized message via Telegram for users with `platform` set to `telegram`.
- **Parameters:**
  - Uses the user's Telegram chat ID (`platformId`) to send the message.
- **Credentials:** Telegram Bot API credentials are required.
- **Continue on Fail:** Enabled to avoid halting the workflow if sending fails.

### 7. Send Email

- **Type:** EmailSend Node
- **Function:** Sends the personalized message via email for users with `platform` set to `email`.
- **Parameters:**
  - From: `noreply@ecoaware.ai`
  - To: User's email address
  - Subject: "Your Daily EcoAware Sustainability Tip & Motivation"
- **Credentials:** SMTP credentials are needed.
- **Continue on Fail:** Enabled to prevent workflow interruption if email sending fails.

---

## Prerequisites

- **n8n.io instance** to host the workflow.
- **OpenWeather API key** stored as credential `openWeatherApi`.
- **Telegram Bot** credentials stored as credential `telegramApi`.
- **SMTP email credentials** stored as credential `smtp`.

---

## How It Works

1. **Trigger:** The workflow activates at 8 AM daily.
2. **User Retrieval:** It fetches a list of users.
3. **Weather Data:** Obtains current weather info for San Francisco.
4. **Message Generation:** Creates personalized tips + motivation based on weather.
5. **Dispatch:** Sends the message via Telegram or Email according to user preference, handling users one-by-one.

---

## Extending the Workflow

- Enhance `Get Users` from a real database or API.
- Change location dynamically or per user.
- Add more platforms (e.g., SMS).
- Expand tips and motivation content.
- Improve error handling and logging.

---

## Summary

EcoAware AI helps motivate sustainable living by leveraging environmental data to deliver meaningful, personalized messages daily via multiple communication channels.