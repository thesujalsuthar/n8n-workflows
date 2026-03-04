# AI-Driven Personalized Eco-Friendly Travel Planner and Carbon Offset Advisor

## Overview

This workflow generates a personalized, eco-friendly travel plan tailored to the user's destination, travel dates, and preferences. It fetches transportation and accommodation options along with their environmental impact data, calculates the estimated carbon footprint of the planned travel, and recommends carbon offset contributions to mitigate the environmental impact.

---

## Workflow Breakdown

### 1. Extract User Input

- **Node Type:** Function
- **Purpose:** Extracts user input parameters from the incoming JSON.
- **Input Parameters:** 
  - `destination`: Intended travel location.
  - `travelDates`: Planned travel dates.
  - `preferences`: User travel preferences.
- **Output:** Passes extracted data for subsequent API calls.

---

### 2. Fetch Transportation Options

- **Node Type:** HTTP Request
- **Purpose:** Retrieves transportation choices available for the specified destination.
- **Parameters:**
  - `resource`: `transport`
  - `operation`: `getOptions`
  - `query`: Destination extracted from user input.
- **Authentication:** HTTP Basic Auth (credentials to be configured).
- **Output:** Returns a list of transportation options including types and distances.

---

### 3. Fetch Accommodation Eco-Ratings

- **Node Type:** HTTP Request
- **Purpose:** Obtains environmental (eco) ratings for accommodation options in the destination.
- **Parameters:**
  - `resource`: `accommodation`
  - `operation`: `getEcoRatings`
  - `query`: Destination from user input.
- **Authentication:** HTTP Basic Auth (credentials to be configured).
- **Output:** List of accommodations with their eco-scores.

---

### 4. Calculate Carbon Footprint

- **Node Type:** Function
- **Purpose:** Computes an estimated carbon footprint from transportation and accommodation data.
- **Logic:**
  - Transportation footprint is calculated as distance multiplied by a factor (0.12).
  - Accommodation footprint estimated as inverse of ecoScore (5 minus score).
- **Output:** Carbon footprint summaries for transportation and accommodation, plus original options data.

---

### 5. Compute Offset Suggestions

- **Node Type:** Function
- **Purpose:** Aggregates total carbon footprint and calculates offset recommendations.
- **Logic:**
  - Sum transportation and accommodation footprints.
  - Suggest offset amount at 110% of total carbon footprint for compensation.
- **Output:** Total carbon footprint and recommended offset contribution.

---

### 6. Fetch Carbon Offset Projects

- **Node Type:** HTTP Request
- **Purpose:** Retrieves a list of carbon offset projects focusing on reforestation.
- **Parameters:**
  - `resource`: `carbonOffsetProjects`
  - `operation`: `list`
  - `filters`: `{ type: "reforestation" }`
- **Authentication:** HTTP Basic Auth (credentials to be configured).
- **Output:** List of available carbon offset projects.

---

### 7. Recommend Offset Contributions

- **Node Type:** Function
- **Purpose:** Selects top 3 reforestation projects to recommend for offset contribution.
- **Logic:**
  - Sort projects by rating in descending order.
  - Pick top 3 highest-rated projects.
- **Output:** Recommended offset projects and suggested offset amount.

---

### 8. Return Final Plan

- **Node Type:** No Operation (PassThrough)
- **Purpose:** Outputs the final structured plan containing all recommendations.
- **Output:** Final eco-friendly travel plan with transportation, accommodation, carbon footprint, and offset project recommendations.

---

## Credentials Setup

The workflow requires HTTP Basic Authentication credentials for the following API calls:

- Fetch Transportation Options
- Fetch Accommodation Eco-Ratings
- Fetch Carbon Offset Projects

Please configure these credentials in your n8n instance before activating the workflow.

---

## Usage

1. Provide user input JSON containing:
   - `destination`
   - `travelDates`
   - `preferences`
2. The workflow processes the input and produces:
   - Transportation and accommodation options with eco ratings.
   - Calculated carbon footprint.
   - Recommended carbon offset amounts.
   - Top carbon offset projects for contribution.

---

## Notes

- Carbon footprint calculations use fixed factors for demonstration; replace with real data or API integration as needed.
- Offset suggestions include a 10% buffer above estimated emissions to ensure full compensation.
- Reforestation projects are used as the carbon offset type in this workflow but can be customized.

---

Thank you for using the AI-Driven Personalized Eco-Friendly Travel Planner and Carbon Offset Advisor!