# AI Sustainable Trip Planner Workflow

This workflow is designed to provide sustainable travel planning by optimizing routes, estimating carbon footprints, and suggesting carbon offset options based on trip details submitted via an HTTP POST request.

---

## Workflow Overview

1. **HTTP Request Trigger**  
   - Listens for POST requests at the `/plan-trip` endpoint.  
   - Accepts JSON input containing trip details and returns a JSON response from the last node.

2. **Parse Input**  
   - Extracts travel details from the incoming JSON payload:  
     - `origin`: Starting location  
     - `destination`: Ending location  
     - `departureDate`: Date of departure  
     - `returnDate`: Date of return (optional)  
     - `preferences`: Travel preferences such as preferred transport modes (optional)

3. **Optimize Route & Transport**  
   - Simulates optimizing the travel route focusing on sustainability.  
   - Selects the best transportation mode prioritizing lower carbon emissions or user preferences.  
   - Uses static data for demonstration with predefined transport options including electric trains, electric buses, carpool electric vehicles, and bikes.  
   - Calculates estimated travel time and carbon emissions based on a fixed example distance (250 km).

4. **Calculate Carbon Footprint**  
   - Calculates the total carbon footprint for the trip.  
   - Accounts for round-trip distances if a `returnDate` is provided.  
   - Outputs total distance traveled and total estimated CO2 emissions in kilograms.

5. **Suggest Carbon Offsets**  
   - Provides carbon offset project suggestions based on the calculated carbon footprint.  
   - Includes projects such as Reforestation in the Amazon, Wind Energy Support, and Community Solar initiatives.  
   - Estimates minimum purchase requirements and costs for offsetting the trip’s emissions.

6. **Respond with Plan**  
   - Sends the final JSON response back to the requester containing:  
     - Selected transportation mode  
     - Total distance and carbon footprint  
     - Suggested carbon offset options and their estimated costs

---

## Input Format

Send a POST request to the `/plan-trip` endpoint with a JSON body containing:

```json
{
  "origin": "City A",
  "destination": "City B",
  "departureDate": "YYYY-MM-DD",
  "returnDate": "YYYY-MM-DD",         // Optional
  "preferences": {
    "preferredModes": ["electric_train", "bike"]   // Optional
  }
}
```

---

## Output Format

The JSON response includes:

```json
{
  "mode": "electric_train",
  "totalDistanceKm": 500,
  "totalCO2Kg": 7,
  "offsetOptions": [
    {
      "name": "Reforestation in Amazon",
      "purchaseKg": 10,
      "estimatedCost": 0.2,
      "description": "Planting trees to absorb carbon emissions"
    },
    {
      "name": "Wind Energy Support",
      "purchaseKg": 10,
      "estimatedCost": 0.15,
      "description": "Investing in clean wind energy projects"
    },
    {
      "name": "Community Solar",
      "purchaseKg": 10,
      "estimatedCost": 0.18,
      "description": "Supporting solar power installations"
    }
  ]
}
```

- `mode`: Selected transportation mode for the trip.  
- `totalDistanceKm`: Total kilometers traveled (round trip if return date provided).  
- `totalCO2Kg`: Estimated total carbon emissions in kilograms.  
- `offsetOptions`: List of carbon offset projects with purchase requirements and estimated costs.

---

## Notes

- The distance value and transportation options are currently static for demonstration purposes.  
- Carbon emission factors and speeds are based on typical values for each mode.  
- The workflow prioritizes user preferences if specified; otherwise, it selects the lowest carbon emission mode by default.  
- Carbon offset costs and minimum purchase amounts are predefined for each project.

---

## Deployment

- Import this workflow into your n8n environment.  
- Activate the workflow.  
- Make POST requests to the `/plan-trip` webhook with the required input JSON to receive sustainable trip planning results.