# EcoTravel AI: Sustainable Itinerary & Carbon Offset Workflow

This workflow facilitates sustainable travel planning by recommending eco-friendly travel destinations and calculating the carbon footprint of a user’s trip based on travel mode and distance. It also estimates the cost to offset the carbon emissions and allows the user to purchase carbon offsets directly through an API.

---

## Workflow Overview

The workflow consists of six main nodes:

### 1. User Input Travel Mode & Distance
- **Type:** Manual Trigger
- **Description:** Starting point where the user inputs their travel mode (e.g., plane, train, bus, car, bicycle) and travel distance in kilometers.
- **Data Required:**
  - `travelMode` (string): Mode of travel chosen by the user.
  - `distanceKm` (number): Distance of the trip in kilometers.

---

### 2. Destination Recommendation
- **Type:** Function Node
- **Description:** Provides a list of predefined travel destinations with high sustainability ratings.
- **Data:**
  - Contains an internal array of destinations with city, country, and sustainability rating.
  - Filters destinations with a **sustainability rating of 8 or higher**.
- **Destinations Included:**
  - Amsterdam, Netherlands (9)
  - Copenhagen, Denmark (8.5)
  - Reykjavik, Iceland (9.2)
  - Vancouver, Canada (8)
  - Kyoto, Japan (7.8) *excluded due to rating <8*
  - San Francisco, USA (8.1)

*Note:* This node runs independently and does not affect the downstream carbon calculations directly in this workflow configuration.

---

### 3. Calculate Carbon Footprint
- **Type:** Function Node
- **Description:** Calculates the estimated carbon footprint (in kg CO2) based on the user’s travel mode and travel distance.
- **Emission Factors (kg CO2 per km):**
  - Plane: 0.255
  - Train: 0.041
  - Bus: 0.068
  - Car: 0.192
  - Bicycle: 0 (zero emissions)
- **Validation:** Throws an error if an unsupported travel mode is provided.
- **Output:**
  - `travelMode`
  - `distanceKm`
  - `carbonFootprintKgCO2`

---

### 4. Calculate Offset Cost
- **Type:** Function Node
- **Description:** Calculates the cost to offset the total carbon footprint.
- **Assumptions:**
  - 1 carbon credit = 1 kg CO2 offset
  - Price per credit: $10 USD
- **Output:**
  - `carbonOffsetKgCO2` (equal to the calculated carbon footprint)
  - `pricePerCreditUSD` (fixed at $10)
  - `totalCostUSD` (carbon footprint × price per credit)

---

### 5. Purchase Carbon Offset
- **Type:** HTTP Request Node
- **Description:** Sends a POST request to an external carbon offset API to purchase the required carbon credits.
- **API Endpoint:** `https://api.examplecarbonoffset.com/purchase`
- **Payload Structure:**
  ```json
  {
    "userId": "<User ID or 'demoUser'>",
    "offsetKg": "<carbonOffsetKgCO2>",
    "payment": {
      "amount": "<totalCostUSD>",
      "currency": "USD",
      "method": "<paymentMethod (default: credit_card)>"
    },
    "itinerary": "<optional itinerary details>"
  }
  ```
- **Notes:**
  - `userId`, `paymentMethod`, and `itinerary` are optionally supplied in the JSON input.
  - Payment method defaults to `"credit_card"` if not provided.

---

### 6. Confirm Purchase
- **Type:** Function Node
- **Description:** Confirms successful purchase of carbon offsets and returns a success message along with purchase details from the API response.
- **Output:**
  - `message`: Success confirmation string.
  - `details`: JSON response from the carbon offset purchase API.

---

## Connections Flow

1. **User Input Travel Mode & Distance**
   - Triggers **Calculate Carbon Footprint** node.
2. **Calculate Carbon Footprint**
   - Outputs to **Calculate Offset Cost**.
3. **Calculate Offset Cost**
   - Feeds into **Purchase Carbon Offset** node.
4. **Purchase Carbon Offset**
   - Outputs to **Confirm Purchase**.
5. **Confirm Purchase**
   - Final confirmation output.

The **Destination Recommendation** node runs separately and is not connected in this flow but serves as a standalone function to provide sustainable travel options.

---

## How to Use This Workflow

1. **Provide User Inputs:** Manually trigger the workflow and enter `travelMode` and `distanceKm` to start the calculation.
2. **View Sustainable Destinations:** Check the output of the Destination Recommendation node for eco-friendly destination ideas.
3. **Calculate Emissions:** The workflow will calculate the carbon emissions of the trip automatically.
4. **Compute Offset Cost:** It calculates how much it would cost to offset these emissions.
5. **Purchase Offsets:** Sends a request to purchase carbon credits via the third-party API.
6. **Confirmation:** Displays a confirmation message along with details of the purchase.

---

## Supported Travel Modes

- Plane
- Train
- Bus
- Car
- Bicycle

*Note:* Other travel modes will cause the workflow to throw an error.

---

## Assumptions

- Carbon credit price is fixed at $10 per kg CO2.
- One carbon credit offsets exactly one kg of CO2.
- The external carbon offset purchase API endpoint and schema are placeholders and should be replaced with actual API details.
- User identification and payment methods default to demo values unless specified.

---

## Summary

This workflow supports sustainable travel decisions by combining destination recommendations, carbon footprint estimation, and an integrated carbon offset purchasing process to promote climate-conscious travel choices.