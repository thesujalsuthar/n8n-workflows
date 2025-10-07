# AI-Driven Personalized Sustainable Travel and Carbon Offset Planner

## Overview

This workflow provides a comprehensive sustainable travel planning solution by integrating travel option discovery, carbon footprint estimation, and carbon offset project recommendations. The goal is to create a personalized travel itinerary that minimizes environmental impact and encourages carbon offsetting for unavoidable emissions.

---

## Workflow Nodes and Detailed Functionality

### 1. Prepare Travel Options Request
- **Type**: Function
- **Description**: Extracts trip details such as origin, destination, travel dates, and user preferences from the input JSON. Constructs a GET request URL to query the sustainable travel options API.
- **Key Output**: A request object containing the API endpoint URL, method (GET), and authentication headers.

### 2. Get Sustainable Travel Options
- **Type**: HTTP Request
- **Description**: Sends the request prepared in the previous node to the sustainable travel options API to retrieve available travel methods aligned with user preferences and sustainability criteria.
- **Credentials**: Uses the `SustainableTravelAPI` API key for authentication.
- **Response**: JSON data with a list of travel options.

### 3. Format Travel Options
- **Type**: Function
- **Description**: Parses the API response to extract key details for each travel option, including transportation method, vehicle type, trip duration, cost, and optionally preliminary emissions estimates.
- **Output**: An array of structured travel option JSON objects for downstream processing.

### 4. Prepare Carbon Footprint Request
- **Type**: Function
- **Description**: For each travel option, prepares a POST request payload to the carbon footprint API. The payload includes mode of travel, distance in kilometers (defaulting to 0 if unavailable), and vehicle type.
- **Key Output**: Request object with URL, POST method, headers (including API key), and JSON body.

### 5. Get Carbon Footprint Estimate
- **Type**: HTTP Request
- **Description**: Sends the prepared carbon footprint calculation requests to the carbon footprint API.
- **Credentials**: Uses the `CarbonFootprintAPI` API key.
- **Response**: JSON response containing estimated CO2 emissions for each travel leg.

### 6. Extract Carbon Footprint Data
- **Type**: Function
- **Description**: Extracts and normalizes the carbon emissions data (in kg CO2) from the API response for each travel option.
- **Output**: JSON objects including transportation method and estimated emissions.

### 7. Summarize Total Carbon Footprint
- **Type**: Function
- **Description**: Aggregates the CO2 emissions from all travel options to compute the total carbon footprint of the planned trip.
- **Output**: Summary JSON containing origin, destination, travel dates, and total estimated emissions.

### 8. Prepare Carbon Offset Projects Request
- **Type**: Function
- **Description**: Based on the total emissions, decides whether to recommend carbon offsetting:
  - If emissions are below 10 kg CO2, it outputs a message indicating offsetting may not be necessary.
  - If above threshold, prepares a GET request to query carbon offset projects suitable for offsetting the trip's emissions, filtered by location and minimum offset amount.
- **Credentials**: Uses the `CarbonOffsetAPI` API key for authentication.

### 9. Get Recommended Carbon Offset Projects
- **Type**: HTTP Request
- **Description**: Fetches a list of eligible carbon offset projects from the API.
- **Credentials**: Uses the `CarbonOffsetAPI` API key.

### 10. Format Carbon Offset Projects
- **Type**: Function
- **Description**: Formats and extracts key details from the carbon offset projects response, including project ID, name, description, offset capacity in kg CO2, and project URL.
- **Output**: An array of JSON project objects or a message if no projects are suggested.

### 11. Generate Personalized Sustainable Itinerary
- **Type**: Function
- **Description**: Integrates travel options with their associated carbon footprints and matches them with recommended carbon offset projects. Constructs a comprehensive itinerary including estimated emissions per trip segment and offset recommendations.
- **Output**: JSON containing:
  - Detailed itinerary with mode, vehicle type, duration, and emissions per segment.
  - Total emissions for the trip.
  - List of recommended offset projects.

### 12. Create Summary Message
- **Type**: Set
- **Description**: Generates a human-readable summary message combining trip details, total emissions, travel itinerary, and carbon offset project recommendations.
- **Output**: A formatted text summary ready for display or user delivery.

---

## Required Credentials

- **SustainableTravelAPI**: API key credential for fetching sustainable travel options.
- **CarbonFootprintAPI**: API key credential for calculating carbon footprint estimates.
- **CarbonOffsetAPI**: API key credential for retrieving carbon offset projects.

---

## Input Data Format

The input JSON to initiate the workflow should follow this structure:

```json
{
  "origin": "CityA",
  "destination": "CityB",
  "travelDates": {
    "start": "YYYY-MM-DD",
    "end": "YYYY-MM-DD"
  },
  "preferences": {
    // User preferences for travel options, e.g., "avoidFlights": true, "preferredVehicleTypes": ["train"]
  }
}
```

---

## Workflow Execution Flow

1. **Receive trip details** → Prepare request for sustainable travel options.
2. Fetch sustainable travel options → Format options for processing.
3. For each travel option → Prepare and send carbon footprint calculation requests.
4. Extract and summarize total emissions.
5. If emissions exceed threshold → Fetch recommended carbon offset projects.
6. Format offset projects → Combine with travel itinerary and emissions data.
7. Generate and output a final summary message.

---

## Use Case

This workflow empowers travelers and planners to make informed decisions about sustainable travel by:

- Identifying eco-friendly travel modes.
- Quantifying trip carbon emissions.
- Suggesting carbon offset projects to mitigate environmental impact.
- Presenting clear, actionable itinerary and offset recommendations.

---

## Notes

- Emissions estimates depend on the accuracy of external APIs.
- Carbon offset recommendations are location-specific and filtered by emission offset amount.
- The threshold for suggesting offsets is customizable (default 10 kg CO2).

---

## License

This workflow content is provided as-is without warranty. Users should comply with API usage terms of the integrated services.