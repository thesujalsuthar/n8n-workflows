# AI-Powered Sustainable Urban Mobility Planner

## Overview

This workflow integrates multiple urban mobility data sources with an AI-based route optimization logic to provide users with sustainable, cost-effective, and efficient travel recommendations between specified start and end locations. It harnesses real-time traffic, public transport schedules, bike-sharing availability, and electric vehicle (EV) charging station data to compose optimized travel plans that minimize carbon emissions and travel costs.

---

## Workflow Steps

### 1. Set Input Defaults
- **Node Type:** Function
- **Purpose:** Initializes input parameters with default values if not provided.  
- **Inputs:**
  - `startLocation`: Location where the journey begins (default: `"UserSpecifiedStart"`).
  - `endLocation`: Journey destination (default: `"UserSpecifiedEnd"`).
  - `travelTime`: Desired travel time or departure time (default: current timestamp).  
- **Output:** Populated JSON with these inputs, enabling downstream API requests.

---

### 2. Get Real-Time Traffic Data
- **Node Type:** HTTP Request
- **API:** `https://api.trafficservice.com/v1/traffic`
- **Purpose:** Retrieves live traffic conditions between the start and end locations.
- **Query Parameters:**
  - `location`: Start location
  - `destination`: End location
- **Response Format:** JSON  
- **Output:** Real-time traffic details including estimated travel times affected by current traffic.

---

### 3. Get Public Transport Schedules
- **Node Type:** HTTP Request
- **API:** `https://api.publictransport.com/v1/schedules`
- **Purpose:** Fetches available public transport options and schedules between origin and destination at the specified travel time.
- **Query Parameters:**
  - `origin`: Start location
  - `destination`: End location
  - `datetime`: Travel time/departure time
- **Response Format:** JSON  
- **Output:** Public transit connections, schedules, fare info, and timing estimates.

---

### 4. Get Bike-Sharing Availability
- **Node Type:** HTTP Request
- **API:** `https://api.bikesharing.com/v1/availability`
- **Purpose:** Checks for bike-sharing stations and bike availability near the start location.
- **Query Parameters:**
  - `location`: Start location
- **Response Format:** JSON  
- **Output:** List of bike-sharing stations, availability, and price data.

---

### 5. Get EV Charging Stations
- **Node Type:** HTTP Request
- **API:** `https://api.evcharging.com/v1/stations`
- **Purpose:** Retrieves EV charging stations near the destination location.
- **Query Parameters:**
  - `near`: End location
- **Response Format:** JSON  
- **Output:** EV charging station details to support electric vehicle route planning.

---

### 6. AI Route Optimization
- **Node Type:** Function
- **Purpose:** Consolidates all collected data (traffic, public transport, bike-sharing, EV charging) to generate an optimized route plan.
- **Description:**
  - Implements a mock AI decision model (simulated within the function) that:
    - Prefers bike-sharing if stations are available and distance is under 5 km.
    - Falls back to public transport if bike-sharing is not suitable.
    - Uses electric vehicle options if neither bike-sharing nor public transport are viable.
  - Calculates estimated carbon emissions, costs, and travel times per mode.
  - Composes recommended transport modes and route segments accordingly.
- **Output:** JSON object with:
  - `recommendedModes`: array of recommended transport modes.
  - `totalCarbonEmission`: estimated carbon footprint in kg CO2.
  - `totalCost`: total predicted cost of travel.
  - `totalTime`: total estimated travel time in minutes.
  - `segments`: breakdown of route segments by transport mode including duration, cost, and emissions.

---

### 7. Prepare Output Summary
- **Node Type:** Function
- **Purpose:** Formats the optimization result into a user-friendly summary.
- **Output:** JSON containing:
  - Summary message listing recommended modes.
  - Carbon emissions, travel cost, estimated travel time.
  - Detailed travel segments.
  - Placeholder for future sustainable recommendations.

---

### 8. Notify User
- **Node Type:** NoOp (can be modified for notifications)
- **Purpose:** Outputs a personalized notification message based on the travel recommendations.
- **Message Content:**
  ```
  Hello! Based on your daily commute from [startLocation] to [endLocation], here are your optimized travel options:
  - Modes: [recommendedModes]
  - Estimated Carbon Footprint: [totalCarbonEmission] kg CO2
  - Estimated Cost: [totalCost]
  - Estimated Travel Time: [totalTime] minutes

  Consider these sustainable options to reduce your environmental impact!
  ```

---

## Inputs Required

| Parameter      | Description                     | Example         | Default           |
|----------------|---------------------------------|-----------------|-------------------|
| startLocation  | Starting point of the journey    | `"Times Square"`| `"UserSpecifiedStart"`|
| endLocation    | Destination of the journey       | `"Central Park"`| `"UserSpecifiedEnd"` |
| travelTime     | Desired time for travel          | `"2024-06-20T08:00:00Z"` | Current timestamp |

---

## Outputs

- Recommended transport modes (bike-sharing, public transport, or electric vehicle).
- Estimated carbon footprint for the trip (in kilograms of CO2).
- Estimated travel cost in the local currency.
- Estimated travel duration (in minutes).
- Detailed segment data describing cost, duration, and emissions per mode.
- User notification message summarizing the plan.

---

## Notes

- The AI recommendation logic is a simulated placeholder for real AI/ML integration.
- Distance calculations and emission estimations are mocked for demo purposes.
- APIs used must be replaced or configured with valid endpoints and credentials for production deployment.
- The Notify User node uses a no-operation node for demonstration; this can be replaced with email, SMS, or UI notification actions.

---

## Deployment

- Import the workflow into n8n.
- Configure API credentials if needed.
- Provide input parameters (`startLocation`, `endLocation`, and `travelTime`) before triggering.
- Monitor output summary and notification for route recommendations.

---

## License

This project is provided as-is for urban mobility planning and sustainability use cases. Adapt and extend to fit specific city and user needs.