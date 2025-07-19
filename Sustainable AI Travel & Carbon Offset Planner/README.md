# AI-Powered Personalized Sustainable Travel Planner and Carbon Offset Tracker

## Overview
This workflow provides users with personalized, eco-friendly travel itineraries alongside detailed carbon footprint estimations and tailored carbon offset recommendations. Leveraging AI, it helps plan sustainable trips while promoting environmental responsibility.

---

## Workflow Summary

### 1. HTTP Request Trigger (`planTrip`)
- **Type:** POST endpoint
- **Purpose:** Starts the workflow upon receiving user travel preferences in the request body.
- **Endpoint:** `/planTrip`
- **Response:** Sent after workflow completion, containing the full travel plan, carbon footprint summary, and offset options.

### 2. AI Travel Planner (OpenAI GPT-4 Chat Completion)
- **Role:** AI assistant specialized in sustainable travel planning.
- **Input:** User travel preferences (passed dynamically via `{{ $json.preferences }}`).
- **Output:** A detailed travel itinerary recommending:
  - Sustainable travel options
  - Eco-friendly accommodations
  - Low-impact activities
  - Estimated carbon footprint per travel segment
- **Prompt:** Instructs GPT-4 to focus on eco-friendliness and include carbon footprint data for each trip segment.

### 3. Parse & Calculate Carbon Footprint (Function Node)
- **Role:** Parses the AI-generated travel plan from JSON string.
- **Calculation:** Sums up carbon emissions (in kilograms) from each segment to derive total carbon footprint.
- **Output:** An object containing:
  - Parsed travel plan structure
  - Total estimated carbon footprint (`totalCarbon`)

### 4. Carbon Offset Recommender (OpenAI GPT-4 Chat Completion)
- **Role:** AI assistant that suggests impactful carbon offset projects aligned with the user’s total carbon footprint.
- **Input:** Total carbon footprint from previous step (`{{ $json.totalCarbon }}`).
- **Output:** JSON with:
  - Recommended carbon offset options
  - Project details
  - Estimated costs

### 5. Format Final Output (Function Node)
- **Purpose:** Aggregates and formats the final JSON response.
- **Includes:**
  - Complete travel itinerary (`itinerary`)
  - Total carbon footprint in kilograms (`totalCarbonKg`)
  - Detailed carbon offset options (`carbonOffsetOptions`)

### 6. HTTP Response
- **Role:** Returns the complete travel planning and carbon offset data back to the requester as the API response.

---

## Input & Output

### Input JSON Format:
```json
{
  "preferences": {
    "destination": "string",
    "travelDates": "date range",
    "activities": ["string"],
    "accommodationType": "string",
    "transportPreferences": ["string"],
    // other relevant preferences
  }
}
```

### Output JSON Example:
```json
{
  "summary": {
    "itinerary": {
      // detailed sustainable travel plan with segments
    },
    "totalCarbonKg": 123.45,
    "carbonOffsetOptions": {
      // recommended carbon offset projects and costs
    }
  }
}
```

---

## How It Works

1. User sends a POST request with travel preferences to `/planTrip`.
2. AI Travel Planner generates an eco-friendly itinerary with carbon footprint estimations.
3. Carbon footprint is parsed and totaled.
4. AI recommends carbon offset projects tailored to the user’s carbon footprint.
5. Final response aggregated and sent back as JSON.

---

## Requirements

- n8n workflow automation environment.
- OpenAI GPT-4 API access configured in n8n.
- HTTP client for making POST requests to the `/planTrip` endpoint.

---

## Notes

- The workflow uses dynamic expressions (`{{ }}`) to pass data between nodes.
- Proper JSON formatting is expected in AI responses for parsing.
- Carbon footprint values are represented in kilograms of CO₂.

---

## Usage

Send a POST request to the `planTrip` endpoint with your travel preferences to receive a personalized sustainable travel plan and carbon offset tracking options.