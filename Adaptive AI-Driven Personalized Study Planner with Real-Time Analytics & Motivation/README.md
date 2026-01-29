# Adaptive AI-Powered Personalized Study Planner with Real-Time Learning Analytics and Motivation Booster

This workflow creates an adaptive, AI-driven personalized study plan tailored to individual users, utilizing real-time learning analytics and providing motivational boosts to enhance their study habits.

---

## Workflow Overview

The workflow automates the process of fetching user data, generating a personalized study plan using AI, analyzing learning progress, adapting the study plan accordingly, and sending motivational messages to keep users engaged.

---

## Detailed Node Descriptions

### 1. Fetch User Profile
- **Type:** HTTP Request (GET)
- **Function:** Retrieves the user’s profile from `https://api.example.com/user-profile`.
- **Data:** Includes learning pace, preferences, goals, and user ID.

### 2. Parse User Profile
- **Type:** Function
- **Function:** Extracts key details from the fetched user profile:
  - `learningPace`
  - `preferences`
  - `goals`

### 3. Generate Personalized Study Plan
- **Type:** OpenAI GPT-4 Chat Completion
- **Function:** Uses GPT-4 with temperature 0.7 to create a tailored daily study schedule based on user pace, preferences, and goals.
- **Details:** The AI incorporates session durations, focus areas, and break times.

### 4. Extract Initial Study Plan
- **Type:** Function
- **Function:** Extracts the generated study plan content from the AI response.

### 5. Fetch Real-Time Learning Analytics
- **Type:** HTTP Request (GET)
- **Function:** Retrieves the user’s current study analytics from `https://api.example.com/learning-analytics?userId={{userId}}`.
- **Purpose:** Provides real-time data to evaluate user’s learning performance.

### 6. Prepare Analytics & Plan Input
- **Type:** Function
- **Function:** Consolidates:
  - `userId`
  - Real-time learning analytics
  - Initial generated study plan  
  into one JSON payload for further AI analysis.

### 7. Analyze Learning Analytics and Adapt Plan
- **Type:** OpenAI GPT-4 Chat Completion
- **Function:** Uses GPT-4 with temperature 0.6 to analyze real-time learning data and the current study plan.
- **Output:** Produces adaptive recommendations to optimize and update the study plan based on performance.

### 8. Save Updated Study Plan
- **Type:** HTTP Request (POST)
- **Function:** Saves the updated study plan back to the system via `https://api.example.com/save-study-plan`.
- **Data Sent:** `userId` and the adapted `studyPlan`.

### 9. Generate Motivational Message
- **Type:** Function
- **Function:** Selects a random motivational message from a predefined list to encourage the user’s study progress.

Motivational messages include:
- "Keep pushing forward! Every bit counts."
- "You are doing great, stay consistent!"
- "Remember your goals - you can achieve them!"
- "Take a short break, then come back stronger."
- "Believe in yourself, progress is progress!"

### 10. Send Motivation Message
- **Type:** Messaging
- **Function:** Sends the motivational message through the user's preferred communication channel (`email` by default) to their preferred contact.

---

## Execution Flow

1. **Fetch User Profile** → 2. **Parse User Profile** → 3. **Generate Personalized Study Plan** → 4. **Extract Initial Study Plan**

2. Parallel branches start:
   - 5. **Fetch Real-Time Learning Analytics**  
   - Then converged in  
   6. **Prepare Analytics & Plan Input**

3. 6. **Prepare Analytics & Plan Input** → 7. **Analyze Learning Analytics and Adapt Plan**

4. After adaptation:
   - 8. **Save Updated Study Plan**
   - 9. **Generate Motivational Message** → 10. **Send Motivation Message**

---

## Prerequisites

- API endpoint access for user profiles, learning analytics, and saving study plans.
- OpenAI API credentials with GPT-4 availability.
- Messaging system configured for sending motivational messages via user preference channels.

---

## Notes

- The workflow is configured to optimize study planning dynamically by integrating AI-generated recommendations and real-time user data.
- Motivational messages are randomized for personalized encouragement.
- Communication channels and contacts are derived from user preferences, defaulting to email.

---

## Summary

This workflow is designed to enhance learning productivity through a smart, adaptive AI study planner enriched by real-time feedback and human-centric motivational support.