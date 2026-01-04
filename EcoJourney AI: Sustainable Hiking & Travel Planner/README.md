# EcoJourney AI: Personalized Sustainable Hiking and Travel Planner

## Overview

EcoJourney AI is an automated workflow designed to create a personalized, sustainable hiking and travel plan. It integrates user preferences, environmental data, carbon footprint estimation, and carbon offset options to help users plan eco-friendly trips.

---

## Workflow Nodes and Description

### 1. Start
- **Type:** Start Node
- **Purpose:** Entry point for the workflow.

---

### 2. Get User Preferences
- **Type:** Function Node
- **Purpose:** Extracts and structures user input preferences.
- **Inputs Handled:** 
  - `location` (latitude, longitude, and name)
  - `hikingDifficulty` (default: "moderate")
  - `travelDates` (start and end, default: null)
  - `interests` (default: ["nature", "culture"])
  - `sustainabilityFocus` (default: true)

---

### 3. Fetch Environmental Data
- **Type:** HTTP Request Node
- **Purpose:** Retrieves weather and environmental forecast data for the selected location and dates.
- **API Used:** OpenWeather One Call API
- **Parameters:**
  - Latitude and longitude from user preferences
  - Excludes minutely and hourly data, focuses on daily data
  - Uses metric units
  - API key injected from environment variable `OPENWEATHER_API_KEY`

---

### 4. Calculate Environmental Impact
- **Type:** HTTP Request Node
- **Purpose:** Estimates the carbon footprint of the planned hiking and travel activities.
- **API Used:** Carbon Interface API
- **Parameters:**
  - Type: "hiking_and_travel"
  - Distance in kilometers (default: 100 km if not provided)
  - Origin and destination from user preferences
  - API key accessed via environment variable `CARBON_INTERFACE_API_KEY`

---

### 5. Get Carbon Offset Options
- **Type:** HTTP Request Node
- **Purpose:** Fetches a list of carbon offset projects related to forestry for global impact mitigation.
- **API Used:** CarbonFund API
- **Query Parameters:**
  - Category: "forest"
  - Region: "global"

---

### 6. Compile Personalized Plan
- **Type:** Function Node
- **Purpose:** Synthesizes all collected data into a comprehensive personalized hiking and travel plan with sustainability focus.
- **Outputs:**
  - **Recommended Hikes:** Trail suggestions with difficulty, weather forecast, and sustainable hiking tips (e.g., Leave No Trace principles).
  - **Travel Tips:** Best dates, sustainable travel recommendations, and eco-friendly behavior tips.
  - **Environmental Impact:** Detailed carbon footprint information.
  - **Carbon Offset Options:** Top three carbon offset projects suitable for purchase with their name, price, description, and URL.

---

## Environment Variables Required

- `OPENWEATHER_API_KEY` - API key for OpenWeather One Call API.
- `CARBON_INTERFACE_API_KEY` - API key for Carbon Interface API.

---

## Workflow Execution Flow

1. **Start** node triggers the workflow.
2. **Get User Preferences** collects and normalizes inputs.
3. The preferences are piped into both:
    - **Fetch Environmental Data** to gather weather info.
    - **Calculate Environmental Impact** to estimate carbon cost.
4. The carbon impact output triggers:
    - **Get Carbon Offset Options** to suggest offset projects.
5. All data streams into **Compile Personalized Plan** which creates the final output.

---

## Output Structure

The output JSON contains a `personalizedPlan` object with:

- `recommendedHikes`: Array of hike suggestions (name, difficulty, weather forecast, notes).
- `travelTips`: Object including best travel dates, sustainability focus flag, and tips.
- `environmentalImpact`: Carbon footprint data from the Carbon Interface.
- `carbonOffsetOptions`: Array of top carbon offset project details.

---

## Usage

- Integrate this workflow in n8n or compatible automation platforms.
- Provide user input as JSON with at least a `location` object including `latitude`, `longitude`, and `name`.
- Set required environment variables.
- Trigger execution to receive a sustainable hiking and travel plan tailored to the user's preferences.

---

## Notes

- The default hiking difficulty is moderate if not specified.
- Default travel distance for carbon impact estimation is 100 km if user input is missing.
- Focus is maintained on sustainable travel and minimizing environmental footprint throughout planning.