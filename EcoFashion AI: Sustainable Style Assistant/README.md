# EcoFashion AI: Personalized Sustainable Outfit Generator and Wardrobe Optimizer

## Overview

EcoFashion AI is a workflow designed to provide personalized sustainable outfit suggestions based on the user’s wardrobe, weather data, and style preferences. This workflow fetches real-time weather information, calculates sustainability scores for wardrobe items, filters outfits suitable for the current weather and user preferences, and sends personalized daily outfit recommendations via email and Telegram.

---

## Workflow Components

### 1. Set User Data

- **Purpose:** Initialize the workflow with user-specific data including city, email, Telegram chat ID, wardrobe items, and style preferences.
- **Details:**
  - `city`: User's location for weather data (example: San Francisco).
  - `userEmail`: Email address for sending outfit suggestions.
  - `userChatId`: Telegram chat ID for messaging.
  - `preferences`: User preferences such as preferred colors and styles.
  - `wardrobe`: Array of clothing items including details about type, color, style, and sustainability attributes.

---

### 2. Get Weather Data

- **Type:** HTTP Request
- **Purpose:** Fetch current weather data for the specified city using the OpenWeather API.
- **Parameters:**
  - City query (`q`): From user data or defaults to "New York".
  - Units: Metric system.
  - API Key: From user credentials.
- **Output:** Current weather details including temperature used to tailor outfit suggestions.

---

### 3. Generate Sustainable Outfit

- **Type:** Function
- **Purpose:** Compute personalized outfit recommendations based on wardrobe, preferences, and weather.
- **Logic:**
  - **Sustainability Score Calculation:**
    - +2 points if material includes "organic".
    - +1 point if fair trade certified.
    - +1 point for low water usage.
    - +2 points if using recycled materials.
  - **Weather Suitability:**
    - Jackets recommended if temperature < 10°C.
    - Long sleeves or jackets for 10–19°C.
    - Short sleeves for ≥ 20°C.
    - Accessories always suitable regardless of weather.
  - **Preference Matching:**
    - Filters items by preferred colors.
    - Filters items by preferred styles.
  - **Sorting & Selection:**
    - Items sorted by sustainability score in descending order.
    - Top 5 items selected as outfit suggestions.
- **Output:** Array of recommended outfit items with sustainability scores.

---

### 4. Format Outfit Message

- **Type:** Function
- **Purpose:** Create a user-friendly textual summary of recommended outfits.
- **Message Format:**
  - Lists each outfit item with:
    - Name
    - Type (e.g., jacket, short sleeve)
    - Color
    - Sustainability score
  - Example message:
    ```
    Here are your personalized sustainable outfit suggestions for today:

    1. Organic Cotton T-Shirt (Type: short_sleeve, Color: blue, Sustainability Score: 4)
    2. ...
    ```
- **Output:** Text message ready for sending.

---

### 5. Send Email Notification

- **Type:** Email Send
- **Purpose:** Deliver daily outfit suggestions to the user's email.
- **Parameters:**
  - From: `no-reply@ecofashionai.com`
  - To: User's email address.
  - Subject: "Your Daily Personalized Sustainable Outfit Suggestions"
  - Message Body: Formatted outfit message.
- **Credentials:** Requires SMTP credentials.

---

### 6. Send Telegram Message

- **Type:** Telegram
- **Purpose:** Send the outfit suggestion message directly to the user's Telegram account.
- **Parameters:**
  - Chat ID: User's Telegram chat ID.
  - Message: Formatted outfit message.
- **Credentials:** Requires Telegram API credentials.

---

## Data Flow

1. **User Data Initialization:** User wardrobe and preferences are set with default/example values.
2. **Weather Fetching:** The workflow retrieves the current weather for the user’s city.
3. **Outfit Generation:** Combines wardrobe, weather, and preferences to generate sustainable outfit options.
4. **Message Formatting:** Converts outfit data to a readable message.
5. **Notification Delivery:** Sends the outfit message to the user via email and Telegram.

---

## Prerequisites

- OpenWeather API key (stored in credentials).
- SMTP credentials for sending emails.
- Telegram Bot API token and chat ID setup.
- User wardrobe and preferences data initialized.

---

## Customization

- **Wardrobe Items:** Update with user’s actual clothing items, including sustainability attributes.
- **Preferences:** Customize preferred colors and styles to improve outfit matching.
- **City:** Change location to fetch localized weather data.
- **Notification Channels:** Extend or modify channels (e.g., add SMS or app notifications).

---

## Summary

EcoFashion AI combines environmental sustainability with style personalization, factoring in current weather conditions to provide meaningful, eco-friendly outfit recommendations daily, delivered conveniently via email and Telegram.