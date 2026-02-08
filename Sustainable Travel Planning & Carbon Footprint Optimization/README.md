# Travel Planning & Carbon Footprint Workflow

This workflow is designed to accept user travel details and preferences via a webhook, calculate the travel carbon footprint, fetch relevant environmental impact data for the travel destination, generate a personalized sustainable travel plan with carbon offset recommendations, and respond with the comprehensive plan.

---

## Workflow Overview

1. **Webhook Trigger**  
   Receives a POST request at the path `/start-travel-planning` containing user travel preferences and travel details.

2. **Extract Preferences & Travel Data**  
   Extracts `preferences` and `travelDetails` from the incoming JSON payload for further processing.

3. **Calculate Carbon Footprint**  
   Calculates an estimated carbon footprint (in kilograms of CO2) based on the user's travel mode and distance, adjusted according to preferences such as low carbon mode.

4. **Fetch Environmental Impact Metrics**  
   Makes a GET request to the OpenTree Eco API to retrieve environmental data for the travel destination, using the location specified in `travelDetails.destination`. Authentication is handled via `x-api-key` from environment variables (`OPENTREE_API_KEY`).

5. **Generate Travel Plan & Offsets**  
   Combines the carbon footprint data, user preferences, and fetched environmental metrics to build a personalized sustainable travel plan. It includes recommendations for carbon offset options filtered by user preferred offset types, with estimated costs to offset the calculated carbon footprint.

6. **Respond to User**  
   Sends back the final travel plan, including carbon footprint, destination info, environmental metrics, and offset recommendations, as the response to the original webhook request.

---

## Node Details

### 1. Webhook  
- **HTTP Method:** POST  
- **Path:** `/start-travel-planning`  
- **Response Mode:** Last Node (sends the final node's output as response)  

### 2. Extract Preferences & Travel Data (Function Node)  
- Extracts `preferences` and `travelDetails` from the incoming JSON object.

### 3. Calculate Carbon Footprint (Function Node)  
- Simulates carbon footprint calculation using distance and mode of travel:  
  - Flight: 0.21 kg CO2/km  
  - Train: 0.05 kg CO2/km  
  - Car: 0.12 kg CO2/km (slightly penalized if low carbon mode is preferred)  
  - Default: 0.1 kg CO2/km  
- Applies adjustments based on user preference `lowCarbonMode`.

### 4. Fetch Environmental Impact Metrics (HTTP Request Node)  
- Requests environmental data relevant to travel destination from OpenTree Eco API:  
  - **Endpoint:** `https://api.opentree.eco/v1/environmental-data`  
  - **Query Parameter:** `location` set dynamically to `travelDetails.destination`  
  - **Header:** `x-api-key` from environment variable `OPENTREE_API_KEY`  

### 5. Generate Travel Plan & Offsets (Function Node)  
- Receives carbon footprint, preferences, and environmental metrics.  
- Provides three carbon offset options with URLs and cost per kg CO2 offset:  
  - Reforestation Projects  
  - Renewable Energy Investment  
  - Clean Cookstoves  
- Filters these options if user specifies `preferredOffsetTypes`.  
- Calculates estimated cost in USD for offsets based on carbon footprint.  
- Returns a comprehensive travel plan JSON object containing:  
  - `carbonFootprintKg`  
  - `destination`  
  - `preferredMode`  
  - `environmentalMetrics`  
  - `offsetRecommendations`  

### 6. Respond to User (Respond to Webhook Node)  
- Outputs the full travel plan JSON back to the caller of the webhook request.

---

## Input JSON Example (POST `/start-travel-planning`)

```json
{
  "preferences": {
    "lowCarbonMode": true,
    "preferredOffsetTypes": ["Reforestation Projects", "Clean Cookstoves"]
  },
  "travelDetails": {
    "mode": "car",
    "distance": 150,
    "destination": "Portland"
  }
}
```

---

## Output JSON Example

```json
{
  "carbonFootprintKg": "19.80",
  "destination": "Portland",
  "preferredMode": "car",
  "environmentalMetrics": { /* Data returned from OpenTree API */ },
  "offsetRecommendations": [
    {
      "offsetName": "Reforestation Projects",
      "offsetUrl": "https://www.ecosia.org/donate",
      "estimatedCostUSD": "0.40"
    },
    {
      "offsetName": "Clean Cookstoves",
      "offsetUrl": "https://offset.climateneutralnow.org",
      "estimatedCostUSD": "0.30"
    }
  ]
}
```

---

## Environment Variables

- `OPENTREE_API_KEY` — API key for accessing the OpenTree Eco environmental data API.

---

## Notes

- The carbon footprint calculation implemented here is a simplified simulation for demonstration. It can be extended or replaced with calls to real carbon footprint services.
- Offsets and cost calculations are examples and should be adapted to real-world pricing and options as needed.
- The workflow is designed to be easily extensible by adding more sophisticated logic or integrating additional APIs.

---

## Usage

1. Import this workflow into your n8n instance.  
2. Set the `OPENTREE_API_KEY` environment variable for the HTTP Request node.  
3. Send a POST request to the webhook URL with user travel preferences and travel details.  
4. Receive the personalized sustainable travel plan response.