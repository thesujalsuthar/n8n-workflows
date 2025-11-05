# AI-Powered Sustainable Style Advisor: Eco-Friendly Outfit Recommendations

## Overview

This workflow provides personalized, eco-friendly outfit recommendations to users based on their preferences, current weather conditions, and the latest sustainable fashion trends. It runs daily and delivers curated outfit suggestions via email to promote stylish and environmentally conscious choices.

---

## Workflow Components

### 1. Daily Trigger
- **Node:** `Daily Trigger`
- **Type:** Cron Trigger
- **Description:** Schedules the workflow to run every day at 7:00 AM, initiating the process of generating and sending outfit recommendations.

### 2. Load User Preferences from Database
- **Node:** `Load User Preferences from DB`
- **Type:** PostgreSQL Query
- **Description:** Retrieves user preferences from a connected PostgreSQL database. These preferences typically include:
  - City (for weather data)
  - Preferred materials (e.g., organic cotton, hemp)
  - Email address (for notifications)
- **Credentials:** PostgreSQL database credentials must be configured.

### 3. Get Current Weather
- **Node:** `Get Current Weather`
- **Type:** HTTP Request
- **Description:** Fetches the current weather for the user's city using the OpenWeatherMap API.
- **API Endpoint:**  
  `https://api.openweathermap.org/data/2.5/weather?q={{city}}&units=metric&appid={{API_KEY}}`
- **Credentials:** API key for OpenWeatherMap must be provided.

### 4. Get Eco-Friendly Fashion Trends
- **Node:** `Get Eco-Friendly Fashion Trends`
- **Type:** HTTP Request
- **Description:** Retrieves the latest eco-friendly fashion trends relevant to the current season from an external EcoFashion API.
- **API Endpoint:**  
  `https://api.ecofashiontrends.io/v1/trends?season={{season}}&apikey={{API_KEY}}`
- **Credentials:** API key for the EcoFashion API must be supplied.

### 5. Generate Outfit Recommendations
- **Node:** `Generate Outfit Recommendations`
- **Type:** Function
- **Description:** Processes user preferences, current weather, and sustainable fashion trends to curate outfit suggestions.
- **Logic:**
  - Determines season based on temperature (>= 20°C = "spring", else "fall")
  - Filters fashion trends according to the season
  - Matches outfit items against user's preferred materials
  - Outputs a list of recommended sustainable outfit items with their eco-friendly scores

### 6. Send Email Notification
- **Node:** `Send Email Notification`
- **Type:** Email Send
- **Description:** Sends an email to the user with their personalized list of sustainable outfit recommendations.
- **Email Includes:**
  - User’s city and current temperature
  - List of outfit items with material and eco-friendly score
  - Encouraging message to stay stylish and sustainable
- **Credentials:** SMTP credentials must be configured for email sending.

### 7. Receive User Preferences (Optional HTTP Endpoint)
- **Node:** `Receive User Preferences`
- **Type:** HTTP Trigger (POST)
- **Description:** Allows external submission of user preferences via an HTTP POST request to the `/user-preferences` path.
- **Response:** Returns an HTTP 200 status on receipt.

---

## Credentials Setup

1. **OpenWeatherMap API**  
   - Required for fetching weather data.  
   - Configure under the node credentials as `openWeatherMapApi`.

2. **EcoFashion API**  
   - Required for accessing sustainable fashion trends.  
   - Configure under the node credentials as `ecoFashionApi`.

3. **PostgreSQL Database**  
   - Stores user preferences data.  
   - Configure under the node credentials as `postgres`.

4. **SMTP Email**  
   - Required to send the email notifications to users.  
   - Configure under the node credentials as `smtp`.

---

## Execution Flow

1. The workflow triggers daily at 7:00 AM.
2. User preferences are loaded from the PostgreSQL database.
3. Parallel HTTP requests fetch the current weather and eco-friendly fashion trends.
4. The function node generates personalized outfit recommendations based on collected data.
5. The final recommendations are emailed to the user.

---

## Customization

- **User Preferences:** Can include additional fields such as style preferences, budget, or allergies.
- **Messaging:** Email templates can be modified to fit branding or tone.
- **Trigger Schedule:** The daily trigger time can be adjusted as needed.

---

## Summary

This AI-powered workflow automates the delivery of sustainable style advice, helping users make environmentally conscious fashion choices by considering real-time environmental data and the latest eco-fashion trends—all personalized and delivered directly via email.