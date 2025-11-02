# AI-Driven Sustainable Grocery Shopping Assistant

## Overview

This workflow provides users with sustainable and eco-friendly alternatives for their grocery shopping list. It leverages OpenAI’s GPT-4 API to suggest greener product options, estimates potential carbon footprint savings, and simulates local store availability and pricing for the alternatives.

---

## Workflow Nodes and Detailed Description

### 1. Start - Receive Shopping List

- **Type:** Set Node  
- **Purpose:** Takes the user’s grocery shopping list as input.  
- **Details:**  
  - Accepts a JSON array under the key `shoppingList`.  
  - Initially set to an empty array `[]`.  
  - This input is sent downstream to the AI for processing.

---

### 2. Get Sustainable Alternatives

- **Type:** HTTP Request  
- **Purpose:** Calls OpenAI’s GPT-4 API to get sustainable, eco-friendly alternatives for each grocery item.  
- **Parameters:**  
  - **HTTP Method:** POST  
  - **URL:** `https://api.openai.com/v1/chat/completions`  
  - **Authentication:** Bearer Token via header (`Authorization: Bearer <API_KEY>`)  
  - **Request Body:**  
    - `model`: `gpt-4`  
    - `messages`: Chat prompt consists of:  
      - *System role:* instructs the model to act as an assistant that suggests 1-2 eco-friendly alternatives per product with brief reasons.  
      - *User role:* contains the JSON stringified user shopping list.  
    - `temperature`: 0.7 (to balance creativity and reliability)  

- **Output:** Receives AI-generated suggestions either as structured JSON or a raw content text.

---

### 3. Parse Alternatives

- **Type:** Function  
- **Purpose:** Parses the AI response to extract the sustainable alternatives.  
- **Logic:**  
  - Attempts to parse the AI’s textual response as JSON.  
  - If parsing fails, returns the raw text under `alternativesRaw`.  

- **Output:** Parsed JSON alternatives for use in subsequent nodes.

---

### 4. Calculate Carbon Savings

- **Type:** Function  
- **Purpose:** Estimates the potential carbon footprint savings from switching to sustainable alternatives.  
- **Logic:**  
  - For demonstration, assigns a fixed estimated savings of 0.3 kg CO₂ per item replaced.  
  - Sums savings for all items in the input shopping list.  
  - Returns total estimated savings rounded to 2 decimals.  

- **Output:** `{ totalCarbonSavingsKgCO2: "<value>" }`

---

### 5. Fetch Store Availability & Pricing

- **Type:** Function  
- **Purpose:** Simulates checking availability and pricing of suggested alternatives at local stores.  
- **Logic:**  
  - Iterates over each alternative product.  
  - Assigns a random price between $1.00 and $6.00 USD.  
  - Randomly flags availability (80% chance available).  
  - Defaults to "Local Green Market" as the store.  
  - If no parsable alternatives, returns an empty product list.  

- **Output:** Array of store products with fields: `{ name, store, priceUSD, available }`

---

### 6. Compose Final Output

- **Type:** Function  
- **Purpose:** Combines all data into a user-friendly message and structured output.  
- **Output JSON Schema:**

```json
{
  "message": "Here are your sustainable alternatives with their availability and prices in local stores, plus your estimated carbon footprint savings of X kg CO2.",
  "alternatives": [...],        // Array of eco-friendly alternatives
  "storeProducts": [...],       // Availability and pricing info
  "carbonSavingsKgCO2": "X"    // Estimated carbon savings value
}
```

---

## Data Flow Summary

1. User inputs a shopping list in the **Start - Receive Shopping List** node.
2. The list is sent to the **Get Sustainable Alternatives** node, which queries GPT-4 to suggest greener product options.
3. AI response is parsed in **Parse Alternatives**.
4. **Calculate Carbon Savings** computes estimated CO₂ savings for the suggested alternatives.
5. **Fetch Store Availability & Pricing** simulates local store data for the alternatives.
6. Both calculations feed into **Compose Final Output**, which produces a consolidated message and data to present to the user.

---

## Usage Notes

- **API Key Required:** You must provide your OpenAI API Key in the credentials for the HTTP Request node.  
- **Input Format:** The shopping list must be an array of product names, for example:  
  ```json
  ["milk", "eggs", "bread"]
  ```  
- **Simulated Data:** Store availability and prices are randomly generated for demonstration purposes and should be connected to real store APIs for production use.  
- **Carbon Savings:** Calculations are illustrative with fixed/random values and can be replaced with precise lifecycle analysis data for real impact measurement.

---

## Summary

This workflow offers an interactive AI-powered assistant to help users shop more sustainably by suggesting eco-friendly grocery items, estimating environmental impact reduction, and simulating purchase logistics—all in one streamlined automation.