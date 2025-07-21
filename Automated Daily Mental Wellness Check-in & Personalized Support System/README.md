# Automated Mental Wellness Check-in and Resource Recommendation System

## Overview

This workflow automates a daily mental wellness check-in process by sending personalized emails to users, analyzing their responses, and providing tailored mental health resource recommendations. It operates on a scheduled basis and utilizes sentiment analysis to gauge the emotional tone of users' replies.

---

## Workflow Components

### 1. Cron Trigger (`Cron`)

- **Type:** Cron Trigger
- **Schedule:** Every day at 9:00 AM
- **Function:** Initiates the workflow daily by triggering the email check-in process.

---

### 2. Send Check-In Email (`Send Check-In Email`)

- **Type:** Email Send
- **From:** `wellness@yourdomain.com`
- **To:** Dynamic recipient email (`{{$json["email"]}}`)
- **Subject:** `Daily Mental Health Check-In`
- **Body:**

  ```
  Hi {{$json["name"]}},

  We hope you're doing well. Please take a moment to answer this quick mental health questionnaire by replying to this email or clicking on the link below:

  1. How are you feeling today on a scale from 1 (very bad) to 10 (very good)?
  2. Any specific stressors or concerns you want to share?

  Your responses will help us provide personalized support.

  Take care,
  Mental Wellness Team
  ```

- **Credentials:** Requires SMTP credentials for sending email.

---

### 3. Fetch Responses (`Fetch Responses`)

- **Type:** Email Read (IMAP)
- **Operation:** Retrieve all emails
- **Filter:** Subject must match `Re: Daily Mental Health Check-In`
- **Credentials:** IMAP credentials required to access the inbox.
- **Function:** Collects responses from users to their daily check-in emails.

---

### 4. Extract Email Body (`Extract Email Body`)

- **Type:** Set Node
- **Operation:** Extract the text body of each fetched email.
- **Expression:** Outputs the email `text` field for further processing.

---

### 5. Sentiment Analysis (`Sentiment Analysis`)

- **Type:** Text Analysis
- **Operation:** Analyze sentiment of the extracted email body text.
- **Output:** Sentiment score indicating emotional tone (positive, neutral, negative).

---

### 6. Generate Recommendation (`Generate Recommendation`)

- **Type:** Function Node
- **Logic:**

  - Uses sentiment score to classify emotional state.
  - Generates tailored mental wellness recommendations based on the following:
    - **Sentiment score < -0.3:** Suggests seeking professional help or crisis support with specific resource links.
    - **Sentiment score between -0.3 and 0.3:** Offers mindfulness exercises and light physical activities.
    - **Sentiment score > 0.3:** Encourages continuation of positive mental wellness routines.

- **Sample Recommendations:**

  - **Negative Sentiment:**
    ```
    It seems you're going through a tough time. Consider reaching out to a mental health professional or using crisis support services. Here are some resources:
    - National Suicide Prevention Lifeline: 1-800-273-8255
    - BetterHelp for online counseling
    - Local therapist directories
    ```
    
  - **Neutral Sentiment:**
    ```
    Thanks for checking in. You might benefit from mindfulness exercises or light physical activities to improve your mood.
    - Try guided meditation
    - Take a short walk each day
    - Practice gratitude journaling
    ```

  - **Positive Sentiment:**
    ```
    Great! It sounds like you're feeling positive. Keep up the good work with your mental wellness routines.
    - Maintain regular self-care
    - Share your positive habits with others
    - Continue mindfulness practices
    ```

---

### 7. Send Recommendations (`Send Recommendations`)

- **Type:** Email Send
- **From:** `wellness@yourdomain.com`
- **To:** Dynamic recipient email (`{{$json["email"]}}`)
- **Subject:** `Your Personalized Mental Wellness Recommendations`
- **Body:**

  ```
  Hi {{$json["name"]}},

  Based on your recent check-in, here are some personalized recommendations:

  {{$json["recommendation"]}}

  Remember, if you ever need immediate help, don't hesitate to reach out to a professional.

  Take care,
  Mental Wellness Team
  ```

- **Credentials:** Requires SMTP credentials for sending email.

---

## Workflow Execution Flow

1. **Cron Trigger:** Fires daily at 9:00 AM.
2. **Send Check-In Email:** Sends daily mental health questionnaire to users.
3. **Fetch Responses:** Retrieves user replies to the check-in email.
4. **Extract Email Body:** Extracts message content for analysis.
5. **Sentiment Analysis:** Evaluates emotional tone of the response.
6. **Generate Recommendation:** Creates personalized advice based on sentiment.
7. **Send Recommendations:** Sends tailored mental wellness resources back to the user.

---

## Prerequisites

- Valid SMTP account and credentials to send emails.
- IMAP access credentials to read incoming emails.
- Appropriate permissions and API keys (if applicable) for sentiment analysis node.
- User data including at least names and email addresses to dynamically personalize emails.

---

## Notes

- Customize the `fromEmail` address and email templates as needed.
- Ensure secure storage of SMTP and IMAP credentials.
- Optionally, adapt the questionnaire or recommendation logic to better fit your audience.
- Test the workflow end-to-end to confirm correct triggering, response fetching, sentiment analysis, and email sending.

---

## License

This workflow is provided as-is for educational and implementation purposes. Modify and adapt according to your organizational policies and requirements.