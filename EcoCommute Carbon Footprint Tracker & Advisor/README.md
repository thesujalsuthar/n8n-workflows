# EcoFriendly Daily Commute Carbon Footprint Tracker and Optimization Advisor

## Overview

This workflow calculates the daily carbon footprint of a user's commute based on their origin and destination. It gathers routing and transit data using Google Maps and a Public Transit API, evaluates the estimated CO2 emissions, incorporates environmental data such as air quality, and sends personalized recommendations to help reduce carbon footprint.

---

## Workflow Components

### 1. Start - User Input

- **Type:** Function
- **Purpose:** Initializes the workflow with user-provided or default origin and destination coordinates.
- **Details:**  
  - Defaults to Manhattan coordinates if none are provided.
  - Outputs JSON containing `origin` and `destination` latitude/longitude strings.

---

### 2. Google Maps Directions

- **Type:** HTTP Request
- **Purpose:** Fetches route directions from Google Maps Directions API.
- **Authentication:** API key via headerAuth credentials.
- **Input Parameters:**  
  - `origin`: Latitude and longitude string from user input.  
  - `destination`: Latitude and longitude string from user input.  
  - `mode`: Set to `"transit"` for public transport options.  
  - `key`: Google Maps API key credential.
- **Response:** JSON containing route and step details including mode of travel, distance, etc.

---

### 3. Public Transit API

- **Type:** HTTP Request
- **Purpose:** Retrieves transit routes from a public transit provider API.
- **Authentication:** HTTP Basic Auth.
- **Input Parameters:**  
  - `origin` and `destination` from user input.
- **Response:** JSON with available public transit routes data.

---

### 4. Environmental Data API

- **Type:** HTTP Request
- **Purpose:** Retrieves environmental data such as air quality index (AQI) near the origin point.
- **Authentication:** API Key via headerAuth.
- **Query Parameters:**  
  - `lat`: Latitude extracted from origin coordinates.  
  - `lon`: Longitude extracted from origin coordinates.
- **Response:** JSON containing air quality and other environmental data.

---

### 5. Calculate Footprint and Advise

- **Type:** Function
- **Purpose:**  
  - Processes route and transit steps from Google Maps and Public Transit API data.  
  - Calculates total trip distance in kilometers.  
  - Estimates carbon emissions based on travel modes with emission factors:  
    - Car: 192 g CO2/km  
    - Bus: 80 g CO2/km  
    - Train: 60 g CO2/km  
    - Walking/Bicycle: 0 g CO2/km  
  - Generates personalized recommendations to reduce carbon footprint.  
  - Examples:
    - Suggest using more public transit or biking.  
    - Recommend walking/biking for short distances (<3km).

- **Input:** Data from Google Maps Directions and Public Transit API.
- **Output:** JSON with total distance, CO2 footprint (grams and kg), and recommendations.

---

### 6. Merge Environmental Insights

- **Type:** Function
- **Purpose:**  
  - Enhances commute data by adding air quality index from Environmental Data API.  
  - Adds recommendations related to environmental conditions (e.g., poor air quality warnings).
- **Input:** Data from Environmental Data API and Calculate Footprint and Advise node.
- **Output:** Extended commute data with environmental insights and updated recommendations.

---

### 7. Send Email Summary

- **Type:** Email Send
- **Purpose:** Sends a summary email to the user including:  
  - Commute origin and destination.  
  - Total distance traveled.  
  - Estimated carbon footprint in kilograms.  
  - Personalized recommendations to reduce emissions.
- **Fields:**  
  - `toEmail`: User email or default (`user@example.com`).  
  - `subject`: "Your Daily Commute Carbon Footprint Summary".  
  - `text`: Summary with dynamic fields based on workflow outputs.
- **Credentials:** Requires SMTP credential configured as `your_smtp_credential`.

---

## Data Flow & Connections

1. **User Input** triggers parallel requests to:  
   - Google Maps Directions  
   - Public Transit API  
   - Environmental Data API

2. **Google Maps Directions** and **Public Transit API** pass data to **Calculate Footprint and Advise**.

3. **Calculate Footprint and Advise** output merges with **Environmental Data API** results in **Merge Environmental Insights**.

4. **Merge Environmental Insights** sends final data to **Send Email Summary** node.

---

## Setup & Configuration

- **Google Maps API Key:** Store in n8n credentials under `googleMapsApi`.
- **Public Transit API:** Configure HTTP Basic Authentication with proper credentials.
- **Environmental Data API:** Secure headerAuth authentication setup required.
- **Email SMTP:** Configure SMTP credentials in n8n named `your_smtp_credential`.
- **User Input:** Modify the "Start - User Input" node to dynamically receive user commute data or leave defaults.

---

## Emission Factors Reference

| Mode        | Emission Factor (g CO2/km) |
|-------------|----------------------------|
| Car         | 192                        |
| Bus         | 80                         |
| Train       | 60                         |
| Bicycle     | 0                          |
| Walking     | 0                          |

---

## Recommendations Logic

- Emission footprint > 0: Suggest increased public transit or biking.
- Distance < 3 km: Recommend walking or biking.
- Air Quality Index > 100: Suggest telecommuting or low-emission transport use.

---

## Notes

- Coordinates format: `"latitude,longitude"` as strings (e.g., `"40.748817,-73.985428"`).
- The workflow is designed for daily commute tracking but can be adapted for any origin/destination route.
- Error handling: If no routes are found, an error JSON is returned and no email is sent.

---

## License

This workflow template is provided as-is for personal or organizational use to encourage eco-friendly commuting habits.