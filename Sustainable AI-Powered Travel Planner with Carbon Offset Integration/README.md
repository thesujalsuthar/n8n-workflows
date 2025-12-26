# AI-Driven Personalized Eco-Friendly Travel Itinerary Planner and Carbon Offset Calculator

## Overview

This workflow is designed to generate a personalized, sustainable travel itinerary by integrating various transportation options, eco-friendly accommodations, and activities with an AI-driven carbon footprint calculator and carbon offset purchasing mechanism. It fetches travel options, calculates environmental impact, recommends offset options, and assembles the information into a coherent travel plan.

---

## Workflow Nodes and Descriptions

### 1. Fetch Flight Options
- **Type:** HTTP Request
- **Purpose:** Retrieves available flight options for the specified trip.
- **API Endpoint:** `https://api.traveldata.com/v1/flights`
- **Inputs:**
  - Origin airport (`origin`)
  - Destination airport (`destination`)
  - Departure date (`departure_date`)
  - Return date (`return_date`)
- **Authentication:** Bearer token via header (`YOUR_TRAVELDATA_API_KEY`)
- **Response:** JSON list of flight options.

---

### 2. Fetch Train Options
- **Type:** HTTP Request
- **Purpose:** Fetches train travel options matching origin, destination, and departure date.
- **API Endpoint:** `https://api.traveldata.com/v1/trains`
- **Inputs:**
  - Origin station (`origin`)
  - Destination station (`destination`)
  - Date of travel (`date`)
- **Authentication:** Bearer token via header (`YOUR_TRAVELDATA_API_KEY`)
- **Response:** JSON list of train options.

---

### 3. Fetch Car Rental Options
- **Type:** HTTP Request
- **Purpose:** Obtains car rental options for the trip duration starting at origin location.
- **API Endpoint:** `https://api.carrental.com/v1/cars`
- **Inputs:**
  - Pickup location (`pickup_location`)
  - Pickup date (`pickup_date`)
  - Dropoff date (`dropoff_date`)
- **Authentication:** Bearer token via header (`YOUR_CARRENTAL_API_KEY`)
- **Response:** JSON list of car rental options.

---

### 4. Calculate Carbon Footprints
- **Type:** Function
- **Purpose:** Calculates estimated carbon emissions (kg CO2) for each travel option using placeholder carbon factors based on an assumed distance.
- **Process:**
  - Retrieves flights, trains, and cars data from previous nodes.
  - Uses default carbon emission factors per km:
    - Flight: 0.255 kg CO2/km
    - Train: 0.041 kg CO2/km
    - Car: 0.192 kg CO2/km
  - Assumes a default distance of 1000 km (can be replaced with actual calculation).

- **Output:**
  - Carbon footprint per option (flights, trains, cars) listed by ID.

---

### 5. Fetch Eco-Friendly Accommodations
- **Type:** HTTP Request
- **Purpose:** Searches for eco-friendly lodging options at the destination, accommodating user preferences.
- **API Endpoint:** `https://api.ecoaccommodations.com/v1/search`
- **Inputs:**
  - Location (destination)
  - Preferences (serialized JSON object)
- **Authentication:** Bearer token via header (`YOUR_ECOACC_API_KEY`)
- **Response:** JSON list of eco-friendly accommodations.

---

### 6. Fetch Eco-Friendly Activities
- **Type:** HTTP Request
- **Purpose:** Retrieves a list of environmentally-friendly activities available at the destination.
- **API Endpoint:** `https://api.ecoactivities.com/v1/activities`
- **Inputs:**
  - Location (destination)
  - Type set to `"eco-friendly"`
- **Authentication:** Bearer token via header (`YOUR_ECOACT_API_KEY`)
- **Response:** JSON list of eco-friendly activities.

---

### 7. Generate Carbon Offset Options
- **Type:** Function
- **Purpose:** Calculates total carbon emissions across all modes of transportation and provides carbon offset options with estimated costs.
- **Details:**
  - Sums carbon footprint from flights, trains, and car rentals.
  - Presents offset options:
    - Tree Planting: $0.02 per kg CO2
    - Clean Energy Projects: $0.03 per kg CO2
- **Output:**
  - Total carbon footprint (kg CO2).
  - Offset options with descriptions and prices.

---

### 8. Assemble Sustainable Itinerary
- **Type:** Function
- **Purpose:** Compiles all collected data into a single comprehensive itinerary object.
- **Includes:**
  - Flights, trains, car rentals
  - Eco-friendly accommodations
  - Eco-friendly activities
  - Calculated carbon footprints
  - Carbon offset suggestions
- **Output:** Structured JSON containing the full sustainable travel itinerary.

---

### 9. Purchase Carbon Offset
- **Type:** HTTP Request (POST)
- **Purpose:** Finalizes a carbon offset purchase based on user selection and the total calculated emissions.
- **API Endpoint:** `https://api.carbonoffset.com/v1/purchase`
- **Inputs:**
  - User ID (`userId`)
  - Offset method ID (`offsetMethod`)
  - Amount of CO2 to offset (`amountKgCO2`)
- **Authentication:** Bearer token (configured in workflow)
- **Response:** Confirmation / receipt of purchase.

---

## Data Flow & Connections

- The workflow begins by fetching travel options (flights, trains, cars).
- These options feed into calculating individual carbon footprints.
- Carbon footprints are then used to generate carbon offset options.
- Parallel requests fetch eco-friendly accommodations and activities.
- All gathered data is merged into a sustainable travel itinerary.
- Finally, the workflow supports purchasing the carbon offset selected by the user.

---

## Setup Instructions

1. **API Keys:** Replace placeholder API keys (`YOUR_TRAVELDATA_API_KEY`, `YOUR_CARRENTAL_API_KEY`, `YOUR_ECOACC_API_KEY`, `YOUR_ECOACT_API_KEY`) and set authentication tokens accordingly.
2. **Customize Distance Calculation:** Replace the placeholder distance function with actual geographic calculations if desired.
3. **Input Data:** Ensure input JSON contains keys:
   - `origin`
   - `destination`
   - `departureDate`
   - `returnDate`
   - Optionally, `preferences` for accommodation filters.
4. **User Information:** For carbon offset purchases, provide the user identifier and selected offset method.

---

## Notes

- Carbon emission factors are placeholders and should be updated with accurate scientifically sourced values if possible.
- The workflow is modular and can be extended with additional transport modes or accommodation sources.
- All APIs used should be properly licensed and accessible with valid credentials.

---

_End of documentation for **AI-Driven Personalized Eco-Friendly Travel Itinerary Planner and Carbon Offset Calculator**._