# Carbon Footprint Calculator Workflow

This workflow calculates a user's daily carbon footprint based on their activity data, generates personalized recommendations for reducing emissions, and sends the results via Telegram.

---

## Workflow Overview

The workflow consists of four main steps:

1. **Receive Activity Data** (Webhook)
2. **Calculate Carbon Footprint** (Function Node)
3. **Generate Recommendations** (Function Node)
4. **Send Results to User** (Telegram Node)

---

## Nodes & Details

### 1. Webhook - Receive Activity Data

- **Type:** Webhook (HTTP POST)
- **Path:** `/submit-activity`
- **Purpose:** Receives activity data submitted by users including transportation, energy usage, and waste generation.
- **Expected input JSON format example:**

```json
{
  "chat_id": "123456789",
  "transportation": {
    "car_km": 15,
    "bus_km": 5,
    "train_km": 0,
    "bike_km": 2,
    "walk_km": 1
  },
  "energy": {
    "electricity_kwh": 10,
    "natural_gas_m3": 2
  },
  "waste": {
    "kg_waste": 3
  }
}
```

---

### 2. Calculate Carbon Footprint

- **Type:** Function
- **Function Logic:**
  - Defines emission factors (in kg CO2 per unit) for transportation, energy, and waste.
  - Calculates emissions per category using provided data:
    - Transportation modes: car, bus, train, bike, walk
    - Energy consumption: electricity (kWh), natural gas (m³)
    - Waste produced in kg
  - Computes total emissions by summing category emissions.
  - Outputs:
    - `totalEmissions`: Total kg CO2 emitted (rounded to 3 decimals)
    - `breakdown`: Emission breakdown by category (transportation, energy, waste)
    - `inputData`: Original parsed input data

---

### 3. Generate Recommendations

- **Type:** Function
- **Function Logic:**
  - Analyzes emission breakdown values.
  - Generates personalized recommendations:
    - **Transportation > 1 kg CO2:** Suggests reducing car travel and using public transport, biking, or walking.
    - Otherwise: Encourages maintaining low transportation emissions.
    - **Energy > 1 kg CO2:** Advises reducing electricity usage and considering renewable energy.
    - Otherwise: Encourages maintaining efficient energy use.
    - **Waste > 0.5 kg CO2:** Recommends recycling, composting, and reducing waste.
    - Otherwise: Praise for minimal waste.
  - Outputs a list of textual recommendations.

---

### 4. Send Results to User

- **Type:** Telegram node
- **Functionality:**
  - Sends a Telegram message using the chat ID from the input data.
  - Message content includes:
    - Total carbon footprint (kg CO2)
    - Emission breakdown (transportation, energy, waste)
    - Numbered personalized recommendations
- **Telegram API Credentials:** Required and configured under `Telegram API`.

**Message Example:**

```
Your daily carbon footprint is 3.456 kg CO2.
Breakdown:
- Transportation: 1.234 kg CO2
- Energy: 2.000 kg CO2
- Waste: 0.222 kg CO2

Recommendations:
1. Consider reducing car travel and use public transportation, biking, or walking more frequently.
2. Reduce electricity usage by turning off unused devices and consider switching to renewable energy providers.
3. Waste production is minimal; great job!
```

---

## Running the Workflow

1. Deploy the workflow in your n8n environment.
2. Configure the Telegram API credentials.
3. Users submit their daily activity data as a POST request to the webhook URL:
    ```
    https://<your-n8n-domain>/webhook/submit-activity
    ```
4. The workflow will automatically calculate emissions, generate recommendations, and send a Telegram message to the user.

---

## Emission Factors Used

| Category       | Unit            | Emission Factor (kg CO2/unit) |
|----------------|-----------------|-------------------------------|
| Car            | Kilometer       | 0.21                          |
| Bus            | Kilometer       | 0.05                          |
| Train          | Kilometer       | 0.04                          |
| Bike           | Kilometer       | 0                             |
| Walk           | Kilometer       | 0                             |
| Electricity    | kWh             | 0.233                         |
| Natural Gas    | Cubic Meter     | 2.02                          |
| Waste          | Kilogram Waste  | 0.45                          |

---

## Notes

- Emission factors are approximations and may vary by region.
- The workflow assumes input data is properly formatted.
- Recommendations thresholds are set for simplicity and can be adjusted in the Generate Recommendations function.