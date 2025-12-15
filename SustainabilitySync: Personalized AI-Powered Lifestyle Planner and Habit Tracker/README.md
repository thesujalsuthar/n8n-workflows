# Personalized AI-Enhanced Sustainable Lifestyle Planner and Habit Tracker

This workflow integrates multiple data sources and AI-driven functions to provide a personalized sustainable lifestyle planner and habit tracker. It leverages environmental data, user habits, and community impact metrics to calculate a sustainability score, generate personalized goals, create motivational reminders, and engage users through weekly messages and community challenges.

---

## Workflow Overview

### 1. Retrieve User Habits
- **Node:** Retrieve User Habits (MongoDB)
- **Description:** Queries the MongoDB collection `habits` to retrieve the user’s current sustainability-related habits data.

### 2. Fetch Local Environmental Data
- **Node:** Fetch Local Environmental Data (HTTP Request)
- **Description:** Calls the OpenAQ API to get real-time environmental data (e.g., air quality index) nearest to the user. Utilizes an API key passed via header authentication.

### 3. Retrieve Community Impact Metrics
- **Node:** Retrieve Community Impact Metrics (MongoDB)
- **Description:** Fetches community sustainability participation data from the MongoDB collection `communityMetrics`.

### 4. Calculate Sustainability Score
- **Node:** Calculate Sustainability Score (Function)
- **Description:** Combines user habits, local environmental data, and community impact metrics to compute a personalized sustainability score.
- **Logic Highlights:**
  - Assigns points based on:
    - Recycling frequency
    - Public transport usage frequency
    - Energy saving habits
  - Adjusts score based on air quality index (AQI)
  - Adds points for community participation rate

### 5. Generate Personalized Sustainability Goals
- **Node:** Generate Personalized Sustainability Goals (Function)
- **Description:** Generates actionable sustainability goals based on the user's sustainability score.
- **Goal Examples:**
  - Score < 30: Improve recycling, public transport usage, and reduce energy consumption.
  - Score between 30 and 60: Maintain recycling habits and increase community involvement.
  - Score > 60: Mentor others and lead community challenges.

### 6. Store Habit Data
- **Node:** Store Habit Data (MongoDB)
- **Description:** Saves or updates the user’s habit summary data into the `habitTracking` collection in MongoDB for record-keeping and progress tracking.

### 7. Create Reminders and Motivational Tips
- **Node:** Create Reminders and Motivational Tips (Function)
- **Description:** Generates reminders for each sustainability goal and provides a set of motivational tips to encourage ongoing sustainable behavior.

### 8. Provide Community Challenges
- **Node:** Provide Community Challenges (Function)
- **Description:** Offers curated community challenges designed to foster social engagement and amplify sustainability impact.
- **Challenges Include:**
  - Community Cleanup Challenge (7-day event)
  - Bike to Work Week (7-day event)

### 9. Generate Weekly Engagement Message
- **Node:** Generate Weekly Engagement Message (OpenAI)
- **Description:** Uses the OpenAI GPT-4 model to create a customized, motivational weekly message for the user, incorporating their sustainability score, goals, community challenges, and tips.

### 10. Send Message to User
- **Node:** Send Message to User (Slack)
- **Description:** Delivers the AI-generated weekly engagement message to the user through Slack.

---

## Credentials Required

- **MongoDB Account:** Access to MongoDB collections: `habits`, `communityMetrics`, and `habitTracking`.
- **Environment API Key:** API key for OpenAQ or equivalent environmental data provider.
- **OpenAI API Key:** Access to OpenAI GPT-4 API for generation of engagement messages.
- **Slack API:** Slack workspace access and bot authentication for sending direct messages.

---

## Data Flow Summary

1. User habits and community metrics are retrieved from MongoDB.
2. Real-time environmental data is fetched via HTTP request.
3. These inputs feed into the sustainability score calculation function.
4. Personalized goals are generated based on the score.
5. Goals trigger reminders and motivational tips generation.
6. Community challenges are added to the user's plan.
7. All information is used to generate a personalized weekly message through OpenAI.
8. Message is sent to the user via Slack.
9. User habit data is updated/stored in MongoDB.

---

## Usage Notes

- Ensure all credential nodes are configured with valid API keys and account information before executing the workflow.
- Modify habit and challenge data to suit your target audience and geographic area.
- Adjust scoring thresholds and goal logic to reflect evolving sustainability standards and user feedback.
- Integrate additional communication channels if Slack is not the preferred messaging platform.

---

## Summary

This workflow efficiently combines environmental insights, personal data, and AI to support users on their journey toward a more sustainable lifestyle. Through personalized scoring, goal setting, community engagement, and motivational communication, it empowers individuals to track and improve their environmental impact continually.