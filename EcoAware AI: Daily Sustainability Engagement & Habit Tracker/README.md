# EcoAware AI: Personalized Daily Sustainability Engagement and Habit Motivation System

## Overview

EcoAware AI is an automated workflow designed to engage users daily with personalized sustainability tips, motivational messages, and habit tracking to encourage eco-friendly behavior. The system delivers daily emails and SMS notifications, tracks user progress on sustainability habits via Google Sheets, and allows users to update their habit progress through email commands.

---

## Workflow Components

### 1. Daily Scheduler

- **Type:** Scheduler node
- **Trigger:** Every day at 8:00 AM
- **Purpose:** Initiates the daily tip and motivation generation process.

### 2. Generate Daily Tip

- **Type:** Function node
- **Functionality:**
  - Randomly selects one sustainability habit from a predefined list:
    - Reduce Plastic Use
    - Save Water
    - Energy Saving
    - Composting
    - Bike or Walk Instead of Driving
  - Selects a random tip associated with the chosen habit.
- **Output:** `{ habitId, habitName, tip }`

### 3. Generate Motivation Message

- **Type:** Function node
- **Functionality:** Randomly selects a motivational message from a preset list, e.g., "Every small step makes a big difference!"
- **Output:** `{ motivation }`

### 4. Send Email Notification

- **Type:** Email send node
- **Input:** Receives daily tip and motivational message.
- **Email Content:**
  - Subject: *Your Daily Eco-Friendly Tip*
  - Body:
    ```
    Hello,

    Here is your personalized sustainability tip for today:

    {{tip}}

    Motivation: {{motivation}}

    Keep making a positive impact!

    EcoAware AI Team
    ```
- **Uses:** SMTP credentials.
- **Recipient:** User's email (from input data or previous node output).

### 5. Send SMS Notification

- **Type:** Twilio node (or similar SMS provider)
- **Input:** Receives daily tip and motivational message.
- **SMS Content:**
  ```
  Your daily sustainability tip: {{tip}}. Motivation: {{motivation}}. Keep it green with EcoAware AI!
  ```
- **Uses:** Twilio API credentials.
- **Recipient:** User's phone number (from input data or previous node output).

### 6. Update Habit Progress

- **Type:** Google Sheets node
- **Functionality:** Updates the user’s progress for the habit sent with the daily tip.
- **Data Fields:**
  - habitId
  - habitName
  - date (current date)
  - progress (defaults to 0 if not supplied)
- **Uses:** Google Sheets OAuth credentials.

### 7. Generate Analytics Summary

- **Type:** Function node
- **Functionality:** Aggregates all habit progress data from Google Sheets to generate a summary with total progress per habit.
- **Output:** `{ summary: [{ habit, totalProgress }] }`

### 8. Send Analytics Email

- **Type:** Email send node
- **Functionality:** Sends the user a summary email showing their sustainability progress.
- **Email Content:**
  - Subject: *Your Sustainability Impact Summary*
  - Body lists each habit and the total progress logged.
- **Uses:** SMTP credentials.
- **Recipient:** User’s email.

### 9. User Input

- **Type:** Email read node (IMAP)
- **Functionality:** Reads incoming emails from users with commands to log habit progress.
- **Uses:** IMAP credentials.

### 10. Parse User Habit Command

- **Type:** Function node
- **Functionality:** Parses the email text for commands of the format:
  ```
  progress <habitId> <value>
  ```
  Example:
  ```
  progress reduce_plastic 3
  ```
- **Outputs:** Extracted `habitId` and `progress` value, and validates input format.

### 11. Validate User Input

- **Type:** Function node
- **Functionality:** Checks the parsed inputs for validity. Throws an error on invalid format.

### 12. Log Habit Progress

- **Type:** Google Sheets node
- **Functionality:** Records the user-reported progress for a habit into the Google Sheet.
- **Data Fields:**
  - habitId
  - progress
  - date (current date)
- **Uses:** Google Sheets OAuth credentials.

### 13. User Confirmation Message

- **Type:** Set node
- **Functionality:** Creates a confirmation message to acknowledge progress logging.

### 14. Send Confirmation Email

- **Type:** Email send node
- **Functionality:** Sends an email back to the user confirming their habit progress has been logged.
- **Subject:** *Habit Progress Logged*
- **Body:** Confirmation message with habit ID, progress value, and date.
- **Uses:** SMTP credentials.
- **Recipient:** User's email (from original input).

---

## Connections & Data Flow Summary

- The **Daily Scheduler** triggers the generation of both the daily tip and motivational message in parallel.
- Outputs from the tip and motivation generation nodes merge into both the email and SMS notification nodes for delivery.
- Habit progress for daily tips is updated in Google Sheets automatically with default or existing progress.
- The analytics function periodically compiles total progress data to email a summary overview to users.
- Incoming user emails are processed to parse habit progress commands.
- Validated commands update progress in Google Sheets and trigger a confirmation email back to the user.

---

## Credentials Required

- **SMTP Account:** For sending emails (daily tips, summary, and user confirmations).
- **Twilio Account (or equivalent):** For sending SMS notifications.
- **Google Sheets OAuth2:** For read/write operations on habit progress data.
- **IMAP Account:** For reading user input emails.

---

## Habit List

| Habit ID        | Habit Name                       |
|-----------------|---------------------------------|
| reduce_plastic  | Reduce Plastic Use               |
| save_water      | Save Water                      |
| energy_saving   | Energy Saving                   |
| composting      | Composting                     |
| bike_or_walk    | Bike or Walk Instead of Driving |

---

## Usage Notes

- Users receive daily sustainability tips and motivational quotes at 8 AM.
- Users can log their own habit progress by replying via email with commands formatted as:
  ```
  progress <habitId> <value>
  ```
- Logged progress is stored and aggregated for performance summaries.
- Confirmation emails are sent automatically after progress logging.

---

Thank you for using EcoAware AI to improve your sustainability habits and contribute to a greener future!