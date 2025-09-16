# AI-Driven Eco-Friendly Smart Wardrobe Assistant

## Project Overview

The **AI-Driven Eco-Friendly Smart Wardrobe Assistant** is an intelligent assistant designed to suggest sustainable outfit combinations tailored to your current weather conditions, upcoming calendar events, and personal style preferences. It promotes eco-conscious living by evaluating the sustainability of your wardrobe items and delivering daily outfit recommendations along with environmentally friendly tips directly to your email or messaging apps.

---

## Features

### 1. Outfit Suggestion

- **Criteria Considered:**
  - **Current Weather:** Utilizes real-time weather data to ensure outfit suggestions are weather-appropriate.
  - **Upcoming Calendar Events:** Considers event type and formality to recommend suitable outfits.
  - **User Preferences:** Takes into account your style (casual, formal, sporty, etc.), favorite colors, sustainable brands, and material choices.
  - **Sustainability Score:** Evaluates the eco-friendliness of clothing items based on their sustainability ratings.

- **Core Functions:**
  - `suggestOutfitCombinations`: Generates outfit combinations matching the criteria above.
  - `evaluateEcoFriendlyOptions`: Assesses and prioritizes sustainable choices to promote environmentally responsible dressing.

### 2. Daily Recommendations

- **Delivery Methods:**
  - Email
  - Messaging Apps (e.g., WhatsApp, Slack)

- **Content Included:**
  - Recommended Outfit for the day based on integrated criteria.
  - Eco-Conscious Tips to encourage sustainable fashion habits.

- **Schedule:**
  - Recommendations and tips are sent out daily to keep you informed and inspired.

### 3. User Profile Management

- **Preferences:**
  - Style options such as casual, formal, sporty, etc.
  - Favorite colors to personalize outfit suggestions.
  - Sustainable brand preferences.
  - Material preferences focused on eco-friendly fabrics.

- **Wardrobe Inventory:**
  - Item tracking including:
    - `itemId`: Unique identifier for each clothing piece.
    - `type`: Category such as shirt, pants, dress, shoes, etc.
    - `material`: Fabric composition (cotton, polyester, etc.).
    - `brand`: Brand name for brand-specific preferences.
    - `color`: Color of the item.
    - `sustainabilityScore`: Numerical rating indicating the eco-friendliness of the item.

### 4. Integrations

- **Weather API:** Fetches current and forecasted weather data to optimize outfit relevance.
- **Calendar API:** Retrieves upcoming user events to tailor outfit formality and style.
- **Email API:** Sends daily outfit recommendations and eco-conscious tips via email.
- **Messaging API:** Delivers the same daily recommendations through various messaging platforms.

### 5. AI Model

- **Type:** Recommendation system designed to create personalized, sustainable outfit suggestions.

- **Inputs:**
  - Weather Data
  - Calendar Events
  - User Preferences
  - Wardrobe Inventory

- **Outputs:**
  - Outfit Recommendations that fit the user’s needs and sustainability goals.
  - Eco Tips aimed at increasing awareness and encouraging sustainable fashion choices.

---

## Summary

This workflow combines AI-driven recommendations with real-time data and user preferences to help users make smarter, greener wardrobe decisions. By leveraging multiple APIs and maintaining detailed user profiles, the assistant promotes sustainable fashion habits through daily personalized outfit suggestions and insightful eco-conscious advice delivered seamlessly via email or messaging apps.