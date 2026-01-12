# EcoTravel AI: Personalized Sustainable Trip Planner & Carbon Offset Optimizer

## Overview
EcoTravel AI is a workflow designed to provide personalized sustainable travel recommendations and carbon offset options based on user trip details. It calculates the estimated carbon footprint of a trip and offers eco-friendly travel alternatives along with tailored carbon offset solutions. The final output is sent as a notification to the user.

---

## Workflow Steps

### 1. Get Travel Data
- **Type:** HTTP Request (GET)
- **Description:** Fetches travel information such as distance and transport mode from an external Travel Data Service API based on user inputs: origin, destination, and date.
- **Inputs:**
  - `origin` (string)
  - `destination` (string)
  - `date` (string, format: YYYY-MM-DD)
- **Authentication:** HTTP Basic Auth
- **Output:** Travel data JSON including total distance and transport mode.

### 2. Calculate Carbon Footprint
- **Type:** Function
- **Description:** Processes travel data to calculate the trip’s carbon footprint in kilograms of CO2.
- **Logic:**
  - Extracts distance (km) and transport mode.
  - Uses pre-defined emission factors (kg CO2 per km) for `flight`, `train`, `bus`, and `car`.
  - Calculates carbon emissions = distance × emission factor.
- **Output:** Object including distance, transport mode, and carbon emissions (kg CO2).

### 3. Get Sustainable Travel Recommendations
- **Type:** HTTP Request (POST)
- **Description:** Requests sustainable and lower-carbon travel options from a Sustainable Travel API.
- **Inputs:** 
  - Origin, destination, date 
  - Calculated carbon emissions
  - Preferred transport modes prioritized for sustainability: train, bus, car
  - Optimization goal: carbon reduction
- **Authentication:** HTTP Basic Auth
- **Output:** Recommendation list of sustainable travel options.

### 4. Get Carbon Offset Options
- **Type:** HTTP Request (GET)
- **Description:** Retrieves suitable carbon offset options from a Carbon Offset API based on the calculated carbon footprint.
- **Inputs:**
  - Amount (carbon emissions in kg)
  - Region: "global"
- **Authentication:** API Key
- **Output:** List of carbon offset programs or products.

### 5. Format Notification Message
- **Type:** Function
- **Description:** Combines travel footprint details, sustainable travel recommendations, and carbon offset options into a user-friendly notification message.
- **Format:**
  ```
  Your trip from [origin] to [destination] on [date] generates approximately [carbonEmissionsKg] kg CO2. We recommend the following sustainable travel options: [recommendations], and carbon offset options suitable for your footprint.
  ```

### 6. Send Notification
- **Type:** Twilio
- **Description:** Sends the formatted message to the user’s contact number via SMS.
- **Inputs:** 
  - Message (from previous step)
  - Recipient phone number (`userContact`)
- **Authentication:** Twilio API credentials

---

## Inputs Required
- `origin` (string): Departure location code or name.
- `destination` (string): Arrival location code or name.
- `date` (string): Travel date in ISO format (YYYY-MM-DD).
- `userContact` (string): Phone number to receive notifications.

---

## Authentication & API Credentials
- **Travel Data Service:** HTTP Basic Auth credential (`Travel API Basic Auth`)
- **Sustainable Travel API:** HTTP Basic Auth credential (`Sustainable Travel API Auth`)
- **Carbon Offset API:** API Key credential (`Carbon Offset API Key`)
- **Twilio API:** Twilio API credential (`Twilio API`)

---

## Summary
This workflow automates the entire process starting from fetching travel data, calculating carbon emissions, recommending sustainable travel alternatives, suggesting carbon offset options, and finally delivering personalized notifications to users. EcoTravel AI helps travelers minimize their carbon footprint and make environmentally responsible choices effortlessly.