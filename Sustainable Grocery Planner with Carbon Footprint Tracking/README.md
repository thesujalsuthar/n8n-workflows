# Sustainable Grocery Assistant with Carbon Footprint Insights

This workflow helps users plan their grocery shopping by providing detailed carbon footprint insights and sustainability scores for grocery products. It also suggests sustainable alternatives and tracks the user's carbon footprint over time.

---

## Workflow Overview

The workflow is triggered via a webhook and follows these steps:

1. **Receive Grocery Product Data:**  
   The webhook accepts HTTP POST requests containing grocery product information such as product name and quantity.

2. **Lookup Carbon Footprint Data:**  
   For the given product, the workflow queries an external Carbon Footprint API to retrieve carbon emission data.

3. **Evaluate Product Footprint:**  
   Using the carbon footprint data per unit, the workflow calculates the total carbon footprint based on quantity and assigns a sustainability score.

4. **Suggest Sustainable Alternatives:**  
   Provides mock suggestions of alternative products (e.g., organic or local versions) with lower carbon footprints.

5. **Track Footprint Over Time:**  
   Stores the user's daily total carbon footprint in a MongoDB Atlas database for historical tracking.

6. **Response Formatting:**  
   Returns a clear structured response summarizing the product’s carbon footprint, sustainability score, and suggested alternatives.

---

## Nodes Description

### 1. Webhook - Plan Groceries  
- **Type:** Webhook  
- **HTTP Method:** POST  
- **Endpoint Path:** `/groceries/plan`  
- **Function:** Triggers the workflow and accepts input JSON containing grocery product details.

### 2. Carbon Footprint Lookup  
- **Type:** HTTP Request  
- **Operation:** Lookup product carbon footprint data via an authenticated external API.  
- **Input:** `productName` from webhook payload.  
- **Authentication:** HTTP Basic Auth using `CarbonFootprintAPI` credentials.

### 3. Evaluate Product Footprint  
- **Type:** Function  
- **Functionality:**  
  - Extracts the carbon footprint per product unit from API data.  
  - Uses a default value of 1.0 kg CO2e if data is missing.  
  - Calculates total footprint by multiplying footprint per unit with quantity.  
  - Assigns a sustainability score:  
    - High: footprint < 1 kg CO2e/unit  
    - Medium: footprint between 1 and 3 kg CO2e/unit  
    - Low: footprint > 3 kg CO2e/unit

### 4. Suggest Alternatives  
- **Type:** Function  
- **Functionality:**  
  - Generates mock sustainable alternatives such as "Organic" or "Local" versions of the product with reduced carbon footprints (40-50% lower).  
  - Assigns these alternatives a "High" sustainability score.

### 5. Track Footprint Over Time  
- **Type:** MongoDB  
- **Functionality:**  
  - Upserts the user's daily total carbon footprint into MongoDB.  
  - Uses collection: `grocery_footprint`  
  - Upsert Key: `userId` (defaults to 'anonymous' if none provided)  
  - Stores the date and footprint total for record keeping.

### 6. Format Response  
- **Type:** Function  
- **Functionality:**  
  - Prepares the final response payload with the relevant product data, footprint metrics, sustainability score, and alternatives.  
  - Returns this data as the webhook response.

---

## Expected Input Format

Send a POST request to the webhook URL `/groceries/plan` with JSON data including:

```json
{
  "productName": "string",
  "quantity": number,
  "userId": "string (optional)"
}
```

- `productName`: Name of the grocery product to evaluate.  
- `quantity`: Amount of the product (default is 1 if omitted).  
- `userId`: Identifier for tracking user footprint data (optional).

---

## Sample Response

```json
{
  "message": "Grocery product processed",
  "product": {
    "name": "Apple",
    "quantity": 3,
    "carbonFootprintPerUnit": 0.8,
    "totalCarbonFootprint": 2.4,
    "sustainabilityScore": "High",
    "sustainableAlternatives": [
      {
        "name": "Organic Apple",
        "carbonFootprintPerUnit": 1.44,
        "sustainabilityScore": "High"
      },
      {
        "name": "Local Apple",
        "carbonFootprintPerUnit": 1.2,
        "sustainabilityScore": "High"
      }
    ]
  }
}
```

---

## Prerequisites

- **CarbonFootprintAPI Credentials:** Set up HTTP Basic Authentication credentials named `CarbonFootprintAPI` with appropriate API access.  
- **MongoDB Atlas Connection:** Configure and provide credentials for MongoDB Atlas connection named `MongoDB Atlas` with access to the `grocery_footprint` collection.

---

## Notes

- The workflow uses a default footprint value if no data is found for a product.  
- Alternatives are mock suggestions generated within the workflow and not fetched from an external source.  
- Footprint tracking stores one entry per user per day to monitor sustainability progress over time.

---

## Activation

Activate this workflow in your n8n instance to start processing grocery carbon footprint requests.