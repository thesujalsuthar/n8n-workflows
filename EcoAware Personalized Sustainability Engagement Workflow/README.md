# EcoAware Daily Sustainability Engagement

This workflow delivers personalized daily sustainability tips and motivational messages to users based on their preferences and local environmental data. It aims to encourage eco-friendly habits through tailored advice, AI-generated encouragement, and email communication, while tracking community impact over time.

---

## Workflow Overview

The workflow consists of the following main steps:

1. **User Data Initialization**  
   Defines a set of users with their preferences and locations.

2. **Fetch Local Environmental Data**  
   Retrieves current air pollution data from OpenWeather API based on each user's location.

3. **Generate Personalized Tip**  
   Creates customized sustainability tips combining user preferences and current environmental conditions.

4. **AI Motivation Message**  
   Uses OpenAI GPT-4 to generate motivational messages encouraging users to adopt the personalized tips.

5. **Send Email**  
   Sends an email containing the personalized tip and the motivational message to each user.

6. **Track Community Impact**  
   Stores user engagement data (tip and motivation message) daily into a MongoDB collection for analytics.

---

## Nodes Detail

### 1. User Data (Function Node)  
- Creates user profiles with the following fields:  
  - `userId`: Unique identifier  
  - `name`: User's name  
  - `email`: User email address  
  - `preferences`: Array of sustainability interests (e.g., water conservation, recycling)  
  - `location`: User location (city, state)  
  
Example users included:  
- Alice (San Francisco, CA) – preferences: water conservation, recycling  
- Bob (Austin, TX) – preferences: energy saving, plant-based diet

---

### 2. Fetch Local Environmental Data (HTTP Request Node)  
- Queries OpenWeather’s Air Pollution API endpoint.  
- Dynamically sets latitude and longitude based on user location:  
  - San Francisco, CA → lat: 37.7749, lon: -122.4194  
  - Austin, TX → lat: 30.2672, lon: -97.7431  
- API key (`appid`) required via credential configuration (`OpenWeather API`).  
- Retrieves pollutant levels such as PM2.5 used for air quality assessment.

---

### 3. Generate Personalized Tip (Function Node)  
- Inputs: User preferences and local air pollution data (PM2.5).  
- Logic:  
  - For each user preference, adds a relevant sustainability tip.  
  - Adds an air quality-related recommendation based on PM2.5 levels:  
    - PM2.5 > 15: Suggest limiting outdoor activities  
    - PM2.5 ≤ 15: Encourage outdoor activity  
- Outputs an object containing:  
  - `userId`, `email`, `name`, and joint `tip` string.

---

### 4. AI Motivation Message (OpenAI Node)  
- Uses GPT-4 model.  
- Prompt instructs the AI to generate a motivational message to help users adopt the tip as a daily habit.  
- Emphasizes positive impact and easy actionable steps.  
- Temperature set to 0.7 for balanced creativity and clarity.  
- Limits output to 200 tokens.  
- Requires configured OpenAI API credentials.

---

### 5. Send Email (Send Email Node)  
- Sends personalized emails from `no-reply@ecoaware.org` to each user’s email.  
- Email includes:  
  - User's personalized tip  
  - AI-generated motivational message  
  - Encouraging closing remark from "The EcoAware Team"  
- SMTP credentials must be configured in the workflow.

---

### 6. Track Community Impact (MongoDB Node)  
- Upserts daily engagement records into MongoDB collection `community_impact`.  
- Stores:  
  - `userId`  
  - Current `date` (ISO format, YYYY-MM-DD)  
  - The generated `tip`  
  - The AI-generated `motivation` message  
- Credentials for MongoDB access need configuration.

---

## Credentials Required

- **OpenWeather API** – API key credential for air pollution data.  
- **OpenAI API** – API credential for GPT-4 usage.  
- **SMTP Email** – Email server credentials to send messages.  
- **MongoDB Community Impact** – Database credentials to store impact data.

---

## How It Works

1. The workflow starts with the **User Data** node, producing a list of users.  
2. Each user is processed to fetch localized air pollution data via the **Fetch Local Environmental Data** node.  
3. Using preferences and environmental data, the **Generate Personalized Tip** node creates tailored advice.  
4. The tip is sent to OpenAI GPT-4 to generate a motivational message via the **AI Motivation Message** node.  
5. The combined tip and motivation are emailed to the user through the **Send Email** node.  
6. Simultaneously, the interaction is logged in the `community_impact` MongoDB collection by the **Track Community Impact** node.  

This workflow can be scheduled daily to keep users engaged and informed about sustainable living adjusted to their local conditions.

---

## Configuration Steps

1. **Add your OpenWeather API key** to the HTTP Request node credential.  
2. **Configure OpenAI API access** with valid API keys.  
3. **Provide SMTP credentials** to enable email sending.  
4. **Set up MongoDB credentials** for storing community engagement data.  
5. (Optional) Extend the user list or update preferences/locations as needed.

---

## Summary

EcoAware Daily Sustainability Engagement workflow leverages real-time environmental data, user preferences, AI-generated motivation, and direct email communication to promote sustainable habits effectively and measurably in a community-driven manner.