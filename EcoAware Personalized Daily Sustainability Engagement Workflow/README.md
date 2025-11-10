# EcoAware Daily Sustainability Tips Workflow

This n8n workflow automates the process of generating and delivering personalized daily sustainability tips to users, encouraging eco-friendly habits and community engagement.

---

## Workflow Overview

The workflow follows these main steps:

1. **Start**: Initialize with the user ID.
2. **Fetch User Habits**: Retrieve the user's current sustainability habits from MongoDB.
3. **Prepare AI Input**: Consolidate user habits data to prepare for tip generation.
4. **Generate Sustainability Tip**: Use OpenAI GPT-4 to create a personalized daily sustainability tip based on user habits.
5. **Update User Habits**: Save the generated tip and timestamp to MongoDB.
6. **Fetch Community Challenges**: Retrieve active community sustainability challenges.
7. **Prepare Challenges Text**: Format the challenges for email content.
8. **Send Daily Tip Email**: Email the personalized tip to the user.
9. **Send Challenge Invitation Email**: Invite user via email to join community challenges.
10. **Send Push Notification**: Send a reminder notification encouraging habit updates and community participation.

---

## Nodes Description

### 1. Start (Function Node)
- **Purpose:** Initializes the workflow by setting the `userId`.
- **Logic:** Defaults to `"defaultUser"` if no user ID is provided in the input.
- **Output:** JSON object containing `userId`.

### 2. Fetch User Habits (MongoDB Node)
- **Operation:** Query MongoDB `habits` collection.
- **Query:** Finds habits document where `userId` matches.
- **Purpose:** Retrieve user's recent habits and preferences.

### 3. Prepare AI Input (Function Node)
- **Purpose:** Prepares input for AI by packaging user habits.
- **Output:** JSON with `userId` and full `userHabits` data for AI consumption.

### 4. Generate Sustainability Tip (OpenAI Node)
- **Model:** GPT-4
- **System Message:** Defines AI persona as "EcoAware," providing personalized sustainability tips and habit encouragement.
- **User Message:** Instructs AI to generate a daily tip based on user's recent habits (`userHabits`).
- **Output:** Generated personalized sustainability tip.

### 5. Update User Habits (MongoDB Node)
- **Operation:** Upsert document in `habits` collection.
- **Key:** `userId`
- **Updates:** Saves the latest tip content and timestamp (`lastTipDate` = current time).
- **Purpose:** Keeps user habit record updated with the new tip.

### 6. Fetch Community Challenges (MongoDB Node)
- **Operation:** Query active challenges from the `communityChallenges` collection.
- **Query:** `{"active":true}`
- **Limitation:** Limits results to top 3.
- **Purpose:** Retrieve relevant community challenges for engagement.

### 7. Prepare Challenges Text (Function Node)
- **Purpose:** Formats challenge names into a comma-separated string.
- **Output:** JSON object containing `challenges` as formatted text.

### 8. Send Daily Tip Email (Email Node)
- **Recipient:** User's email from workflow data.
- **Subject:** "Your Daily Sustainability Tip 🌱"
- **Content:** Personalized tip and encouragement message.
- **Credential Required:** SMTP credentials.

### 9. Send Challenge Invitation Email (Email Node)
- **Recipient:** User's email.
- **Subject:** "Join Our EcoAware Community Challenges!"
- **Content:** List of active challenges encouraging participation.
- **Credential Required:** SMTP credentials.

### 10. Send Push Notification (Firebase Cloud Messaging Node)
- **Recipient:** User's push notification token.
- **Notification:** Reminder to update habits and participate in community challenges.
- **Credential Required:** Firebase Cloud Messaging API credentials.

---

## Connections Flow

- **Start** → **Fetch User Habits** → **Prepare AI Input** → **Generate Sustainability Tip**
- **Generate Sustainability Tip** branches to:
  - **Update User Habits**
  - **Send Daily Tip Email**
- **Update User Habits** → **Fetch Community Challenges** → **Prepare Challenges Text**
- **Prepare Challenges Text** branches to:
  - **Send Challenge Invitation Email**
  - **Send Push Notification**

---

## Credentials Required

- **MongoDB Account:** Access to `habits` and `communityChallenges` collections.
- **OpenAI API Key:** For GPT-4 interactions.
- **SMTP Account:** For sending emails.
- **Firebase Cloud Messaging Account:** For sending push notifications.

---

## Usage Notes

- Ensure all credentials are configured correctly in n8n before running the workflow.
- MongoDB collections must be structured with appropriate user habits and community challenges data.
- Emails and push notifications rely on valid user contact information (`email` and `pushToken`) stored or retrieved during the workflow.
- The OpenAI prompt uses the user's habits data to generate genuine personalized tips.
- The workflow supports user engagement via both email and notification channels, fostering sustainable behavior and community involvement.

---

## License

This workflow content is provided as-is for implementation and customization within n8n environments.