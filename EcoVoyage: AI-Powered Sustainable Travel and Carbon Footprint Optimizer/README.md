# AI-Driven Personalized Sustainable Travel Planner and Carbon Footprint Optimizer

This workflow is designed to create personalized, eco-friendly travel itineraries that optimize for sustainable travel and minimize carbon footprints. It leverages AI (GPT-4) to generate detailed travel plans based on user input and offers suggestions for carbon offsetting.

---

## Overview

The workflow exposes an HTTP POST webhook endpoint `/plan-travel` that accepts user travel preferences and details. It processes the input to generate a customized sustainable travel itinerary, including estimated carbon footprint calculations. The final response includes suggested carbon offset methods to help travelers minimize their environmental impact.

---

## Workflow Nodes Description

### 1. Webhook (`Webhook`)
- **Type:** HTTP POST Endpoint
- **Path:** `plan-travel`
- **Purpose:** Entry point for receiving user travel requests with JSON payload.
- **Response Mode:** Returns the last node’s output as the HTTP response.

### 2. Extract Preferences (`Extract Preferences`)
- **Type:** Function
- **Purpose:** Parses and structures the input JSON to extract essential travel preferences:
  - `origin`: Starting location
  - `destination`: Travel destination
  - `travelDates`: Trip dates
  - `interests`: User interests or activities (optional)
  - `travelStyle`: Travel style, defaults to `'eco-friendly'` if not provided
  - `maxCarbonFootprint`: Optional maximum carbon footprint limit
- **Output:** Objects containing the extracted travel preferences.

### 3. Generate Itinerary with GPT-4 (`Generate Itinerary with GPT-4`)
- **Type:** OpenAI GPT-4 Node
- **Purpose:** Creates a detailed, personalized, sustainable travel itinerary based on the extracted preferences.
- **Configuration:**
  - AI Model: GPT-4
  - Temperature: 0.7 (balanced creativity)
  - Max Tokens: 600
- **Prompt Summary:** The prompt instructs GPT-4 to act as an expert eco-friendly travel planner, recommending sustainable transportation, accommodation, activities, local dining options, and providing carbon footprint estimates for each step.
- **Output:** JSON including:
  - `itinerary`: Array of daily itinerary steps with individual carbon footprint estimates.
  - `totalCarbonFootprintKgCO2`: Total estimated carbon emissions for the trip.

### 4. Parse GPT Output (`Parse GPT Output`)
- **Type:** Function
- **Purpose:** Parses the GPT-4 generated JSON string output into a usable JSON object for further processing.

### 5. Calculate Carbon Offsets (`Calculate Carbon Offsets`)
- **Type:** Function
- **Purpose:** Suggests carbon offsetting options based on the total carbon footprint calculated by GPT-4.
- **Logic:**
  - If the total carbon footprint is greater than zero, suggests:
    - Tree planting (offset 1 kg CO2 per kg estimated)
    - Purchasing carbon credits (offset equivalent to total carbon footprint)
- **Output:** Consolidated itinerary, total carbon footprint, and offset suggestions.

### 6. Respond with Plan (`Respond with Plan`)
- **Type:** Respond to Webhook
- **Purpose:** Returns the entire personalized travel plan including itinerary details and carbon offset suggestions as the final response to the HTTP request.

---

## Input JSON Format

Send a POST request to `/plan-travel` with a JSON body including:

```json
{
  "origin": "City A",
  "destination": "City B",
  "travelDates": {
    "start": "YYYY-MM-DD",
    "end": "YYYY-MM-DD"
  },
  "interests": ["hiking", "local cuisine"],                // Optional
  "travelStyle": "eco-friendly",                            // Optional, default is "eco-friendly"
  "maxCarbonFootprint": 150                                 // Optional in Kg CO2
}
```

---

## Output JSON Format

The response will be a JSON object with the following structure:

```json
{
  "itinerary": [
    {
      "day": 1,
      "activity": "Travel from City A to City B by electric train",
      "carbonFootprintKgCO2": "10.5"
    },
    {
      "day": 1,
      "activity": "Visit sustainable urban farm",
      "carbonFootprintKgCO2": "0.2"
    },
    ...
  ],
  "totalCarbonFootprintKgCO2": "50.7",
  "offsetSuggestions": [
    {
      "method": "Plant trees",
      "estimatedOffsetsKgCO2": 50.7
    },
    {
      "method": "Purchase carbon credits",
      "estimatedOffsetsKgCO2": 50.7
    }
  ]
}
```

---

## Extensibility Notes

- The **Calculate Carbon Offsets** node currently includes basic offset suggestions and can be enhanced to integrate with external carbon credit APIs or more refined offset calculations.
- The GPT prompt can be adjusted to incorporate additional personalized travel constraints or preferences as needed.

---

## Summary

This workflow automates the generation of sustainable, personalized travel itineraries with clear environmental impact transparency and actionable offsetting recommendations, empowering eco-conscious travelers with AI-driven planning.