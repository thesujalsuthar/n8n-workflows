# EcoAware AI: Personalized Daily Sustainability Habit Tracker & Motivator

## Overview

EcoAware AI is an automated workflow designed to encourage and support users in building personalized daily sustainability habits. It selects a daily eco-friendly habit tip, sends it via email to the user, collects their progress through a tracking form, provides motivational feedback based on their input, and sends follow-up emails with personalized suggestions.

---

## Workflow Components

### 1. Set Date and User
- **Type:** Function
- **Purpose:** Initializes the workflow by capturing the user ID and the current date (in `YYYY-MM-DD` format).
- **Function Logic:** Extracts `user_id` from incoming JSON and sets the current date.
- **Output:** JSON with `userId` and `date`.

---

### 2. Select Daily Tip
- **Type:** Function
- **Purpose:** Randomly selects a daily sustainability habit from a predefined list and provides a corresponding tip.
- **Habits Included:**
  - Reducing plastic use
  - Using public transport
  - Saving water
  - Recycling regularly
  - Eating plant-based meals
  - Energy saving at home
  - Composting organic waste
- **Function Logic:** Picks one habit randomly, attaches an eco-friendly tip related to that habit.
- **Output:** JSON with `habit`, `tip`, `date`, and `userId`.

---

### 3. Generate Habit Tracking Form URL
- **Type:** HTTP Request (POST)
- **Purpose:** Sends user info and the selected habit to an external form service to generate URLs for habit progress tracking.
- **Parameters Sent:**
  - `userId`
  - `date`
  - `habit`
- **URL:** `https://formservice.example.com/submit` (replace with your actual form URL)
- **Output:** Used internally to build habit tracking progression flow.

---

### 4. Send Daily Tip Email
- **Type:** Send Email
- **Purpose:** Emails the user their daily sustainability tip and a link to track their habit progress.
- **From:** `ecoaware@yourdomain.com`
- **To:** User email extracted dynamically (`{{$json["email"]}}`)
- **Subject:** "Your Daily Sustainability Tip & Habit Tracker"
- **Email Body:**

  ```
  Hello,

  Today's sustainability tip:
  {{ $json["tip"] }}

  Please update your progress for the habit: {{ $json["habit"] }} by submitting your input here:

  [Track your progress](https://formservice.example.com/track?user={{ $json["userId"] }}&date={{ $json["date"] }})

  Keep up the great work!

  EcoAware AI Team
  ```

---

### 5. Habit Input Webhook
- **Type:** Webhook (POST)
- **Purpose:** Receives user-submitted habit progress updates via the tracking form.
- **Webhook URI:** `/habit-input-webhook`
- **Response:** Sends confirmation message `{"message": "Thanks for submitting your habit input!"}` to the client upon submission.

---

### 6. Generate Motivational Feedback
- **Type:** Function
- **Purpose:** Analyzes the user’s submitted progress and mood and generates personalized motivational feedback and suggestions.
- **Input:**
  - `userId`
  - `date`
  - `habit`
  - `progress` (percentage)
  - `mood` (e.g., “motivated”, “tired”, “stressed”)
- **Logic:**
  - If progress ≥ 80%: Praises strong sustainability habits, encourages new goals.
  - If progress ≥ 50%: Encourages improvement and planning.
  - Otherwise: Motivates with focus on small consistent changes.
  - Adds mood-based personalized encouragement or advice.
- **Output:** JSON including `feedback` and `suggestions` alongside input data.

---

### 7. Send Feedback Email
- **Type:** Send Email
- **Purpose:** Sends personalized habit progress feedback and improvement tips back to the user.
- **From:** `ecoaware@yourdomain.com`
- **To:** User email (`{{$json["email"]}}`)
- **Subject:** "Your Sustainability Habit Feedback & Tips"
- **Email Body:**

  ```
  Hello,

  Thank you for updating us on your progress with "{{ $json["habit"] }}" on {{ $json["date"] }}.

  Here's your personalized feedback:
  {{ $json["feedback"] }}

  Suggestions for improvement:
  {{ $json["suggestions"] }}

  Keep pushing forward towards a greener lifestyle!

  EcoAware AI Team
  ```

---

## Workflow Connections

- **Set Date and User** → **Select Daily Tip**
- **Select Daily Tip** → **Generate Habit Tracking Form URL** and **Send Daily Tip Email** (parallel)
- **Habit Input Webhook** → **Generate Motivational Feedback**
- **Generate Motivational Feedback** → **Send Feedback Email**

---

## Usage Notes

- Replace placeholder email addresses (`ecoaware@yourdomain.com`) and API URLs (`https://formservice.example.com`) with your actual service endpoints.
- Ensure the incoming JSON includes relevant user details, especially `user_id` and `email`.
- The form service must be capable of receiving user habit data submissions and invoking the webhook for feedback generation.
- Feel free to expand or modify habit lists and corresponding tips to suit your audience.

---

This workflow enables the delivery of personalized sustainability motivation that adapts based on user interaction and engagement, fostering eco-aware daily habits effectively.