# EcoAware Daily Sustainability Tip Delivery Scheduler

## Overview
This workflow automates the delivery of personalized daily sustainability tips to EcoAware users. Each morning at 8:00 AM, it retrieves user data, customizes a motivational sustainability tip based on user preferences and engagement history using OpenAI's GPT model, and sends the tip via email and Discord message. It also logs the tip sent to each user's engagement history in the database.

---

## Workflow Nodes and Functionality

### 1. Daily Trigger
- **Type:** Schedule Trigger
- **Schedule:** Runs daily at 8:00 AM
- **Purpose:** Initiates the workflow at the specified time every day.

### 2. Get Users
- **Type:** MongoDB Node
- **Operation:** Find documents in the `users` collection
- **Filter:** None (retrieves all users)
- **Credentials:** MongoDB Account
- **Purpose:** Fetches all registered users from the database.

### 3. Prepare User Data
- **Type:** Function Node
- **Functionality:**
  - Extracts user details including:
    - User ID (`_id`)
    - Email address (`email`)
    - Messaging ID (`messagingId`), nullable
    - Preferences (`preferences`), defaults to empty object
    - Engagement history (`engagementHistory`), defaults to empty array
- **Purpose:** Structures user data for further processing.

### 4. Filter Users With Contact
- **Type:** Function Node
- **Functionality:** Filters only users who have either an email address or a Discord messaging ID.
- **Purpose:** Ensures tips are sent only to users with a contact method.

### 5. Generate Sustainability Tip
- **Type:** OpenAI Node (GPT-4o-mini model)
- **Prompt:**
  ``` 
  You are an assistant creating personalized daily sustainability tips based on user preferences and past engagement.

  User Preferences: {{ $json.preferences | JSON.stringify }}

  Engagement History: {{ $json.engagementHistory | JSON.stringify }}

  Craft a concise, motivational sustainability tip that aligns with the user's interests and encourages positive habits. Output only the text of the tip without any extra formatting.
  ```
- **Options:** Caching enabled to optimize API calls
- **Credentials:** OpenAI API Key
- **Purpose:** Generates a custom sustainability tip tailored to each user's profile.

### 6. Send Email
- **Type:** Email Send Node
- **Operation:** Send email
- **From:** no-reply@ecoaware.org
- **To:** User's email address (`{{$json.email}}`)
- **Subject:** "Your Daily EcoAware Sustainability Tip"
- **Body:**
  ```
  Hello,

  Here's your personalized sustainability tip for today:

  {{$json.choices[0].message.content}}

  Keep up the great work in making the planet healthier!

  - EcoAware Team
  ```
- **Credentials:** SMTP Account
- **Purpose:** Delivers the generated sustainability tip via email.

### 7. Send Message
- **Type:** Discord Node
- **Operation:** Send direct message if `messagingId` exists
- **Channel:** `direct` if messagingId present, otherwise empty
- **User ID:** User's `messagingId`
- **Text:** "Daily Sustainability Tip: {{$json.choices[0].message.content}}"
- **Credentials:** Discord Bot API
- **Purpose:** Sends the daily sustainability tip to users via Discord direct message.

### 8. Update Engagement History
- **Type:** MongoDB Node
- **Operation:** Update
- **Collection:** `users`
- **Record ID:** User's `_id`
- **Update Fields:**
  - Pushes a new engagement entry with:
    - Current date and time (`date`)
    - The sustainability tip text (`tip`)
- **Credentials:** MongoDB Account
- **Purpose:** Logs the sent tip into each user's engagement history for tracking and personalization.

---

## Workflow Execution Flow
1. **Daily Trigger** activates at 8:00 AM.
2. Fetch all users from MongoDB (`Get Users`).
3. Structure user data for use (`Prepare User Data`).
4. Filter out users without email or Discord contact (`Filter Users With Contact`).
5. For each user, generate a personalized sustainability tip using OpenAI (`Generate Sustainability Tip`).
6. Send the tip via email (`Send Email`) and Discord (`Send Message`).
7. Update the user’s engagement history in MongoDB (`Update Engagement History`).

---

## Prerequisites & Credentials
- **MongoDB Account:** Access to the `users` collection.
- **OpenAI API Key:** To generate personalized tips.
- **SMTP Account:** To send emails from `no-reply@ecoaware.org`.
- **Discord Bot API:** To send messages to users on Discord.

---

## Summary
This workflow provides EcoAware users with daily, tailored motivation to maintain sustainable habits through automated tips delivered by both email and Discord, leveraging user preferences and past engagement for a personalized experience, while continuously logging interactions for ongoing optimization.