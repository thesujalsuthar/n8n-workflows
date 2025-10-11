# GreenCommute AI Scheduler

## Overview

GreenCommute AI Scheduler is an automated workflow designed to help users plan eco-friendly daily commutes by aggregating and analyzing transportation data from multiple sources. The workflow integrates public transit information, bike share availability, and EV charging station data to provide personalized recommendations focused on reducing carbon emissions. Users receive customized email recommendations and timely alerts related to their commute options.

---

## Workflow Breakdown

### 1. Extract User Input

- **Type:** Function
- **Purpose:** Parses and organizes the user's input for the commute.
- **Input:** JSON with fields such as:
  - `origin`: Starting location
  - `destination`: Ending location
  - `preferredModes`: Array of transport modes (optional; defaults to `['public_transit', 'bike_share', 'ev_charging']`)
  - `departureTime`: Desired departure time (optional; defaults to current time)
- **Output:** Structured commute details to be used by subsequent nodes.

---

### 2. Fetch Public Transit Routes

- **Type:** HTTP Request
- **API Endpoint:** `https://api.publictransit.example.com/routes`
- **Query Parameters:**
  - `origin`: Starting location from user input
  - `destination`: Ending location from user input
  - `departureTime`: Departure time from user input
- **Purpose:** Retrieves available public transit routes for the given trip.

---

### 3. Fetch Bike Share Data

- **Type:** HTTP Request
- **API Endpoint:** `https://api.bikeshare.example.com/availability`
- **Query Parameters:**
  - `near`: User's origin location
- **Purpose:** Gets availability data for nearby bike share stations.

---

### 4. Fetch EV Charging Stations

- **Type:** HTTP Request
- **API Endpoint:** `https://api.evcharging.example.com/stations`
- **Query Parameters:**
  - `near`: User's origin location
- **Purpose:** Fetches nearby EV charging station availability.

---

### 5. Process and Score Recommendations

- **Type:** Function
- **Purpose:** 
  - Analyzes and scores public transit routes based on efficiency and environmental impact.
  - Filters bike share stations and EV charging stations by availability.
  - Builds a combined recommendation list prioritizing eco-friendly options.
  - Calculates estimated carbon savings compared to a conventional commute baseline.
- **Key Logic:**
  - Assigns scores to transit routes rewarding buses/trains and penalizing long durations and CO2 emissions.
  - Selects top bike stations and EV charging stations available near the user.
  - Calculates carbon savings by comparing baseline CO2 emissions against recommended options.

---

### 6. Send User Recommendations Email

- **Type:** Email Send
- **Recipients:** User's email (from input JSON fields `userEmail` or `email`)
- **Subject:** "GreenCommute AI Scheduler: Your Daily Commute Recommendations"
- **Content:** Provides:
  - Summary of recommended commute modes and details
  - Estimated carbon savings in grams of CO2
  - Encouragement for eco-friendly commuting

---

### 7. Generate Alerts

- **Type:** Function
- **Purpose:** Creates alert messages regarding potential issues in the commute:
  - Delays in public transit routes
  - Lack of available bikes at bike share stations
  - Lack of available ports at EV charging stations
- **Output:** List of alert messages to notify the user.

---

### 8. Send Alert Emails

- **Type:** Email Send
- **Recipients:** User's email as above
- **Subject:** "GreenCommute AI Scheduler: Alerts Update"
- **Content:** Contains commuting alerts or a message confirming no current alerts.

---

## Workflow Connections

- The workflow starts by extracting user input.
- Then concurrently fetches data for public transit, bike share, and EV charging.
- These datasets feed into a single node to process and score recommendations.
- After scoring, the workflow splits to send recommendations and also generate alerts.
- Alerts, if any, are sent in a separate email to the user.

---

## Usage

1. **Input Requirements:** Provide user commute details either via an API trigger or manual input in the following JSON format:

```json
{
  "origin": "User's starting location",
  "destination": "User's destination",
  "preferredModes": ["public_transit", "bike_share", "ev_charging"], // optional
  "departureTime": "ISO8601 formatted date-time string", // optional
  "userEmail": "user@example.com"
}
```

2. **Execution:** Run the workflow in an n8n environment.

3. **Result:** The user receives two types of emails:
   - A daily commute recommendation email with travel options and carbon savings.
   - Alerts email regarding any issues or shortages in the recommended modes.

---

## Notes

- APIs in this workflow use example endpoints; replace them with actual service URLs.
- Scoring and recommendation logic can be customized for more sophisticated environmental impact calculations.
- Email service in n8n must be configured for sending messages.
- The carbon savings calculation uses a simplified model; extend as needed for accuracy.

---

Thank you for using the GreenCommute AI Scheduler — your partner for smarter, greener travel!