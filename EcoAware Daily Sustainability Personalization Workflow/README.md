# EcoAware Personalized Daily Sustainability Engagement Workflow

## Overview

The **EcoAware Personalized Daily Sustainability Engagement Workflow** is designed to engage users daily with personalized sustainability tips. It leverages user preferences, recent activity, local environmental data, and sustainability news to craft customized messages delivered via email and Telegram. The workflow also tracks user engagement for continuous improvement.

---

## Workflow Nodes Description

### 1. Get User Preferences

- **Type:** HTTP Request
- **Operation:** Retrieve user profile/preferences.
- **Purpose:** Fetch the user’s sustainability preferences including category and location to tailor content.

---

### 2. Get Sustainability News

- **Type:** HTTP Request
- **API Endpoint:** `https://api.sustainabilitynews.example/v1/daily-tips`
- **Authentication:** Bearer token via header
- **Query Parameters:**
  - `category`: User’s preferred sustainability news category, defaults to `'general'` if not specified.
  - `location`: User’s location to optionally tailor the news geographically.
- **Purpose:** Fetch relevant daily sustainability tips based on user preferences.

---

### 3. Get Local Environmental Data

- **Type:** HTTP Request
- **API Endpoint:** `https://api.localenvdata.example/v1/environmental-metrics`
- **Authentication:** API key (`x-api-key` header)
- **Query Parameters:**
  - `location`: User’s location.
- **Purpose:** Retrieve local environmental metrics such as air quality index to inform tips with relevant local context.

---

### 4. Get User Recent Activity

- **Type:** HTTP Request
- **API Endpoint:** `https://api.userhabittracker.example/v1/recent-activities`
- **Authentication:** Bearer token via header
- **Query Parameters:**
  - `userId`: The ID of the user.
  - `since`: Timestamp set to 7 days ago (ISO string) to get recent activities.
- **Purpose:** Understand recent user sustainable habits and behaviors to personalize tips accordingly.

---

### 5. Personalize Tip

- **Type:** Function
- **Input:** Outputs from Sustainability News, Local Environmental Data, User Recent Activity, and User Preferences nodes.
- **Logic:**
  - Analyze recent user activities and match them with sustainability tips most relevant to the user’s behavior.
  - Apply local environmental conditions (e.g., air quality alerts) to adjust or append advice.
  - Default to the first available tip if no activity data exists.
- **Output:** A personalized sustainability tip message tailored to the user.

---

### 6. Send Daily Email Notification

- **Type:** Email Send
- **From:** `no-reply@ecoaware.example`
- **To:** User’s email address
- **Subject:** "Your EcoAware Daily Sustainability Tip"
- **Content:** The personalized tip from the previous node.
- **Purpose:** Deliver the daily sustainability engagement message via email.

---

### 7. Send Daily Message Notification

- **Type:** Telegram Message
- **To:** User’s Telegram `chatId`
- **Content:** Same personalized tip.
- **Authentication:** Telegram API credentials required.
- **Purpose:** Deliver the same daily sustainability tip to users who prefer Telegram messaging.

---

### 8. Track User Engagement

- **Type:** HTTP Request
- **Operation:** Update user engagement record
- **API Parameters:**
  - `userId`: ID of the user.
  - JSON Body Updates: 
    - Update `lastTipSent` timestamp.
    - Increment `engagementScore` by 1.
- **Purpose:** Log the tip delivery and measure continued user engagement for analytics and optimization.

---

## Workflow Execution Flow

1. **Start by fetching user preferences** (`Get User Preferences`).
2. Parallel requests to:
   - Fetch daily sustainability tips based on user category and location (`Get Sustainability News`).
   - Get current local environmental data for the user’s location (`Get Local Environmental Data`).
   - Retrieve the user’s recent activity from the last 7 days (`Get User Recent Activity`).
3. Consolidate data and **generate a personalized tip** (`Personalize Tip`).
4. **Send the personalized tip** via:
   - Email (`Send Daily Email Notification`)
   - Telegram message (`Send Daily Message Notification`)
5. **Update engagement tracking** for the user (`Track User Engagement`).

---

## Credentials Required

- **sustainabilityNewsApi**: API Key or Bearer token to access sustainability news API.
- **localEnvDataApi**: API Key for local environmental data service.
- **userHabitTrackerApi**: API Key or Bearer token for user activity tracking service.
- **telegramApi**: Telegram Bot API credentials for sending messages.

---

## Configuration

- Ensure all API endpoints are accessible and API keys are valid.
- User data must include valid contact details (email, Telegram chatId).
- The email sender address `no-reply@ecoaware.example` can be customized.
- Adjust categories and query parameters as needed based on the external APIs specifications.

---

## Summary

This workflow intelligently integrates various data sources and personalizes daily sustainability tips for users. It enhances user interaction by delivering content through multiple channels and tracks engagement to support future improvements. It’s ideal for eco-conscious applications aiming to promote sustainable behaviors with relevant, timely messaging.