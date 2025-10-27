# AI-Powered Personalized Climate Action Planner

This workflow provides users with a personalized climate action plan by integrating real-time local climate data, carbon footprint calculation, tailored lifestyle recommendations, and local eco-friendly resources and events. Users receive updates and progress reports via Telegram.

---

## Workflow Overview

1. **Fetch Local Climate Data**  
   Retrieves current weather data (temperature, conditions) from an external API based on the user's city and country.

2. **Calculate Carbon Footprint**  
   Calculates the user's estimated carbon emissions using their inputs: energy usage, transportation, diet, and waste generation.

3. **Generate Sustainable Lifestyle Recommendations**  
   Produces personalized recommendations to reduce carbon emissions based on the user's footprint and local climate.

4. **Send Personalized Alert**  
   Sends a Telegram message to the user detailing their current local temperature, carbon footprint estimate, and tailored recommendations.

5. **Fetch Local Eco-friendly Resources & Events**  
   Provides a list of eco-friendly resources and upcoming community events relevant to the user's location.

6. **Send Local Resources & Events**  
   Shares the above local resources and events with the user via Telegram.

7. **Track Sustainability Progress**  
   Maintains a history of the user’s carbon footprint over time and calculates percentage reduction since starting.

8. **Send Progress Update**  
   Sends a Telegram progress update highlighting the user's emission changes and encouraging continued sustainability efforts.

---

## Node Details

### 1. Fetch Local Climate Data  
- **Type:** Function  
- **Purpose:** Fetches real-time weather data using OpenWeatherMap API based on user location (`city`, `country`).  
- **Note:** Replace `'YOUR_API_KEY'` with a valid OpenWeatherMap API key.

### 2. Calculate Carbon Footprint  
- **Type:** Function  
- **Purpose:** Calculates total carbon emissions (in tons CO2e) from:  
  - Energy usage (kWh)  
  - Transport usage (km)  
  - Diet type (`vegan`, `vegetarian`, `omnivore`)  
  - Waste produced (kg)  
- Uses predefined emission factors for calculations.

### 3. Generate Sustainable Lifestyle Recommendations  
- **Type:** Function  
- **Purpose:** Creates personalized advice based on emissions and local temperature, such as reducing electricity or adopting a plant-based diet.

### 4. Send Personalized Alert  
- **Type:** Telegram message  
- **Purpose:** Sends climate action summary and recommendations to users on Telegram.  
- **Credentials:** Requires Telegram API credentials.

### 5. Fetch Local Eco-friendly Resources & Events  
- **Type:** Function  
- **Purpose:** Provides simulated local eco-friendly resources (community gardens, recycling centers, markets) and upcoming sustainability events.

### 6. Send Local Resources & Events  
- **Type:** Telegram message  
- **Purpose:** Shares local eco-friendly facilities and event information with users on Telegram.  
- **Credentials:** Requires Telegram API credentials.

### 7. Track Sustainability Progress  
- **Type:** Function  
- **Purpose:** Tracks the user’s emissions history and calculates the percentage reduction compared to their initial emission.

### 8. Send Progress Update  
- **Type:** Telegram message  
- **Purpose:** Sends sustainability progress updates and motivates users to continue their efforts.  
- **Credentials:** Requires Telegram API credentials.

---

## Inputs Expected

- `userName` (string): User's name for personalized messages.
- `city` (string): User's city for local climate data.
- `country` (string): User's country for local climate data.
- Carbon footprint inputs:  
  - `energyUsage` (number): Energy consumption in kWh.  
  - `transportUsage` (number): Distance traveled via transport in km.  
  - `dietType` (string): Dietary choice (“vegan”, “vegetarian”, or “omnivore”).  
  - `wasteProduced` (number): Waste produced in kilograms.  
- `pastEmissions` (array, optional): Historical emissions data for tracking progress.

---

## Outputs and Notifications

- **Personalized climate action plan message:** Includes local temperature, carbon footprint estimate, and recommendations.
- **Local eco-friendly resources and events message:** Lists nearby eco-friendly initiatives and community events.
- **Sustainability progress update message:** Displays reduction percentage and recent carbon footprint data.

---

## Setup Instructions

1. **API Key Configuration**  
   - Replace `'YOUR_API_KEY'` in the **Fetch Local Climate Data** node with your OpenWeatherMap API key.

2. **Telegram Credentials**  
   - Configure your Telegram Bot API credentials in the workflow settings for all Telegram nodes.

3. **Input Data**  
   - Ensure your input data includes user location, carbon footprint metrics, and optionally past emissions for tracking.

4. **Trigger the Workflow**  
   - Run or trigger the workflow with appropriate input to receive personalized climate action notifications.

---

## Notes

- Emission factors used are approximations and can be adjusted for more precise calculations.
- Local resources and events are simulated and should be replaced with dynamic data sources for production.
- Telegram bot must be appropriately set up and authorized with the users to receive messages.

---

Stay committed to personal and community sustainability — every step counts!