# AI-Powered Sustainable Fashion Outfit Recommender

## Overview

This workflow provides personalized, eco-friendly outfit recommendations powered by AI, based on user preferences, current weather, and sustainable fashion brand data. It integrates weather information, sustainable brand ratings, and AI-generated styling advice to help users make fashion choices that are stylish and environmentally conscious.

---

## Workflow Details

### 1. User Input (Form)

- **Parameters:**
  - **Occasion:** Select from Casual, Formal, Work, Party, or Sport.
  - **Location:** Enter a city name or ZIP code for weather data.
  - **Preferred Styles:** Choose one or more styles (Minimalist, Boho, Streetwear, Classic, Athleisure).
  - **Preferred Temperature Range (°C):** Optional temperature input; if left blank, actual weather temperature is used.

- **Purpose:** Captures user data to tailor outfit recommendations.

---

### 2. Get Weather (HTTP Request)

- **API:** OpenWeatherMap
- **Function:** Retrieves current weather data (temperature in °C) for the user’s specified location.
- **Note:** Requires setting your `YOUR_OPENWEATHERMAP_APIKEY`.

---

### 3. Determine Temperature (Function)

- **Logic:** 
  - Checks if the user provided a preferred temperature.
  - If yes, uses the preferred temperature; otherwise, uses actual current temperature from the weather API.
- **Output:** Sets a final temperature value for recommendation.

---

### 4. Fetch Sustainable Brands (HTTP Request)

- **API:** Sustainable Fashion Brands API (`https://sustainable-fashion-api.example.com/brands`)
- **Function:** Retrieves a list of sustainable fashion brands available for recommendations.

---

### 5. Fetch Environmental Impact Ratings (HTTP Request)

- **API:** Environmental Impact Ratings for brands (`https://sustainable-fashion-api.example.com/environmental-impact`)
- **Input:** Brand IDs fetched in the previous step.
- **Function:** Obtains environmental impact ratings for each sustainable brand.

---

### 6. Merge Brand Data (Function)

- **Logic:** 
  - Combines brand information with their environmental impact ratings.
  - Creates a unified dataset where each brand has its impact rating attached.

---

### 7. Build AI Prompt (Function)

- **Data Used:**
  - Sustainable brands with impact ratings ≤ 3 (filtered for better sustainability).
  - User inputs: occasion, temperature, preferred styles.

- **Prompt Constructed:** 
  - Instructs the AI to provide an eco-friendly outfit recommendation based on occasion, temperature, user’s style preferences, and filtered sustainable brands.
  - Requests output as a JSON object detailing outfit items including brand, item type, material, and rationale.
  - Includes current fashion trends in suggestions.

---

### 8. OpenAI GPT-4 (AI Model)

- **Model:** GPT-4
- **Input:** The prompt constructed in the previous step.
- **Function:** Generates the outfit recommendation in JSON format.

---

### 9. Parse AI Response (Function)

- **Logic:** 
  - Extracts the AI-generated message content.
  - Attempts to parse the response as JSON.
  - Returns the parsed outfit recommendation or raw text with an error flag if parsing fails.

---

## Connections Flow

- **User Input** feeds both **Get Weather** and **Determine Temperature** nodes.
- **Get Weather** outputs temperature that feeds into **Determine Temperature**.
- **Fetch Sustainable Brands** triggers **Fetch Environmental Impact Ratings**.
- Those outputs merge in **Merge Brand Data**.
- **Merge Brand Data** and **Determine Temperature** both lead to **Build AI Prompt**.
- **Build AI Prompt** feeds into **OpenAI GPT-4**.
- **OpenAI GPT-4** response is processed by **Parse AI Response** to produce the final recommendation.

---

## Prerequisites

- API key for OpenWeatherMap set in the **Get Weather** node.
- Access credentials (if required) for the Sustainable Fashion Brands API.
- OpenAI API credentials configured to use GPT-4 model.

---

## Outputs

- JSON-formatted sustainable fashion outfit recommendations customized to the user’s occasion, location, style preferences, and temperature.
- Outfits include detailed item descriptions: brand, item type, material, and rationale behind selections.

---

## Usage

1. Input your preferences and location.
2. The system fetches current weather or respects your preferred temperature.
3. Sustainable brands and their impact ratings are fetched and processed.
4. An AI-powered suggestion is generated using GPT-4.
5. Receive a detailed, sustainable outfit recommendation ready for use.

---

This workflow bridges sustainability and fashion with AI technology to promote responsible and stylish dressing choices.