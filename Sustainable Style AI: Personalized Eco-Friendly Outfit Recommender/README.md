# AI-Powered Personalized Sustainable Fashion Outfit Recommender

This workflow provides personalized sustainable fashion outfit recommendations based on the user's wardrobe, current weather conditions, and their sustainability preferences. It fetches the user's wardrobe items from a MongoDB database, retrieves the weather forecast using the OpenWeatherMap API, generates an outfit recommendation optimized for both comfort and eco-friendliness, updates the user’s sustainability impact data, and sends a daily email notification with the selected outfit details.

---

## Workflow Overview

The workflow consists of the following main steps/nodes:

### 1. Receive User Request

- **Type:** HTTP Trigger  
- **Description:** Listens for incoming POST requests on the `get-wardrobe` endpoint to start the workflow when a user requests an outfit recommendation.  
- **Authentication:** None

### 2. Fetch User Wardrobe

- **Type:** MongoDB Node (Get Operation)  
- **Description:** Retrieves all clothing items associated with the user from the `wardrobe` collection in the `user_database` MongoDB database. Each item includes metadata such as type (top, bottom, outerwear, shoes), material, color, and an `ecoScore` representing sustainability metrics.  
- **Credentials Required:** MongoDB Credentials

### 3. Fetch Weather Forecast

- **Type:** HTTP Request Node  
- **Description:** Queries the OpenWeatherMap API `onecall` endpoint with latitude and longitude parameters provided by the user request to get daily weather data focusing on the current day. Excludes minute, hourly, and alert data for efficiency.  
- **Parameters:**  
  - Latitude and Longitude from user input  
  - Units set to Metric  
  - API Key is securely pulled from stored credentials  
- **Credentials Required:** OpenWeatherMap API Key

### 4. Generate Outfit Recommendation

- **Type:** Function Node  
- **Description:**  
  - Inputs: User’s wardrobe array, weather forecast for the current day, and a sustainability preference weight (default 0.7).  
  - Filters the wardrobe items based on weather suitability (e.g., discards coats in warm weather).  
  - Scores and ranks items by a weighted sustainability score combining the item's `ecoScore` and the user's preference.  
  - Selects the best items across essential categories: top, bottom, outerwear, and shoes.  
  - Computes an average total eco score for the outfit.  
- **Output:** An object detailing the recommended outfit, total sustainability score, and a brief weather summary.

### 5. Update User Impact

- **Type:** MongoDB Node (Upsert Operation)  
- **Description:** Updates the database to increment the user’s count of sustainable outfits worn and add eco points based on the current outfit’s sustainability score. Uses `userId` as a filter to identify the correct user record. If no user record exists, inserts a new one.  
- **Credentials Required:** MongoDB Credentials

### 6. Send Daily Notification

- **Type:** Email Send Node  
- **Description:** Sends a personalized email to the user's registered contact email containing:  
  - The top, bottom, outerwear, and shoes recommended for the day  
  - A summary of the weather  
  - The sustainability score of the chosen outfit, encouraging ethical and sustainable fashion choices  
- **Credentials Required:** SMTP Credentials for email dispatch

---

## Data Flow

1. The workflow triggers on a user POST request to the `/get-wardrobe` endpoint.
2. It fetches the user's wardrobe items from MongoDB.
3. Then, it retrieves the current day's weather forecast for the user's location.
4. Next, it runs a custom function to recommend a weather-appropriate and sustainable outfit based on user preferences and wardrobe data.
5. It updates the user's sustainability impact metrics in the database.
6. Finally, it emails the outfit recommendation to the user.

---

## Prerequisites

- **MongoDB Database:**  
  - Collections: `wardrobe` (user clothing items), `user_impact` (user impact metrics)  
  - Required fields per clothing item: `type`, `ecoScore`, `name` (for display), and other metadata  
  - User data must include fields `userId`, latitude, longitude, sustainability preference, and contact email.

- **OpenWeatherMap API Key:** To fetch weather forecast data.

- **SMTP Credentials:** For sending email notifications.

- **n8n Environment:** Set up with connected credentials for MongoDB, OpenWeatherMap API, and SMTP server.

---

## Request Payload Example

```json
{
  "userId": "user123",
  "latitude": 37.7749,
  "longitude": -122.4194,
  "sustainabilityPreference": 0.8,
  "userContact": "user@example.com"
}
```

---

## Outfit Recommendation Logic Details

- **Weather Suitability:**  
  - If temperature > 20°C: exclude coats and sweaters.  
  - If 10°C < temperature ≤ 20°C: exclude t-shirts and shorts.  
  - If temperature ≤ 10°C: exclude t-shirts and shorts.

- **Sustainability Scoring:**  
  Weighted score per clothing item =  
  `item.ecoScore * sustainabilityPreference + (1 - sustainabilityPreference) * 0.5`

- The outfit includes one item per type: top, bottom, outerwear, and shoes, with the highest weighted sustainability score per category.

- The overall eco score is the average ecoScore of the selected outfit items.

---

## Notification Email Template

```
Good morning! Your sustainable outfit for today: 
Top: {top item name or none}
Bottom: {bottom item name or none}
Outerwear: {outerwear item name or none}
Shoes: {shoes item name or none}
Weather: {weather description}
Sustainability Score: {score} pts 
Remember to wear ethically and sustainably! 🌿
```

---

## How to Activate

- Import this workflow into your n8n instance.
- Configure the required credentials for MongoDB, OpenWeatherMap API, and SMTP.
- Activate the workflow.
- Trigger the workflow by sending a POST request to `/get-wardrobe` with the necessary user data.

---

This workflow delivers a seamless AI-driven experience promoting sustainable fashion choices tailored to individual wardrobes and real-time weather conditions.