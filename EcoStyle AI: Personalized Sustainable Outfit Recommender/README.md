# AI-Powered Sustainable Fashion Outfit Recommender

## Overview

This workflow provides personalized, eco-friendly outfit recommendations by combining a user’s wardrobe data, current local weather information, and the latest sustainable fashion trends. Using AI to analyze and merge these data points, the workflow delivers smart outfit suggestions focused on environmentally conscious fashion choices.

---

## Workflow Breakdown

### 1. Get Wardrobe Data
- **Type:** HTTP Request (GET)
- **Description:** Fetches the user’s wardrobe information from an external API (`https://api.example.com/user/wardrobe`).
- **Authentication:** Header-based authentication.
- **Output:** JSON object representing the user's wardrobe items.

### 2. Get Current Weather
- **Type:** HTTP Request (GET)
- **Description:** Retrieves current weather data from the OpenWeather API based on user’s location.
- **API URL:** `https://api.openweathermap.org/data/2.5/weather`
- **Parameters:**
  - `q`: User location dynamically passed from input JSON.
  - `appid`: OpenWeather API key (stored in credentials).
  - `units`: Metric system.
- **Authentication:** Header-based authentication.
- **Output:** JSON object with detailed current weather information.

### 3. Get Sustainable Fashion Trends
- **Type:** HTTP Request (GET)
- **Description:** Calls an API endpoint (`https://api.example.com/sustainable-fashion-trends`) to retrieve up-to-date sustainable fashion trends.
- **Authentication:** Header-based authentication.
- **Output:** JSON object containing current sustainable fashion insights and trends.

### 4. Combine Data
- **Type:** Function
- **Description:** Consolidates outputs from the previous three nodes (wardrobe, weather, trends) into a single JSON object to be used as input for AI processing.

### 5. AI Outfit Recommendation
- **Type:** OpenAI (GPT-4)
- **Description:** Uses an AI language model to generate an eco-friendly outfit recommendation.
- **Prompt Details:**
  - System Role: Expert sustainable fashion assistant.
  - User Input: Passes combined wardrobe data, current weather, and sustainable fashion trends.
- **Parameters:**
  - Model: GPT-4
  - Temperature: 0.7 (to balance creativity and relevance)
  - Max Tokens: 600
- **Output:** AI-generated detailed outfit recommendation focusing on sustainability.

### 6. Format Output
- **Type:** Function
- **Description:** Extracts the AI model’s response and formats it into a clean JSON object with a `recommendation` field for easy consumption.

---

## Credentials

- **OpenWeather API Key:** Stored securely under `openWeather` credentials. Required to authenticate requests to the OpenWeather API.

---

## Usage Notes

- Make sure API endpoints for wardrobe data and sustainable fashion trends are accessible and properly authenticated.
- Input to this workflow requires the user’s location for weather data retrieval.
- The OpenAI GPT-4 API must be configured and authenticated correctly to generate the outfit recommendations.
- The final output is a JSON containing a sustainability-focused outfit recommendation tailored to the user's wardrobe and local weather conditions.

---

## Summary

By leveraging real-time user data, environment conditions, and trend intelligence with the power of AI, this workflow helps users make smarter, greener fashion choices aligned with sustainable living goals.