# AI-Powered Smart Urban Garden Pest Detection & Eco-Friendly Treatment Recommendation System

## Overview

This workflow automates the process of identifying pests in urban garden plants using AI image analysis and provides personalized eco-friendly treatment recommendations. It also sends the treatment details via email and schedules a follow-up reminder for the user to apply treatments timely.

---

## Workflow Breakdown

### 1. Start Node

- **Purpose**: Initiates the workflow.
- **Trigger**: Manual or external trigger (based on implementation).

---

### 2. Read Image

- **Node Type**: `readBinaryFile`
- **Function**: Reads the image file from a given path.
- **Input**: `imagePath` (passed dynamically).
- **Output**: The image binary data, which is converted to a base64 string for AI analysis.

---

### 3. AI Pest Identification & Recommendation

- **Node Type**: `openai`
- **Model Used**: `gpt-4o-mini`
- **Authentication**: API key passed securely from credentials (`openaiApi.apiKey`).
- **Functionality**:
  - Sends the base64 encoded image to the AI model with the following instructions:
    - Identify the pest type from the image.
    - Provide a confidence score between 0 and 1.
    - Recommend a list of personalized eco-friendly treatment options suitable for urban gardens.
  - Receives AI-generated JSON with the following keys:
    - `pestName`: Name of the pest detected.
    - `identificationConfidence`: Confidence score (0 to 1).
    - `recommendedTreatments`: Array of treatment description strings.

---

### 4. Send Email with Recommendations

- **Node Type**: `emailSend`
- **Purpose**: Sends an email to the user with the pest identification result and treatment recommendations.
- **Email Details**:
  - From: `noreply@urbangardenai.com`
  - To: User's email (passed dynamically via `userEmail`)
  - Subject: `Your Pest Detection & Eco-Friendly Treatment Recommendations`
  - Body:
    ```
    Hello,

    We have identified the pest in your urban garden plant as: [pestName] 
    with a confidence of [identificationConfidence].

    Recommended treatments:

    - Treatment 1
    - Treatment 2
    ...

    We will also schedule reminders for your treatment follow-ups.

    Thank you for using our Smart Urban Garden Pest Detection System.

    Best regards,
    Urban Garden AI Team
    ```
- The treatments are formatted in a bullet-point list.

---

### 5. Schedule Reminder

- **Node Type**: `googleCalendar`
- **Functionality**:
  - Creates a calendar event as a follow-up reminder exactly 7 days after the current date.
  - Event Title: `Follow-up treatment reminder for pest: [pestName]`
  - Scheduled Date & Time: Current date + 7 days (ISO 8601 format).
  - Attendee: The user’s email (`userEmail`).
- **Credentials**: Requires valid Google Calendar OAuth2 connection (`Google Calendar Account`).

---

## Inputs Required

- **imagePath**: File path of the garden plant pest image to be analyzed.
- **userEmail**: Email address of the user to send recommendations and reminders.
- **OpenAI API Key**: Stored securely in workflow credentials for AI processing.
- **Google Calendar Credentials**: OAuth2 credentials for scheduling reminders.

---

## Outputs

- Email sent to user with pest identification and eco-friendly treatment advice.
- Reminder event created on user's Google Calendar for treatment follow-up.

---

## How to Use

1. Provide an image path and user email to the workflow.
2. Ensure valid OpenAI API key and Google Calendar OAuth credentials are configured.
3. Trigger the workflow.
4. The AI analyzes the pest in the image, sends treatment recommendations via email, and schedules a calendar reminder.

---

## Summary

This workflow leverages AI image recognition combined with expert guidance to empower urban gardeners to identify pests accurately and apply eco-friendly treatments promptly, enhancing sustainable gardening practices.