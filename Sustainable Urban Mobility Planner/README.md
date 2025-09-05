# Sustainable Urban Mobility Planner

## Overview

The **Sustainable Urban Mobility Planner** workflow integrates real-time public transit and bike-sharing data to provide users with eco-friendly route planning, carbon footprint estimation, transit alerts, and personalized mobility recommendations.

---

## Workflow Nodes and Data Flow

### 1. Fetch Real-time Transit Data
- **Type:** HTTP Request (GET)
- **Endpoint:** `https://api.publictransit.example.com/realtime`
- **Purpose:** Retrieve up-to-date transit stop information including location, schedule, and status.
- **Output:** Real-time transit data JSON.

---

### 2. Fetch Bike-sharing Availability
- **Type:** HTTP Request (GET)
- **Endpoint:** `https://api.bikesharing.example.com/availability`
- **Purpose:** Get current bike-sharing station information including the number of available bikes.
- **Output:** Bike-sharing availability JSON.

---

### 3. Merge Transit & Bike Data
- **Type:** Function
- **Description:** 
  - Combines transit stops and bike-sharing stations.
  - Filters bike stations to only those with available bikes.
  - Produces a structured dataset with current transit stops and accessible bike stations.
- **Key Logic:**
  ```js
  const bikeStations = bike.stations.filter(s => s.available_bikes > 0);
  const transitStops = transit.stops;
  return { bikeStations, transitStops };
  ```
- **Output:** Merged dataset containing `bikeStations` and `transitStops`.

---

### 4. Generate Pedestrian-friendly Routes
- **Type:** Function
- **Description:** Generates walking route suggestions connecting transit stops to nearby bike stations within 1 km.
- **Logic:**
  - Calculates approximate distance between each transit stop and bike station.
  - Suggests routes if distance ≤ 1 km.
- **Output:** List of pedestrian routes including `from`, `to`, and `distanceKm`.

---

### 5. Calculate Carbon Footprint
- **Type:** Function
- **Inputs:**
  - Real-time transit data.
  - User's planned route segments (with mode and distance).
- **Description:** Estimates total carbon emissions for the planned mobility route segments.
- **Carbon Emission Rates (kg CO2/km):**
  - Transit: 0.05
  - Bike: 0
  - Walk: 0
- **Output:** Carbon footprint estimate in kilograms of CO2.

---

### 6. Compute Eco-friendly Mobility Score
- **Type:** Function
- **Inputs:**
  - Carbon footprint estimate.
  - Number of pedestrian-friendly routes.
  - Count of transit stops.
  - Count of bike stations.
- **Description:** Calculates a mobility score (0-100) reflecting sustainability and efficiency.
- **Scoring Heuristics:**
  - Starts at 100.
  - Subtracts 10 × carbon footprint penalty.
  - Adds bonuses for pedestrian routes, transit stops, and bike stations.
- **Output:** Eco-friendly mobility score.

---

### 7. Fetch Transit Alerts
- **Type:** HTTP Request (GET)
- **Endpoint:** `https://api.publictransit.example.com/status/alerts`
- **Purpose:** Retrieves current transit system alerts including delays or disruptions.
- **Output:** Raw alerts JSON.

---

### 8. Filter Transit Delays & Disruptions
- **Type:** Function
- **Description:** Processes transit alerts to filter only those related to delays or disruptions.
- **Output:** Filtered alerts list.

---

### 9. Generate Alert Message
- **Type:** Function
- **Description:** Constructs a human-readable alert message based on filtered transit alerts.
- **Conditions:**
  - If alerts exist: Reports number of active transit issues.
  - If no alerts: Reports "No current transit delays or disruptions."
- **Output:** Alert message string.

---

### 10. Generate Personalized Recommendations
- **Type:** Function
- **Inputs:**
  - User preferences (e.g., prefers bike, prefers walk).
  - User history (e.g., frequent delays on certain lines).
- **Description:** Provides customized suggestions based on user profile and transit history.
- **Examples:**
  - Suggest starting near bike-sharing stations if preferred.
  - Recommend pedestrian routes if preferred.
  - Warn about frequently delayed transit lines.
- **Output:** List of personalized recommendations.

---

### 11. Compile Summary
- **Type:** Function
- **Inputs:**
  - Carbon footprint
  - Eco-friendly mobility score
  - Pedestrian routes
  - Transit alerts and alert message
  - Personalized recommendations
- **Description:** Gathers all processed information into a single comprehensive JSON summary.
- **Output:** Summary JSON including:
  - `carbonFootprintKgCO2`
  - `ecoFriendlyScore`
  - `pedestrianRoutes`
  - `transitAlerts`
  - `alertMessage`
  - `personalizedRecommendations`

---

## Workflow Execution Flow

- Starts by fetching real-time transit data.
- Immediately fetches bike-sharing availability.
- Merges transit and bike data for integrated mobility options.
- Generates pedestrian-friendly routes connecting transit and bike-sharing stations.
- Calculates carbon footprint based on planned segments and transit data.
- Computes an eco-friendly mobility score combining carbon data with mobility options.
- Fetches and filters transit alerts related to delays and disruptions.
- Creates user-friendly alert messages.
- Generates personalized recommendations using user preferences and history.
- Compiles all metrics, alerts, routes, and recommendations into a final summary.

---

## Usage Notes

- The workflow expects live API endpoints for transit data, bike-sharing availability, and transit alerts.
- User preferences and history data should be passed to relevant nodes for personalized recommendations.
- Carbon footprint calculations assume fixed emission rates; adjust if accurate mode-specific data is available.
- Pedestrian routes are suggested based on a simple geographic proximity heuristic.

---

## Summary

This workflow enables a comprehensive, real-time evaluation and planning of sustainable urban mobility options by integrating multi-modal transit data, environmental impact estimation, system status, and user-centric recommendations, fostering greener and more efficient urban travel choices.