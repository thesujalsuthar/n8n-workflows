# EcoAware: AI-Driven Personalized Sustainability Tips & Habit Motivation

EcoAware is an automated workflow designed to deliver personalized daily sustainability tips and motivational habit advice tailored to individual user preferences and recent behaviors. Leveraging AI through OpenAI's GPT-4, combined with user data stored in Google Sheets, this system sends actionable and encouraging eco-friendly advice via Email, SMS, and Slack every day at 8:00 AM.

---

## Workflow Overview

The workflow operates on a daily schedule and follows these core steps:

1. **Trigger Daily Execution**
2. **Fetch User Basic Information**
3. **Validate User ID**
4. **Retrieve User Behavior Data from Google Sheets**
5. **Parse and Structure User Data**
6. **Generate Personalized Tip and Motivation using GPT-4**
7. **Parse AI Response**
8. **Distribute Tips via Email, SMS, and Slack**

---

## Node Descriptions

### 1. Daily Trigger
- **Type:** Schedule Trigger
- **Description:** Initiates the workflow every day at 8:00 AM.
- **Configuration:** Set to trigger daily at `08:00`.

### 2. User Basic Data
- **Type:** Function
- **Description:** Supplies static or mock user information including:
  - `userId`
  - `email`
  - `phone`
  - `slackChannel`
- **Note:** Replace with dynamic user data as needed.

### 3. Ensure UserID
- **Type:** Function
- **Description:** Validates that a valid `userId` exists; throws an error if absent.

### 4. Get User Behavior Data
- **Type:** Google Sheets
- **Description:** Retrieves up to 100 rows of user behavior data filtered by the provided `userId`.
- **Parameters:**
  - `sheetId`: Replace with your Google Sheet ID.
  - `sheetName`: Set to `"UserBehavior"`.
  - `filter`: Queries rows where `userId` matches.
- **Credentials:** Requires Google Sheets OAuth2 credentials.

### 5. Parse User Data
- **Type:** Function
- **Description:** Extracts user preferences and habits from the Google Sheets data and prepares a user profile JSON object for the AI.

### 6. Generate Sustainability Tip & Motivation
- **Type:** OpenAI (GPT-4)
- **Description:** Sends the user profile data as prompt context to generate a unique, actionable, and motivating sustainability tip and habit advice personalized to the user.
- **Prompt includes:**
  - User profile data (preferences, habits)
  - Instruction to create a JSON response with `tip` and `motivation`.
- **Credentials:** Requires OpenAI API credentials.

### 7. Parse AI Response
- **Type:** Function
- **Description:** Parses the JSON response from the AI to extract `tip` and `motivation`.

### 8. Send Email
- **Type:** Email Send
- **Description:** Sends the personalized sustainability tip and motivation via email.
- **Parameters:**
  - Recipient email address (from user data)
  - Subject: `"Your Daily EcoAware Sustainability Tip & Motivation"`
  - Message body includes the tip and motivation.
- **Credentials:** SMTP credentials required.

### 9. Send SMS
- **Type:** Twilio
- **Description:** Sends the same tip and motivation via SMS.
- **Parameters:**
  - Recipient phone number (from user data)
  - Messaging service SID
- **Credentials:** Twilio API credentials required.

### 10. Post Slack Message
- **Type:** Slack
- **Description:** Posts a formatted message to a Slack channel with the daily tip and motivation.
- **Parameters:**
  - Slack channel (from user data)
- **Credentials:** Slack API credentials required.

---

## Setup Instructions

### Prerequisites

- [n8n](https://n8n.io/) workflow automation platform
- Google Sheets with a `"UserBehavior"` sheet containing user data, including `userId`, preferences, and habits
- OpenAI API key with access to GPT-4
- SMTP credentials for sending emails
- Twilio account with messaging service SID and API credentials for SMS delivery
- Slack workspace and bot token for posting messages

### Configuration Steps

1. **Google Sheets Integration**
   - Replace `"YOUR_SHEET_ID_HERE"` in the `Get User Behavior Data` node with your actual Google Sheet ID.
   - Authorize and set up Google Sheets OAuth2 credentials in n8n.

2. **OpenAI Integration**
   - Add your OpenAI API key under the `OpenAI API` credentials.
   - Ensure GPT-4 access is enabled.

3. **SMTP Setup**
   - Configure SMTP credentials for the `Send Email` node to enable email delivery.

4. **Twilio Setup**
   - Input your Twilio API credentials and messaging service SID in the `Send SMS` node.

5. **Slack Setup**
   - Add Slack API credentials with permission to post messages.
   - Specify the Slack channel in user data (e.g., `#ecoaware-users`).

6. **User Data**
   - Update the `User Basic Data` node or connect to a real user source.
   - Make sure correct user contact details (`email`, `phone`, `slackChannel`) are provided for notifications.

---

## How It Works

- **Every morning at 8:00 AM**, the workflow triggers.
- It loads a user's basic info (mocked or dynamic).
- Validates the presence of a `userId`.
- Fetches the latest behavior data for that user from Google Sheets.
- Creates a structured user profile.
- Uses GPT-4 to generate a customized sustainability tip and motivational advice.
- Parses the AI's JSON response.
- Sends the tip and motivation to the user through:
  - Email
  - SMS
  - Slack channel

Each message encourages sustainable actions, making daily habit-building personalized and engaging.

---

## Customization Tips

- **Multi-User Support:** Extend the workflow to iterate over multiple users by dynamically fetching user data.
- **Enhanced AI Prompts:** Modify the prompt for GPT-4 to include seasonal tips, local environmental conditions, or user progress tracking.
- **Additional Channels:** Add other communication methods like push notifications or app integrations.
- **User Data Source:** Replace the static user data function with database queries or external API calls.

---

## Troubleshooting

- Ensure all API keys and credentials are correctly configured and authorized.
- Verify user contact details are accurate to avoid delivery failures.
- Check Google Sheets permissions for data reading.
- Monitor workflow logs in n8n for errors or failures.
- Validate JSON formatting in AI responses for proper parsing.

---

## License

This workflow and documentation are provided as-is under the MIT License. Modify and extend freely to suit your sustainability engagement needs.

---

## Contact

For questions or support, please reach out via your preferred channel or open an issue in the project repository where this workflow is maintained.