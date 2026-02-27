# AI-Powered Sustainable Travel & Carbon Offset Planner

This workflow is designed to help users plan eco-friendly trips by providing sustainable travel options, estimating the carbon footprint of their travel plans, and recommending carbon offset programs to neutralize emissions. It leverages AI and external APIs to deliver personalized travel insights and actionable plans for reducing environmental impact.

---

## Workflow Overview

The workflow consists of six main steps, orchestrated in sequence to turn user inputs into actionable sustainable travel recommendations and carbon offset suggestions:

### 1. Start
- **Node:** `Start`
- **Type:** Trigger node to initiate the workflow.

### 2. Extract User Input
- **Node:** `Extract User Input`
- **Type:** Function
- **Description:** Extracts user trip details such as `destination`, `startDate`, `endDate`, and `preferences` from the incoming data.
- **Output:** A JSON object containing the extracted user input.

```javascript
const { destination, startDate, endDate, preferences } = $json;

return [{
  json: {
    destination,
    startDate,
    endDate,
    preferences
  }
}];
```

---

### 3. Fetch Sustainable Travel Options
- **Node:** `Fetch Sustainable Travel Options`
- **Type:** HTTP Request
- **Description:** Calls the external API `https://api.travel-sustainable-options.com/v1/options` with user inputs as query parameters to retrieve a list of environmentally friendly travel options tailored to the user's trip.

**Query Parameters:**
- `destination` — Destination city or location.
- `startDate` — Start date of the trip.
- `endDate` — End date of the trip.
- `preferences` — User travel preferences serialized as a JSON string.

- **Response Format:** JSON

---

### 4. Calculate Carbon Footprint
- **Node:** `Calculate Carbon Footprint`
- **Type:** Function
- **Description:** Processes the sustainable travel options data to estimate the total carbon footprint (in kilograms of CO2) based on distance and emission factors for each travel mode.

**Logic:**
- Iterates over each travel option.
- Extracts `distance_km` and `emission_factor_kg_per_km`.
- Calculates cumulative emissions: `totalCarbonKg += distance * emissionFactor`.

**Output:**
- The original sustainable travel options.
- The computed `totalCarbonKg` representing estimated carbon emissions for the trip.

```javascript
const sustainableOptions = $items("Fetch Sustainable Travel Options")[0].json.options;

// Mock logic: Calculate estimated carbon footprint based on travel modes and distance
let totalCarbonKg = 0;
sustainableOptions.forEach(option => {
  const distance = option.distance_km || 0;
  const emissionFactor = option.emission_factor_kg_per_km || 0;
  totalCarbonKg += distance * emissionFactor;
});

return [{ json: { sustainableOptions, totalCarbonKg } }];
```

---

### 5. Fetch Carbon Offset Programs
- **Node:** `Fetch Carbon Offset Programs`
- **Type:** HTTP Request
- **Description:** Fetches available carbon offset projects from `https://api.carbon-offsets.org/v2/projects` that can compensate for the estimated emissions.

**Query Parameters:**
- `max_emissions_kg` — The total carbon footprint calculated in the previous step to filter eligible offset projects.
- `type` — Set to `"all"` to fetch all types of projects.

- **Response Format:** JSON

---

### 6. Generate Personalized Insights & Action Plan
- **Node:** `Generate Personalized Insights & Action Plan`
- **Type:** Function
- **Description:** Combines travel options, estimated carbon footprint, and offset projects to generate a user-specific summary and actionable plan.

**Functionality:**
- Filters carbon offset projects that have sufficient available credits to fully offset the trip's emissions.
- Selects the top 3 recommended offset projects.
- Creates an action plan with a summary and step-by-step guidance.

**Output:**

- `sustainableTravelOptions`: List of travel options.
- `estimatedCarbonFootprintKg`: Total estimated emissions.
- `recommendedCarbonOffsetProjects`: Matching offset projects.
- `actionPlan`: Summary and travel recommendations.

```javascript
const sustainableOptions = $items("Calculate Carbon Footprint")[0].json.sustainableOptions;
const totalCarbonKg = $items("Calculate Carbon Footprint")[0].json.totalCarbonKg;
const offsetProjects = $items("Fetch Carbon Offset Programs")[0].json.projects;

// Generate personalized recommendations
const recommendedProjects = offsetProjects.filter(proj => proj.available_credits_kg >= totalCarbonKg).slice(0, 3);

return [{
  json: {
    sustainableTravelOptions: sustainableOptions,
    estimatedCarbonFootprintKg: totalCarbonKg,
    recommendedCarbonOffsetProjects: recommendedProjects,
    actionPlan: {
      summary: `For your trip to ${$json.destination}, your estimated carbon footprint is ${totalCarbonKg.toFixed(2)} kg of CO2. Consider these travel options and offset programs.`,
      steps: [
        "Select one or more sustainable travel options from the list provided.",
        "Purchase carbon offsets from recommended projects to neutralize emissions.",
        "Follow best eco-friendly travel practices included in the travel options details."
      ]
    }
  }
}];
```

---

## API Details

### Travel Sustainable Options API
- **Endpoint:** `https://api.travel-sustainable-options.com/v1/options`
- **Method:** GET
- **Query Parameters:** `destination`, `startDate`, `endDate`, `preferences` (JSON string)
- **Response:** List of sustainable travel options including distance and emission factors.

### Carbon Offsets API
- **Endpoint:** `https://api.carbon-offsets.org/v2/projects`
- **Method:** GET
- **Query Parameters:** `max_emissions_kg`, `type`
- **Response:** List of carbon offset projects with available credit information.

---

## Usage

1. Trigger the workflow and pass user travel details (`destination`, `startDate`, `endDate`, `preferences`).
2. The workflow fetches sustainable options and calculates carbon emissions.
3. It retrieves matching carbon offset projects.
4. Finally, it generates a personalized travel and offset action plan for the user.

---

## Conclusion

This AI-Powered Sustainable Travel & Carbon Offset Planner enables users to make environmentally conscious travel decisions by integrating data-driven sustainable travel options, emission calculations, and carbon offsetting programs into one streamlined workflow. This tool promotes greener travel habits and helps users reduce their environmental impact effectively.