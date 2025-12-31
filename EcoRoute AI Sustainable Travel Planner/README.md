# EcoRoute AI Planner

EcoRoute AI Planner is an automated workflow designed to provide optimized, eco-friendly travel itineraries based on user-supplied start and destination locations. The workflow integrates multiple routing data sources and calculates carbon footprints to suggest the most environmentally sustainable travel options, along with carbon offset recommendations.

---

## Workflow Overview

The workflow processes input locations, fetches route options for multiple transportation modes, calculates environmental impact metrics, and returns an optimized itinerary with carbon offset suggestions.

---

## Workflow Nodes Description

### 1. Webhook Start
- **Node Name:** `Webhook Start`
- **Function:** Receives POST requests containing JSON payload with `start_location` and `destination_location`.
- **HTTP Method:** POST
- **Path:** `/ecoroute-ai`
- **Outputs:** Incoming request body forwarded for extraction.

---

### 2. Extract Input
- **Node Name:** `Extract Input`
- **Function:** Parses the webhook request payload to extract `start_location` and `destination_location`.
- **Output Data:** `{ start_location, destination_location }`

---

### 3. Geocode Start Location
- **Node Name:** `Geocode Start Location`
- **Service:** OpenStreetMap Geocoding API
- **Operation:** Forward geocoding (address → coordinates)
- **Input:** `start_location`
- **Output:** Latitude and longitude coordinates for the start location.

---

### 4. Geocode Destination Location
- **Node Name:** `Geocode Destination Location`
- **Service:** OpenStreetMap Geocoding API
- **Operation:** Forward geocoding
- **Input:** `destination_location`
- **Output:** Latitude and longitude coordinates for the destination location.

---

### 5. Fetch Transit Routes
- **Node Name:** `Fetch Transit Routes`
- **Service:** Public Transit API (`https://api.publictransit.example.com`)
- **Authentication:** Bearer token (`PUBLIC_TRANSIT_API_KEY`)
- **Parameters:**
  - `startLat`, `startLon` from Geocode Start Location node.
  - `endLat`, `endLon` from Geocode Destination Location node.
  - Mode: `transit`
- **Output:** Available public transit routes with distance and duration.

---

### 6. Fetch Bike Routes
- **Node Name:** `Fetch Bike Routes`
- **Service:** Bike Routes API (`https://api.bikeroutes.example.com`)
- **Authentication:** API key header (`BIKEROUTES_API_KEY`)
- **Parameters:**
  - Coordinates of start and destination locations.
- **Output:** Available bike routes with associated metrics.

---

### 7. Fetch Walking Paths
- **Node Name:** `Fetch Walking Paths`
- **Service:** Walking Paths API (`https://api.walkingpaths.example.com`)
- **Authentication:** Bearer token (`WALKING_PATHS_API_KEY`)
- **Parameters:**
  - Coordinates of start and destination locations.
- **Output:** Available walking routes.

---

### 8. Optimize Routes
- **Node Name:** `Optimize Routes`
- **Type:** Function Node
- **Functionality:**
  - Aggregates routes from Transit, Bike, and Walking APIs.
  - Calculates carbon footprint based on mode and distance:
    - Transit: 0.05 kg CO2 per km (example emission factor)
    - Bike & Walk: 0 (zero emissions)
  - Extracts travel time (duration in minutes).
  - Sorts routes prioritizing lowest carbon footprint; ties broken by shortest travel time.
- **Output:** Sorted list of routes with mode, carbon footprint, and travel time metadata.

---

### 9. Prepare Carbon Offset Data
- **Node Name:** `Prepare Carbon Offset Data`
- **Type:** Function Node
- **Functionality:**
  - Selects the best route (first in the sorted list).
  - Prepares data for carbon offset API:
    - Total carbon footprint in kg CO2.
    - API endpoint URL (`https://api.carbonoffset.example.com/suggest`).
    - API key (`CARBON_OFFSET_API_KEY`).
- **Output:** Payload with carbon footprint and API access details for offset suggestions.

---

### 10. Get Carbon Offset Suggestions
- **Node Name:** `Get Carbon Offset Suggestions`
- **Service:** Carbon Offset API
- **Authentication:** Bearer token using the provided API key
- **Method:** POST
- **Payload:**
  - `carbon_amount`: total carbon footprint from chosen route.
  - `currency`: USD
- **Output:** Carbon offset project suggestions or options returned by API.

---

### 11. Compile Final Itinerary
- **Node Name:** `Compile Final Itinerary`
- **Type:** Function Node
- **Functionality:**
  - Combines optimized route details with carbon offset suggestions.
  - Output JSON includes:
    - Itinerary (mode, route details, carbon footprint in kg CO2, travel time in minutes).
    - Carbon offset suggestions.
- **Final Output:** Delivered as the response to the initial webhook call.

---

## Input Format

The workflow expects incoming JSON via a webhook POST request in the following structure:

```json
{
  "start_location": "Address or place name of start",
  "destination_location": "Address or place name of destination"
}
```

---

## Output Format

The workflow returns a JSON response structured as:

```json
{
  "itinerary": {
    "mode": "transit|bike|walk",
    "details": { /* route details from the respective API */ },
    "carbonFootprintKgCO2": 1.23,
    "travelTimeMinutes": 45
  },
  "carbonOffsetSuggestions": [
    /* Array of carbon offset projects or suggestions */
  ]
}
```

---

## Required Credentials and API Keys

- **OpenStreetMap:** No authentication needed for geocoding.
- **Public Transit API:** Bearer token via `PUBLIC_TRANSIT_API_KEY`.
- **Bike Routes API:** API key in header `x-api-key` named `BIKEROUTES_API_KEY`.
- **Walking Paths API:** Bearer token via `WALKING_PATHS_API_KEY`.
- **Carbon Offset API:** Bearer token via `CARBON_OFFSET_API_KEY`.

Please ensure these environment variables or credentials are set accordingly in your workflow environment.

---

## Summary

EcoRoute AI Planner provides an automated, multi-modal, environmentally-aware travel planning solution by:

- Geocoding user input locations.
- Fetching transit, bike, and walking route options.
- Calculating and comparing carbon emissions.
- Suggesting optimized routes favoring eco-friendliness and time efficiency.
- Offering carbon offset project recommendations to neutralize travel emissions.

Deploy this workflow to accept location inputs and receive optimized, sustainable travel itineraries seamlessly.

---

**End of README**