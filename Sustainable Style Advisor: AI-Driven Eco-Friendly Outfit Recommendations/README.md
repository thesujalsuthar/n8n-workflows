# AI-Powered Sustainable Fashion Outfit Recommender

This workflow leverages AI and various APIs to provide users with personalized sustainable fashion outfit recommendations based on their preferences, current weather, and location. It also offers sustainable fashion tips and local eco-friendly store suggestions, then sends all this information via email.

---

## Workflow Overview

### 1. Receive User Preferences
- **Node:** `Receive User Preferences`
- **Type:** HTTP Trigger (POST)
- **Description:** Receives user input including preferences such as location, style, season, and email.
- **Endpoint:** `/user-preferences`
- **Response:** Returns all data collected through the workflow with HTTP status 200.

### 2. Fetch Weather Data
- **Node:** `Fetch Weather Data`
- **Type:** OpenWeather API
- **Inputs:** User’s location (`cityName`).
- **Outputs:** Current weather data using metric units.
- **Purpose:** To consider weather conditions for outfit recommendations.

### 3. Fetch Fashion Items
- **Node:** `Fetch Fashion Items`
- **Type:** HTTP Request to FashionDB API
- **Inputs:** Parameters include category (`outfit`), user’s preferred style, and season.
- **Authorization:** Uses FashionDB API Key in header.
- **Outputs:** List of fashion items matching user preferences.

### 4. Fetch Sustainability Scores
- **Node:** `Fetch Sustainability Scores`
- **Type:** HTTP Request to Sustainability Ratings API
- **Inputs:** IDs of fetched fashion items, sent as a comma-separated list.
- **Authorization:** Uses Sustainability Ratings API Key.
- **Outputs:** Sustainability scores for each item.

### 5. Score & Filter Sustainable Items
- **Node:** `Score & Filter Sustainable Items`
- **Type:** Function
- **Process:**
  - Merges fashion items with their sustainability scores.
  - Filters out items without scores.
  - Filters items with a sustainability score threshold of 70 or higher.
  - Sorts filtered items by descending sustainability score.
  - Selects top 5 sustainable items.
- **Outputs:** Top sustainable outfit recommendations.

### 6. Fetch Local Eco Stores
- **Node:** `Fetch Local Eco Stores`
- **Type:** HTTP Request to Local Eco-Friendly Stores API
- **Inputs:** User's location and search radius (10 km).
- **Authorization:** Uses Local Eco Stores API Key.
- **Outputs:** List of eco-friendly stores near the user.

### 7. Generate Sustainable Fashion Tips
- **Node:** `Generate Sustainable Fashion Tips`
- **Type:** Function
- **Outputs:** A predefined list of sustainable fashion tips:
  - Opt for organic cotton and recycled fabrics.
  - Support brands with transparent supply chains.
  - Wash clothes at lower temperatures to save energy.
  - Donate or upcycle old clothing instead of discarding.

### 8. Send Email Notification
- **Node:** `Send Email Notification`
- **Type:** Gmail Node
- **Inputs:** 
  - User’s email and name.
  - List of recommended sustainable outfits with scores.
  - Sustainable fashion tips.
  - Local eco-friendly stores information.
- **Authorization:** Gmail OAuth2 credentials.
- **Email Content:** A personalized message containing:
  - Sustainable outfit recommendations.
  - Fashion tips.
  - Nearby eco-friendly stores.
- **Subject:** "Your Sustainable Fashion Outfit Recommendations & Tips"

---

## Credentials Required

- **OpenWeather API Key:** For fetching current weather data.
- **FashionDB API Key:** To query fashion items based on user preferences.
- **Sustainability Ratings API Key:** To obtain sustainability scores for fashion items.
- **Local Eco Stores API Key:** To find nearby eco-friendly stores.
- **Gmail OAuth2 Credentials:** To send personalized email notifications.

---

## Input Data Format (POST `/user-preferences`)

```json
{
  "name": "User's Full Name",
  "email": "user@example.com",
  "location": "City Name",
  "style": "preferred_style",    // e.g., casual, formal, sporty
  "season": "current_season"    // e.g., summer, winter, spring, fall
}
```

---

## Summary

This end-to-end automated workflow empowers users to make sustainable fashion choices tailored to their style and current weather while providing actionable tips and local eco-friendly resources—delivered seamlessly via email.