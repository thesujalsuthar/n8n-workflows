# AI-Powered Sustainable Travel and Carbon Offset Planner

## Overview

This workflow helps users plan sustainable travel by providing detailed route information, calculating the estimated carbon footprint of their trip, suggesting eco-friendly travel alternatives, and offering personalized carbon offset options. It leverages travel and carbon offset APIs, combined with custom logic, to facilitate informed and environmentally responsible travel decisions.

---

## Workflow Nodes

### 1. Start (Manual Trigger)
- **Purpose:** Entry point of the workflow where users input:
  - `origin` (starting location)
  - `destination` (ending location)
  - `departureTime` (planned travel time)
- **Type:** Manual trigger node that accepts user inputs to initiate the process.

---

### 2. Get Travel Route
- **Purpose:** Fetches travel details like route, distance, and duration from a travel API such as Google Maps or OpenRouteService.
- **Parameters:**
  - `operation`: get
  - `resource`: route
  - `from`: origin (user input)
  - `to`: destination (user input)
  - `travelMode`: driving
  - `departureTime`: user-supplied departure time
- **Credentials:** Requires HTTP Basic Authentication (`TravelAPI_Credentials`).
- **Output:** JSON containing route data including distance (in meters) and other travel information.

---

### 3. Calculate Carbon Footprint
- **Purpose:** Estimates the carbon footprint of the trip based on travel mode and distance.
- **Process:**
  - Converts distance from meters to kilometers.
  - Uses predefined emission factors (in kg CO2 per km):
    - Driving: 0.192 
    - Walking: 0 
    - Bicycling: 0 
    - Transit: 0.105
  - Calculates total CO2 emissions.
- **Output:** JSON including:
  - `distance_km`
  - `mode` (travel mode)
  - `co2_emissions_kg` (estimated emissions in kilograms)

---

### 4. Get Carbon Offset Options
- **Purpose:** Retrieves personalized carbon offset projects and pricing based on the estimated CO2 emissions.
- **Request:**
  - `POST` to Carbon Offset API endpoint (`https://api.carbonoffsetapi.com/v1/offsets`).
  - Payload includes:
    - `offset_amount`: CO2 emissions from the previous node.
    - `currency`: USD.
    - User preferences such as project type (`reforestation`) and region (`global`).
- **Credentials:** Uses HTTP Header Authentication (`CarbonOffsetAPI_Key`).
- **Output:** JSON with carbon offset options suitable for the computed emission amount.

---

### 5. Suggest Eco-friendly Alternatives
- **Purpose:** Proposes alternative travel modes with lower or zero carbon emissions.
- **Alternatives Provided:**
  - **Walking:** Zero emissions, healthiest option.
  - **Bicycling:** Zero emissions, ideal for short distances.
  - **Transit:** Estimated emissions roughly 45% less than driving.
- **Output:** JSON array of alternatives with:
  - `mode`
  - `estimated_emissions_kg`
  - `description`

---

### 6. Compile Result
- **Purpose:** Consolidates all the gathered data into a single comprehensive output for the user.
- **Includes:**
  - Summary:
    - Origin
    - Destination
    - Distance (km)
    - Travel mode
    - Estimated CO2 emissions (kg)
  - Carbon offset options from the API.
  - Eco-friendly travel alternatives.
- **Output:** Final JSON with all travel, environmental, offset, and alternative information.

---

## Workflow Execution Flow

1. **User Input**: The workflow begins with manual input of origin, destination, and departure time.
2. **Travel Data Retrieval**: Requests route and travel details from the travel API.
3. **Emissions Calculation**: Calculates carbon footprint using the travel data.
4. **Parallel Steps**:
   - Requests carbon offset options based on emissions.
   - Suggests possible eco-friendly travel alternatives.
5. **Result Compilation**: Collects and combines all outputs for user presentation.

---

## Integrations and Credentials

- **Travel API:** Requires HTTP Basic Auth credentials (`TravelAPI_Credentials`).
- **Carbon Offset API:** Requires HTTP Header Auth API key (`CarbonOffsetAPI_Key`).

---

## Usage Notes

- Input fields must be accurately filled for origin, destination, and departure time to receive correct route and emission data.
- Emission factors can be customized in the function node if new travel modes or updated factors become available.
- Carbon offset preferences (project type and region) can be tailored in the `Get Carbon Offset Options` node JSON body.
- The workflow supports scalability by leveraging parallel data fetches and combining results for efficient performance.

---

## Summary

This workflow empowers users to plan their trips sustainably by understanding the environmental impact and exploring both carbon offsetting and greener travel alternatives in an integrated manner.