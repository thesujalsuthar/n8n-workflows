# EcoAware AI: Personalized Sustainability Habit Engagement Workflow

## Overview

The **EcoAware AI** workflow is designed to engage users daily with personalized sustainability tips, encouraging sustainable habits and capturing user feedback to continuously optimize content delivery. It integrates with multiple APIs and communication channels, providing a seamless, data-driven approach to fostering eco-friendly behaviors.

---

## Workflow Summary

This workflow runs every day at 8:00 AM and performs the following actions:

1. **Determines User Communication Channels** based on user preferences.
2. **Fetches a Personalized Sustainability Tip** for the user.
3. **Logs the Habit Data** related to the delivered tip.
4. **Delivers the Tip via Multiple Channels** (Email, SMS, Push Notification).
5. **Records Engagement Analytics** for tracking delivery and user interaction.
6. **Waits for User Feedback** on the tip.
7. **Collects and Sends User Feedback** for analysis.
8. **Optimizes Content Delivery** for future tips based on feedback and preferences.

---

## Nodes and Their Functions

### 1. Daily Scheduler
- **Type:** Schedule Trigger
- **Trigger Time:** 8:00 AM daily
- **Purpose:** Initiates the workflow once per day to start the process of delivering tips.

### 2. Determine Channels
- **Type:** Function Node
- **Purpose:** Determines which communication channels to use for the user based on their preferences.
- **Default Channels:** Email
- **Customizable:** Reads user preferences if channels are specified to override the default.

### 3. Get Sustainability Tip
- **Type:** HTTP Request (GET)
- **Endpoint:** `https://api.sustainabilitytips.example.com/tips/daily`
- **Parameters:**
  - `userId`: User identifier
  - `preferences`: JSON string of user preferences
- **Purpose:** Retrieves a personalized sustainability tip for the user.

### 4. Log Habit Data
- **Type:** HTTP Request (POST)
- **Endpoint:** `https://api.userhabits.example.com/habits/log`
- **Payload:**
  - `userId`
  - `habitId` related to the tip or a general ID if unavailable
  - Current timestamp
  - `completed`: false (initial log)
- **Authentication:** API key via header
- **Purpose:** Records the delivery of a habit-related tip in the user's habit log.

### 5. Send Email
- **Type:** Email Send (via Gmail)
- **To:** User's email address
- **Subject:** "Your Daily Sustainability Tip 🌿"
- **Content:** Personalized tip content with friendly intro and call to action.
- **Purpose:** Delivers the sustainability tip via email.

### 6. Send SMS
- **Type:** Twilio SMS Send
- **To:** User's phone number
- **Content:** Short sustainability tip message.
- **Authentication:** Twilio API credentials
- **Purpose:** Delivers the sustainability tip via SMS.

### 7. Send Push Notification
- **Type:** Pushbullet Notification
- **Content:** Notification with the sustainability tip text.
- **Authentication:** Pushbullet API credentials
- **Purpose:** Sends a push notification to the user’s device.

### 8. Record Engagement Analytics
- **Type:** HTTP Request (POST)
- **Endpoint:** `https://api.engagementanalytics.example.com/record`
- **Payload:**
  - `userId`
  - Event type: `"TipDelivered"`
  - Tip identifier
  - Delivery timestamp
  - Channels used for delivery (joined string)
- **Authentication:** API key via header
- **Purpose:** Tracks engagement data for tip delivery analysis.

### 9. Wait for Feedback
- **Type:** Wait Node
- **Duration:** 3600 seconds (1 hour)
- **Purpose:** Pauses the workflow allowing the user time to provide feedback.

### 10. Collect User Feedback
- **Type:** HTTP Request (POST)
- **Endpoint:** `https://api.userfeedback.example.com/feedback/collect`
- **Payload:**
  - `userId`
  - `tipId`
  - User’s feedback rating and comments (if any)
  - Timestamp
- **Authentication:** API key via header
- **Purpose:** Submits user feedback on the sustainability tip for analysis.

### 11. Optimize Content Delivery
- **Type:** HTTP Request (POST)
- **Endpoint:** `https://api.contentoptimize.example.com/optimize`
- **Payload:**
  - `userId`
  - Feedback identifier
  - User preferences
- **Authentication:** API key via header
- **Purpose:** Uses feedback and preferences to optimize future content delivery for personalized engagement.

---

## Credentials Required

| Credential Name               | Usage                            |
|------------------------------|---------------------------------|
| `UserHabitsAPI Key`           | Authenticates habit logging API |
| `EngagementAnalytics API Key`| Authenticates engagement API    |
| `Twilio API`                  | For sending SMS                 |
| `Pushbullet API`              | For sending push notifications  |
| `User Feedback API Key`       | For submitting feedback          |
| `Content Optimization API Key`| For content personalization      |

---

## Summary of Data Flow

1. **Trigger** initiates the delivery process.
2. User preferences guide channel selection.
3. Sustainability tip is fetched based on user profile.
4. Habit data is logged to track engagement.
5. Tip is sent via email, SMS, and push notifications.
6. Engagement events are recorded for analytics.
7. After waiting, user feedback is collected.
8. Feedback is relayed to the content optimization service.
9. Workflow completes until the next scheduled execution.

---

## Usage

- Configure API credentials in n8n to enable secure access.
- Ensure user data including `userId`, `userEmail`, `userPhone`, and `preferences` is supplied to the workflow.
- Customize the default channel list and message formats in the function and send nodes as needed.
- Activate the workflow to automate daily personalized sustainability engagement.

---

This comprehensive workflow facilitates personalized, multi-channel delivery of sustainability tips, habit tracking, feedback collection, and ongoing content refinement all aimed at driving meaningful eco-friendly habits.