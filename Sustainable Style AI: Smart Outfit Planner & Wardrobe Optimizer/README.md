# AI-Driven Sustainable Fashion Outfit Planner and Wardrobe Optimizer

This workflow leverages APIs and smart logic to provide personalized, weather-appropriate, and sustainable outfit suggestions, alongside wardrobe optimization tips. It integrates weather data, your calendar events, and the latest sustainable fashion trends to generate tailored recommendations and emails them directly to the user.

---

## Workflow Overview

### Nodes Description

1. **Get Weather Data**
   - **Type:** HTTP Request
   - **Purpose:** Fetches current weather information for the user’s specified location using the WeatherAPI.
   - **Endpoint:** `https://api.weatherapi.com/v1/current.json`
   - **Input:** Location obtained dynamically.
   - **Authentication:** API Key via header.
  
2. **Get Calendar Events**
   - **Type:** Google Calendar
   - **Purpose:** Retrieves all upcoming Google Calendar events to determine today's occasions.
   - **Operation:** Get all events without filters.
   - **Authentication:** Google OAuth2.

3. **Get Latest Sustainable Fashion Trends**
   - **Type:** HTTP Request
   - **Purpose:** Fetches the most current sustainable fashion trends from the SustainableFashionTrends API.
   - **Endpoint:** `https://api.sustainablefashiontrends.com/v1/trends/current`
   - **Authentication:** API Key via header.

4. **Generate Outfit Suggestions**
   - **Type:** Function
   - **Purpose:** Combines the weather, calendar events, and fashion trend data to:
     - Identify today's occasion based on calendar events.
     - Provide outfit suggestions tailored to the weather and event occasion.
     - Incorporate trending sustainable colors and materials.
   - **Logic Highlights:**
     - Cold weather (<10°C): Warm jacket, scarf, sustainable boots.
     - Moderate weather (10-20°C): Light jacket, eco-friendly sneakers.
     - Warm weather (>20°C): Breathable cotton t-shirt and shorts.
     - Formal occasions: Add a recycled fabric formal blazer.
     - Party occasions: Include eco-friendly party attire.
     - Trends integration: Includes trending sustainable fashion elements.

5. **Provide Sustainability Tips**
   - **Type:** Function
   - **Purpose:** Supplies users with actionable sustainability tips focused on wardrobe optimization and eco-conscious choices.
   - **Examples:**
     - Donate unused clothes.
     - Choose organic or recycled materials.
     - Repair and upcycle garments.
     - Select versatile, mix-and-match pieces.
     - Support ethical brands.

6. **Send Outfit & Tips Email**
   - **Type:** SMTP Email
   - **Purpose:** Sends a personalized email to the user containing:
     - The day's occasion.
     - Current weather summary.
     - Generated outfit suggestions.
     - Sustainable wardrobe tips.
   - **Input:** User’s email address dynamically populated.

---

## Data Flow & Execution Sequence

1. **Get Weather Data, Get Calendar Events, and Get Latest Sustainable Fashion Trends** run in parallel.
2. Outputs of the three APIs are combined in **Generate Outfit Suggestions**, which crafts a personalized outfit plan.
3. **Generate Outfit Suggestions** output feeds into **Provide Sustainability Tips**, which adds eco-friendly wardrobe advice.
4. Both the outfit suggestions and sustainability tips are compiled and sent to the user via **Send Outfit & Tips Email**.

---

## Credentials Required

- **WeatherAPI Key:** For accessing current weather data.
- **Google Calendar OAuth2:** To access the user's calendar events.
- **SustainableFashionTrends API Key:** For fetching the latest sustainable fashion trend data.
- **SMTP Email Account:** To send the personalized outfit and tips email.

---

## Customize Your Experience

- Update the **location** field dynamically to match the user's current or preferred location.
- Ensure calendar events are updated for accurate occasion awareness.
- Modify outfit suggestion rules in the **Generate Outfit Suggestions** function for more personalized or advanced styling logic.
- Add or change sustainability tips in the **Provide Sustainability Tips** function to tailor advice.

---

## Benefits

- **Personalized:** Tailors outfit suggestions based on real-time weather and calendar events.
- **Sustainable:** Encourages eco-friendly fashion choices and lifestyle.
- **Convenient:** Delivers daily outfit plans and tips directly to your inbox.
- **Informed:** Integrates trending sustainable fashion insights to keep style current and responsible.

---

## How to Use

1. Connect and authenticate all required credentials (WeatherAPI, Google Calendar, SustainableFashionTrends API, SMTP).
2. Configure user location and email input fields.
3. Run the workflow daily or as needed to receive updated outfit plans and sustainability advice.
4. Check your email for the daily personalized sustainable fashion recommendations.

---

Harness the power of AI and sustainability to optimize your wardrobe and outfit planning effortlessly!