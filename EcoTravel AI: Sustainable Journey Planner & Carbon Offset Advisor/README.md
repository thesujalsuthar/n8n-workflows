# EcoTravel AI: Sustainable Itinerary & Carbon Offset Workflow

## Overview

EcoTravel AI is an automated workflow designed to generate sustainable travel itineraries and recommend carbon offset options based on user travel preferences. It integrates travel options retrieval, carbon emissions calculation, carbon offset recommendations, and sustainable travel tips to support eco-friendly travel planning.

---

## Workflow Components

### 1. Start Node
- **Type**: Start
- **Function**: Entry point of the workflow that passes input travel preferences downstream.

---

### 2. Fetch Travel Options
- **Type**: Function Node
- **Purpose**: 
  - Receives travel preferences including origin, destination, departure and return dates, transport modes, and budget.
  - Fetches possible travel options (segments and details) from travel providers or APIs.
- **Details**:
  - Currently, it returns mock travel options including train, bus, and flight segments with cost, duration, and distance.
  - Each option consists of an array of segments describing transport mode, origin, destination, departure/arrival dates, and distance in km.
  
---

### 3. Calculate Carbon Emissions
- **Type**: Function Node
- **Purpose**:
  - Calculates estimated carbon footprint (CO2 equivalent emissions in kg) for each travel option.
- **Details**:
  - Uses predefined emission factors (kg CO2e per km) for different transport modes:
    - Flight: 0.254
    - Train: 0.041
    - Bus: 0.105
    - Car: 0.192
  - Iterates through travel segments, multiplies segment distances by emission factors, and sums total emissions per option.
  - Adds a new field `carbonEmissionsKg` to each travel option.

---

### 4. Recommend Carbon Offsets
- **Type**: Function Node
- **Purpose**:
  - Provides recommendations for carbon offsetting based on calculated emissions.
- **Details**:
  - Offers a curated list of sustainable carbon offset programs:
    - **Cool Effect - Reforestation Project**: Supports reforestation efforts.
    - **Terrapass - Renewable Energy**: Invests in renewable energy projects.
    - **Myclimate - Clean Cooking**: Advances clean cookstove initiatives.
  - Calculates recommended offset amount as 110% of emissions for extra coverage and assigns it to each travel option.
  - Returns offset recommendations with program details for each option.

---

### 5. Sustainable Travel Tips
- **Type**: Function Node
- **Purpose**:
  - Provides actionable sustainable travel tips to reduce environmental impact.
- **Contents**:
  - Pack light to reduce fuel consumption.
  - Use reusable water bottles and avoid single-use plastics.
  - Prefer public transport or bike rentals at the destination.
  - Support local businesses and eco-friendly accommodations.
  - Avoid unnecessary flights; prefer trains or buses.
  - Offset your carbon footprint through certified programs.
  - Conserve water and electricity during your stay.

---

### 6. End Node
- **Type**: No Operation (NoOp)
- **Function**: Marks the end of the workflow for both carbon offset recommendations and sustainable travel tips flows.

---

## Workflow Execution Flow

1. **Start** node receives travel preferences as input.
2. Transfers to **Fetch Travel Options** to generate possible itineraries.
3. Feeds output to **Calculate Carbon Emissions** to compute emissions per itinerary.
4. From carbon emissions:
   - Sends data to **Recommend Carbon Offsets** for offsetting options.
   - Simultaneously sends data to **Sustainable Travel Tips** for helpful eco-friendly advice.
5. Both recommendation and tips outputs proceed to the **End** node.

---

## Input Specification

```json
{
  "travelPreferences": {
    "origin": "CityA",
    "destination": "CityB",
    "departureDate": "YYYY-MM-DD",
    "returnDate": "YYYY-MM-DD",
    "transportModes": ["train", "bus", "flight"],
    "budget": 100
  }
}
```

- `origin`: Starting location.
- `destination`: Destination location.
- `departureDate` and `returnDate`: Travel dates.
- `transportModes`: Allowed transport modes.
- `budget`: Maximum travel budget.

---

## Output Data

- **Travel Options:** Array containing multiple itinerary options with segments, costs, duration, and distances.
- **Carbon Emissions:** Emissions calculated per travel option in kilograms of CO2 equivalent.
- **Carbon Offset Recommendations:** Suggested offset amounts (110% coverage) plus list of offset programs.
- **Sustainable Travel Tips:** Practical advice for minimizing travel’s environmental impact.

---

## Extensibility Notes

- **API Integration:** Replace mock travel option generation with real travel API calls.
- **Emission Factors:** Update emission factors based on latest environmental data.
- **Offset Programs:** Add dynamic offset program recommendations or link user preferences.
- **Additional Tips:** Tailor travel tips based on destination or travel mode.

---

## License

This workflow content is provided as-is for educational and integration purposes. Use and customize as needed for sustainable travel solutions.