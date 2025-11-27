# AI-Powered Personalized Sustainable Travel and Carbon Offset Planner

## Overview

This workflow provides a personalized sustainable travel planning service integrated with carbon footprint calculation and carbon offset recommendations. Users submit trip details and preferences via a webhook, and the workflow returns sustainable travel options, estimates the trip's carbon emissions, suggests offset projects, and sends an email summary.

---

## Workflow Nodes and Functionality

### 1. Webhook Trigger (`Webhook Trigger`)
- **Type:** Webhook (POST)
- **Path:** `/plan-trip`
- **Function:** Receives incoming user requests containing trip and preference data in JSON format.
- **Input Example:**
  ```json
  {
    "origin": "JFK",
    "destination": "LAX",
    "departureDate": "2024-09-01",
    "returnDate": "2024-09-10",
    "travelers": 2,
    "preferences": {
      "travelModes": ["train", "electric_vehicle"],
      "carbonOffset": true
    },
    "email": "user@example.com"
  }
  ```

---

### 2. Extract Trip Details (`Extract Trip Details`)
- **Type:** Function
- **Purpose:** Parses and extracts relevant trip information and user preferences from the webhook input.
- **Outputs:** JSON object with origin, destination, dates, number of travelers, and preferences.

---

### 3. Fetch Travel Options (`Fetch Travel Options`)
- **Type:** HTTP Request
- **API:** Travelpayouts Cheap Flights API (`https://api.travelpayouts.com/v2/prices/cheap`)
- **Method:** GET
- **Parameters:** Origin, destination, departure and return dates, currency (USD), sorting by price, economy class, limit 5 results.
- **Authentication:** HTTP Basic Auth (`travelAPI` credentials)
- **Output:** List of available travel options including flights, prices, and carriers.

---

### 4. Filter Sustainable Travel Options (`Filter Sustainable Travel Options`)
- **Type:** Function
- **Logic:** Filters travel options to prefer sustainable options such as electric aircraft or eco-friendly carriers. Falls back to top 5 cheapest if no sustainable travel options found.
- **Output:** Array of filtered travel options prioritized for sustainability.

---

### 5. Calculate Carbon Footprint (`Calculate Carbon Footprint`)
- **Type:** Function
- **Method:** Simplified estimation based on assumed distance, travelers, and an average kg CO2/km/person emission factor.
- **Formula:**
  ```
  carbonFootprintKgCO2 = distance(km) * 2 (round trip) * travelers * 0.2 (kg CO2 per km per person)
  ```
- **Output:** Estimated total carbon footprint (kg CO2) of the planned trip.

---

### 6. Get Carbon Offset Recommendations (`Get Carbon Offset Recommendations`)
- **Type:** HTTP Request
- **API:** Carbonscape Offset Recommendations (`https://api.carbonscape.io/v1/offsets/recommendations`)
- **Method:** POST
- **Payload:**
  ```json
  {
    "amount": <carbonFootprint in kg>,
    "currency": "USD",
    "preferences": {
      "projectTypes": ["reforestation", "renewable_energy"],
      "regions": ["global"]
    }
  }
  ```
- **Authentication:** Header Auth (`carbonOffsetAPI` credentials)
- **Output:** Recommended carbon offset projects tailored to the carbon footprint amount and preferences.

---

### 7. Compose Summary Message (`Compose Summary Message`)
- **Type:** Function
- **Purpose:** Combines sustainable travel options, carbon footprint estimate, and carbon offset recommendations into a single summary JSON message.
- **Output:** Structured trip summary JSON.

---

### 8. Send Notification Email (`Send Notification Email`)
- **Type:** Email Send
- **Subject:** "Your Sustainable Travel Plan & Carbon Offset Recommendations"
- **Content:** JSON formatted trip summary details.
- **Recipient:** Obtained from the initial webhook request email field.
- **SMTP Credentials:** Uses configured `smtpEmail` credentials to send the email notification.

---

## Input and Output Summary

- **Input:** POST JSON payload to `/plan-trip` webhook with trip details, traveler info, preferences, and email.
- **Output:** Email sent to user containing:
  - Personalized sustainable travel options.
  - Estimated carbon footprint of the trip.
  - Recommended carbon offset projects.

---

## Credentials Required

- **Travel API:** HTTP Basic Auth credentials for Travelpayouts API (`travelAPI`).
- **Carbon Offset API:** HTTP Header Auth credentials for Carbonscape API (`carbonOffsetAPI`).
- **SMTP Email:** SMTP credentials for sending emails (`smtpEmail`).

---

## How to Use

1. Deploy the workflow in your n8n instance.
2. Set up the required credentials for travel API, carbon offset API, and SMTP.
3. Provide the webhook URL (`/plan-trip`) to your frontend or clients.
4. Clients submit trip details via POST request.
5. The workflow processes the input and sends personalized sustainable travel plans and carbon offset suggestions directly to the user's email.

---

## Notes

- Carbon footprint calculation is simplified and intended for demonstration; replace or enhance with real distance and emissions data for production.
- Sustainable travel filtering assumes data availability indicating eco-friendly carriers or electric aircraft.
- Customize email template and data as needed.

---

Thank you for using the AI-Powered Personalized Sustainable Travel and Carbon Offset Planner!