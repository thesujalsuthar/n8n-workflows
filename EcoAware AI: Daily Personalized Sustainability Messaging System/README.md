# EcoAware AI: Daily Personalized Sustainability Messaging Workflow

## Overview

This workflow automates the generation and delivery of daily personalized sustainability tips and motivational messages to users via multiple communication channels, including email, SMS, and push notifications. It leverages user preferences and engagement history to tailor the content, using AI-driven content generation with OpenAI’s GPT-4 model, and tracks engagement to optimize future messaging.

---

## Workflow Components and Steps

### 1. **Daily Trigger**

- **Node Name:** `Daily Trigger`
- **Type:** Cron
- **Schedule:** Runs daily at 08:00 UTC
- **Purpose:** Initiates the workflow each day to send new personalized sustainability messages.

---

### 2. **Fetch User Profiles**

- **Node Name:** `Fetch User Profiles`
- **Type:** Database (getAll operation)
- **Table:** `user_profiles`
- **Purpose:** Retrieves all registered user profiles including contact information and preferences.

---

### 3. **Fetch User Engagement**

- **Node Name:** `Fetch User Engagement`
- **Type:** Database (getAll operation)
- **Table:** `user_engagement`
- **Purpose:** Fetches the latest engagement data to understand previous interactions per user.

---

### 4. **Filter Users With Contact**

- **Node Name:** `Filter Users With Contact`
- **Type:** Function
- **Purpose:** Filters out users who lack any valid contact method (email, phone, or push token) to avoid sending messages to unreachable users.

---

### 5. **Merge Engagement with Users**

- **Node Name:** `Merge Engagement with Users`
- **Type:** Function
- **Purpose:** Combines user profile data with their recent engagement info, enriching user objects with past message delivery and interaction details.

---

### 6. **Prepare User Data**

- **Node Name:** `Prepare User Data`
- **Type:** Function
- **Purpose:** Extracts and prepares user information focusing on contact details and preferences to serve as input for personalized message generation.

---

### 7. **Generate Personalized Tip**

- **Node Name:** `Generate Personalized Tip`
- **Type:** OpenAI (GPT-4 model)
- **Parameters:**
  - Model: GPT-4
  - Temperature: 0.7 (balanced creativity)
  - System Message: Guides the AI to generate daily sustainability tips and motivational messages.
  - User Message: Supplies user preferences to personalize the generated content.
- **Purpose:** Uses GPT-4 to create a daily personalized sustainability tip and motivational message tailored to each user's preferences and past engagement.

---

### 8. **Create Send Messages**

- **Node Name:** `Create Send Messages`
- **Type:** Function
- **Purpose:** Constructs message payloads for each available user contact channel:
  - Email
  - SMS
  - Push notification

Generates proper message structures including recipient, subject (for email), and message body/content.

---

### 9. **Send Messages**

- **Send Email**
  - **Node Name:** `Send Email`
  - **Type:** Email Send (SMTP)
  - **Credentials:** SMTP Credentials
  - **Parameters:** Uses email address, subject, and message body from payload.
  
- **Send SMS**
  - **Node Name:** `Send SMS`
  - **Type:** Twilio (SMS send)
  - **Credentials:** Twilio Account
  - **Parameters:** Uses phone number and message body.

- **Send Push Notification**
  - **Node Name:** `Send Push Notification`
  - **Type:** Push Notification
  - **Credentials:** Push Notification Service
  - **Parameters:** Uses push token, message title, and body.

Each message channel operates in parallel to send the generated personalized tip via the respective platform.

---

### 10. **Track Engagement**

- **Node Name:** `Track Engagement`
- **Type:** Database (upsert operation)
- **Table:** `user_engagement`
- **Update Key:** `userId`
- **Update Fields:**
  - `lastMessageSentAt`: Timestamp of message sent
  - `lastMessageContent`: Content of the message sent
  - `channel`: Delivery channel used (email, sms, push)
- **Purpose:** Records and updates user engagement data after sending messages to facilitate monitoring and optimization of future outreach.

---

### 11. **Analytics - Collect Stats**

- **Node Name:** `Analytics - Collect Stats`
- **Type:** Function
- **Purpose:** Aggregates delivery statuses and user engagement data for analytics and to improve message personalization and delivery effectiveness over time.

---

## Summary of Workflow Data Flow

1. **Trigger:** Daily at 08:00 UTC.
2. **User Data:** Fetch profiles and engagement history.
3. **Filter:** Remove users without valid contact information.
4. **Merge:** Link user data with engagement history.
5. **Prepare Data:** Format for AI input.
6. **Generate Message:** GPT-4 creates personalized tips.
7. **Create Messages:** Format for each send channel.
8. **Send Messages:** Deliver via email, SMS, and push.
9. **Track Engagement:** Log message sending and interaction.
10. **Analytics:** Collect statistics for continuous improvement.

---

## Requirements and Credentials

- **Database Access:** Read/write permissions for `user_profiles` and `user_engagement` tables.
- **OpenAI API:** Access to GPT-4 model with required API keys.
- **Email SMTP Credentials:** For sending emails.
- **Twilio Account:** For sending SMS messages.
- **Push Notification Service Credentials:** For push message delivery.

---

## Notes

- Ensure timezone for the daily trigger is set correctly (`UTC`) or adjusted as needed.
- Personalization relies heavily on accurate and complete user preference data.
- Engagement tracking is key for optimizing frequency, timing, and content.
- Message templates and OpenAI prompts can be refined for better user experience and relevance.

---

End of Documentation.