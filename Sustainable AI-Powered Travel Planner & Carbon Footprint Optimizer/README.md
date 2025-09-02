# AI-Driven Personalized Sustainable Travel Itinerary and Carbon Offset Planner

This workflow creates a personalized sustainable travel itinerary based on user input for a travel destination. It calculates the estimated carbon footprint of the trip, provides eco-friendly transportation and accommodation options, suggests carbon offset plans with costs, and generates actionable eco travel tips.

---

## Workflow Overview

### 1. Receive Destination Input  
- **Node:** Receive Destination Input (HTTP Trigger)  
- **Description:** Listens for a POST request containing the travel destination and basic travel data (e.g., distance, nights). This triggers the whole workflow.

### 2. Fetch Destination Data  
- **Node:** Fetch Destination Data (Function)  
- **Function:** Simulates fetching destination details such as country, attractions, best travel period, and average temperature.  
- **Note:** In production, this should connect to a real travel information API.

### 3. Recommend Sustainable Transportation  
- **Node:** Recommend Sustainable Transportation (Function)  
- **Function:** Lists transportation modes with their CO2 emissions per kilometer, emphasizing low-impact options like train, electric car, bus, and bicycle.

### 4. Suggest Eco-Friendly Accommodations  
- **Node:** Suggest Eco-Friendly Accommodations (Function)  
- **Function:** Provides accommodations with eco-certifications and estimated nightly CO2 emissions.

### 5. Calculate Transportation Footprint  
- **Node:** Calculate Transportation Footprint (Set)  
- **Function:** Calculates carbon emissions for transportation by multiplying distance and CO2 per kilometer.

### 6. Calculate Accommodation Footprint  
- **Node:** Calculate Accommodation Footprint (Function)  
- **Function:** Calculates carbon emissions for accommodation based on number of nights and average nightly CO2 emission.

### 7. Aggregate Total Carbon Footprint  
- **Node:** Aggregate Total Carbon Footprint (Function)  
- **Function:** Sums transportation and accommodation footprints to estimate the total carbon footprint of the trip.

### 8. Provide Carbon Offset Options  
- **Node:** Provide Carbon Offset Options (Function)  
- **Function:** Lists carbon offset projects with costs per kilogram of CO2 offset, such as tree planting, renewable energy projects, and methane capture.

### 9. Calculate Offset Cost  
- **Node:** Calculate Offset Cost (Function)  
- **Function:** Calculates the estimated cost to offset the total carbon footprint based on the chosen offset project.

### 10. Generate AI-Driven Eco Travel Tips  
- **Node:** Generate AI-Driven Eco Travel Tips (Function)  
- **Function:** Provides a set of personalized eco-friendly travel tips to reduce environmental impact.

### 11. Compile Final Itinerary and Report  
- **Node:** Compile Final Itinerary and Report (Function)  
- **Function:** Aggregates all gathered data and calculations into one structured itinerary and sustainability report.

### 12. Respond with Itinerary and Plan  
- **Node:** Respond with Itinerary and Plan (HTTP Response)  
- **Function:** Returns the complete personalized itinerary, transportation and accommodation options, carbon footprint information, offset plans, and eco travel tips in the response.

---

## Input Data Structure

The initial POST request to `/get-destination-data` should include JSON data like:

```json
{
  "destination": "Example City",
  "distanceKm": 500,
  "nights": 4,
  "chosenOffset": "Tree Planting"
}
```

- `destination`: The travel destination city or location.  
- `distanceKm`: Distance in kilometers for transportation carbon footprint calculation.  
- `nights`: Number of nights staying at accommodation. Defaults to 3 if not provided.  
- `chosenOffset`: The carbon offset project selected by the user. Defaults to "Tree Planting".

---

## Output

The response will include:

- **itinerary:** Destination details, attractions, best travel period, average temperature.  
- **transportationOptions:** List of recommended sustainable transportation modes with descriptions and estimated emissions.  
- **accommodations:** Eco-friendly lodging options with certifications and average nightly CO2 emissions.  
- **carbonFootprint:** Total estimated CO2 emissions split by transportation and accommodation.  
- **offsetEstimate:** Cost estimate and details for chosen carbon offset plan.  
- **ecoTravelTips:** Personalized tips to reduce travel environmental impact.

Example output snippet:

```json
{
  "itinerary": {
    "destination": "Example City",
    "attractions": ["Eco Park", "Sustainable Museum", "Nature Reserve"],
    "bestTravelPeriod": "April to June",
    "avgTempC": 22
  },
  "transportationOptions": [...],
  "accommodations": [...],
  "carbonFootprint": {
    "totalKgCO2": 50,
    "offsetOptions": [...]
  },
  "offsetEstimate": {
    "offsetName": "Tree Planting",
    "offsetCostUSD": 1.0,
    "totalCO2": 50
  },
  "ecoTravelTips": [
    "Bring a reusable water bottle",
    "Choose direct flights when possible",
    "Pack light to reduce fuel consumption",
    "Use public or shared transport locally"
  ]
}
```

---

## Notes

- This is a simulated workflow intended to demonstrate integration of sustainable travel planning with carbon footprint calculations and offsetting.  
- Replace simulated data fetches with actual external API calls for live data in production.  
- Adjustable parameters (e.g., emission factors, offset costs) are contained within node function code for easy updates.  
- Expand eco travel tips and offset options as needed for richer personalization.

---

End of Workflow Documentation.