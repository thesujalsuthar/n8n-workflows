# Sustainable Travel Planning & Carbon Offset Optimization Workflow

## Overview

This workflow is designed to provide sustainable travel itinerary planning combined with carbon footprint calculation and optimized carbon offset recommendations. It leverages travel route data and carbon offset options, processing user preferences to deliver eco-friendly travel plans along with actionable carbon offset solutions.

---

## Workflow Nodes and Functionality

### 1. **HTTP Trigger - Plan Itinerary**
- **Type:** HTTP Trigger
- **Method:** POST
- **Path:** `/plan-itinerary`
- **Purpose:** Entry point to receive user travel planning requests.
- **Response:** Returns the final recommendation from the workflow.

---

### 2. **Extract User Input**
- **Type:** Function Node
- **Purpose:** Extracts and structures user input from the HTTP request body.
- **Input Data Parameters:**
  - `origin`: Starting location of the travel
  - `destination`: Ending location
  - `departureDate`: Planned start date
  - `returnDate`: Planned return date
  - `preferences`: Optional travel preferences (e.g., preferred modes of transport)
- **Action:** Prepares a JSON object with these parameters for further use.

---

### 3. **Get Travel Routes**
- **Type:** HTTP Request
- **Purpose:** Calls an external Travel Data API to retrieve possible travel routes between origin and destination.
- **Authentication:** Bearer token header - requires `YOUR_TRAVEL_API_KEY`
- **Request URL Example:**  
  ```
  https://api.traveldata.com/routes?origin={origin}&destination={destination}&departureDate={departureDate}&returnDate={returnDate}
  ```
- **Output:** JSON list of travel routes, including route segments and details.

---

### 4. **Calculate Carbon Footprint & Score**
- **Type:** Function Node
- **Purpose:** Calculates carbon footprints for each route and scores sustainability based on transportation modes and user preferences.
- **Carbon Emission Factors (kg CO2 per km):**
  - Flight: 0.255
  - Train: 0.041
  - Bus: 0.105
  - Car: 0.192
- **User Preferences Effects:**
  - Increases score if user prefers trains.
  - Decreases score if user wants to avoid flights.
- **Result:** Adds `carbonFootprintKg` and `sustainabilityScore` to each route.

---

### 5. **Filter & Rank Routes**
- **Type:** Function Node
- **Purpose:** Filters routes by a minimum sustainability score specified by user preferences, defaults to 0.5.
- **Logic:**
  - Only routes above minimum sustainability score are kept.
  - If no route meets threshold, returns the best available route.
  - Routes are sorted by sustainability score descending.
- **Output:** Filtered and ranked sustainable routes.

---

### 6. **Get Carbon Offset Options**
- **Type:** HTTP Request
- **Purpose:** Calls an external Carbon Offset API to retrieve available carbon offset products.
- **Authentication:** Bearer token header - requires `YOUR_CARBON_OFFSET_API_KEY`
- **Request URL:**  
  ```
  https://api.carbonoffset.com/options
  ```
- **Output:** List of carbon offset options including offset amount and pricing.

---

### 7. **Merge Route & Offset Data**
- **Type:** Merge Node
- **Merge Mode:** Merge by Index
- **Purpose:** Combines the best sustainable route data with retrieved carbon offset options for subsequent processing.

---

### 8. **Recommend Carbon Offsets**
- **Type:** Function Node
- **Purpose:** Selects appropriate carbon offset options based on the route's calculated carbon footprint.
- **Logic:**
  - Attempts to select a single offset option that meets or exceeds the footprint.
  - If none matches, combines multiple smaller offsets to meet required offset amount.
  - Offsets are sorted by price ascending to recommend cost-effective options.
- **Output:** Adds `recommendedOffsets` array to the route data.

---

### 9. **Format Response**
- **Type:** Function Node
- **Purpose:** Constructs the final output JSON response with clear structure including itinerary details, carbon footprint, sustainability score, and recommended carbon offsets.
- **Response Structure:**
  - `itinerary`: Travel route details (start, end, dates, segments)
  - `carbonFootprintKg`: Calculated carbon emission for the trip
  - `sustainabilityScore`: Eco-friendliness score for route
  - `recommendedCarbonOffsets`: Selected carbon offset options for compensation

---

### 10. **HTTP Response**
- **Type:** HTTP Response
- **Purpose:** Sends the final formatted response back to the user via the original HTTP POST request.

---

## Usage Notes

- Replace `YOUR_TRAVEL_API_KEY` with a valid API key for the travel routes provider.
- Replace `YOUR_CARBON_OFFSET_API_KEY` with a valid API key for the carbon offset provider.
- User preferences allow customization such as minimum sustainability score, mode preferences, or flight avoidance.
- The workflow supports extensibility for additional transport modes and more detailed carbon emission factors.

---

## Summary

This workflow offers a full pipeline for sustainable travel planning:

1. Accepts travel details and preferences via an API call.
2. Retrieves possible travel routes.
3. Calculates carbon footprints.
4. Filters and ranks routes by eco-friendliness.
5. Fetches carbon offset options.
6. Matches offset product recommendations.
7. Returns a clean, actionable itinerary and offset plan.

Use this workflow to build travel applications focused on environmental impact awareness and carbon footprint mitigation.