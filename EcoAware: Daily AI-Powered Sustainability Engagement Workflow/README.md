# EcoAware: AI-Driven Personalized Sustainability Tips & Habit Motivation

EcoAware is an automated workflow designed to deliver personalized sustainability tips and habit motivation messages to users daily via email and SMS. It leverages user preferences to generate relevant eco-friendly advice, promoting greener habits and lifestyles.

---

## Workflow Overview

The workflow runs every day at 8:00 AM and performs the following steps:

1. **Daily Trigger**: Activates the workflow at 8:00 AM daily.
2. **Get Users**: Retrieves a predefined list of users along with their contact information and sustainability preferences.
3. **Generate Tips & Motivation**: For each user, picks random sustainability tips from categories they are interested in and motivational messages for their specific habits.
4. **Send Email**: Sends an email with the personalized sustainability tips and habit motivation to the user's email address.
5. **Send SMS**: Sends a concise version of the tips and motivational messages via SMS to the user's phone number.

---

## Detailed Node Breakdown

### 1. Daily Trigger

- **Node type**: Schedule Trigger
- **Description**: Fires the workflow at 8:00 AM every day.
- **Configuration**:
  - Trigger time set to 8:00 AM (hour: 8, minute: 0).

### 2. Get Users

- **Node type**: Function
- **Description**: Loads the user data, simulating a database of users with their preferences.
- **Users**:
  - **Alice**
    - Email: alice@example.com
    - Phone: +1234567890
    - Interested in: energy, water, waste
    - Habits: reduce-energy-usage, water-saving
  - **Bob**
    - Email: bob@example.com
    - Phone: +1987654321
    - Interested in: transport, plastic
    - Habits: bike-to-work, plastic-reduction

### 3. Generate Tips & Motivation

- **Node type**: Function
- **Description**: Creates personalized tips and motivational habit messages based on each user's preferences.
- **Data sources**:
  - **Tips database** with categories:
    - Energy
    - Water
    - Waste
    - Transport
    - Plastic
  - **Habit motivation messages** keyed by habit codes.
- **Logic**:
  - For each user, randomly selects one tip per category they are interested in.
  - Retrieves motivation messages corresponding to each of their habits.
- **Output**: JSON object containing user's ID, name, email, phone, selected tips, and motivational habit messages.

### 4. Send Email

- **Node type**: Email Send
- **Description**: Sends an email to each user with their personalized sustainability tips and habit motivation.
- **Email Content**:
  - Subject: "Your Daily EcoAware Sustainability Tips & Motivation"
  - Body: Greets the user by name, lists the tips and habit motivations formatted with numbered lists, and closes with a friendly message from the EcoAware team.
- **Email Sender**: ecoaaware@yourdomain.com
- **Email Recipient**: User's email address.

### 5. Send SMS

- **Node type**: Twilio (SMS)
- **Description**: Sends a summarized SMS notification featuring the daily tip and motivational habit messages.
- **Message format**:
  - Greets the user by name.
  - Includes all selected tips separated by semicolons.
  - Lists all habit motivation messages separated by semicolons.
  - Ends with "- EcoAware".
- **SMS Recipient**: User's phone number.
- **Credentials**: Requires configured Twilio account credentials (Twilio API).

---

## Dependencies and Setup

- **Email Sending**: Configure SMTP credentials or email provider compatible with n8n’s Email Send node.
- **SMS Sending**: Configure Twilio API credentials in n8n under the "Twilio Account" credential type.
- **User Data**: Currently stored within the workflow’s function node. For production, integrate with a database or user management system.
- **n8n Platform**: Ensure n8n instance is running with access to the internet for email and SMS services.

---

## Customization Tips

- Modify user data or integrate with external data sources to dynamically fetch user preferences.
- Expand the `tipsDatabase` and `habitMotivation` objects with more sustainability topics and motivational messages.
- Adjust the trigger time in the "Daily Trigger" node based on the target audience’s timezone and habits.
- Customize email and SMS message templates for branding or tone.
- Add additional notification channels, such as push notifications, if needed.

---

## Summary

EcoAware is a simple yet effective n8n automation for fostering eco-conscious behaviors through daily personalized communication. By leveraging AI-driven content selection and multi-channel delivery, it encourages users to adopt sustainable habits easily and consistently.