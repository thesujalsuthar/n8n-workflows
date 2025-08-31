# Sustainable Travel Planner Workflow

This workflow provides a simple automated process to fetch and analyze travel options based on sustainability criteria. It calculates carbon emissions for each travel mode, estimates the carbon offset cost, and outputs a detailed travel plan summary prioritizing eco-friendliness.

---

## Workflow Overview

The workflow consists of the following steps executed sequentially:

1. **Start**  
   Trigger node that initiates the workflow. Accepts input parameters about the travel query.

2. **Fetch Travel Options**  
   Fetches travel options based on provided origin, destination, travel dates, and user preferences. This uses a placeholder function simulating API responses returning modes like flight, train, and bus, each with associated CO2 emissions, duration, and cost.

3. **Sort by Sustainability**  
   Sorts the fetched travel options by ascending CO2 emissions, prioritizing more sustainable travel modes.

4. **Calculate Carbon Offset**  
   Selects the most sustainable option (lowest CO2 emissions) and calculates the carbon offset needed to neutralize emissions.

5. **Estimate Offset Cost**  
   Calculates an estimated cost to offset the carbon emissions using a fixed price per kilogram of CO2.

6. **Build Final Output**  
   Constructs a summarized output including travel mode, environmental impact, duration, cost, carbon offset required, and estimated offset cost.

---

## Input

The `Start` node expects a JSON input with:

```json
{
  "origin": "String",          // Starting location
  "destination": "String",     // Arrival location
  "travelDates": {             // Travel date range or single date
    "start": "YYYY-MM-DD",
    "end": "YYYY-MM-DD"
  },
  "preferences": {             // Optional user preferences for travel
    // Example: "maxCost": 400, "maxDurationHours": 8, etc.
  }
}
```

---

## Nodes Detailed Description

### 1. Start

- **Type:** Trigger node  
- **Function:** Initiates workflow with user input parameters.

---

### 2. Fetch Travel Options

- **Type:** Function node  
- **Function:** Simulates fetching travel options for the given query. Returns a static list of travel modes with associated CO2 emissions (kg), duration (hours), and cost (USD).  
- **Notes:** Replace the placeholder logic with actual API calls or database queries for live data.

---

### 3. Sort by Sustainability

- **Type:** Function node  
- **Function:** Sorts all travel options by their carbon dioxide emissions in ascending order with the goal to highlight the most sustainable options.

---

### 4. Calculate Carbon Offset

- **Type:** Function node  
- **Function:** For the top sustainable option, calculates the total carbon offset needed to achieve carbon neutrality based on the CO2 emissions.

---

### 5. Estimate Offset Cost

- **Type:** Function node  
- **Function:** Uses a fixed carbon offset price ($0.01 per kg CO2) to estimate the total cost required to offset the emissions of the selected travel option.

---

### 6. Build Final Output

- **Type:** Function node  
- **Function:** Builds a clean, summarized JSON output containing:  
  - Travel mode  
  - CO2 emissions (kg)  
  - Duration (hours)  
  - Travel cost (USD)  
  - Carbon offset required (kg)  
  - Estimated carbon offset cost (USD)  

---

## Output Example

```json
{
  "travelMode": "bus",
  "co2EmissionKg": 15,
  "durationHours": 8,
  "costUSD": 80,
  "carbonOffsetNeededKg": 15,
  "estimatedOffsetCostUSD": 0.15
}
```

---

## How to Use This Workflow

1. **Trigger the workflow** by sending travel input data to the `Start` node.
2. **Fetch Travel Options** will simulate available modes with environmental and cost details.
3. **Sort by Sustainability** prioritizes eco-friendly choices.
4. **Calculate Carbon Offset** determines how much offset is needed.
5. **Estimate Offset Cost** shows expected monetary offset cost based on emissions.
6. **Build Final Output** returns a concise travel plan summary emphasizing sustainability.

---

## Notes

- The travel options fetching uses dummy data. Integrate with real APIs to make this workflow practical.
- The offset cost calculation uses a simple fixed price and can be enhanced with dynamic pricing.
- Preferences filtering can be added to refine travel options based on user needs.

---

This workflow serves as a foundational framework for integrating sustainability considerations into travel planning automation. Customize and extend it according to your data sources and business requirements.