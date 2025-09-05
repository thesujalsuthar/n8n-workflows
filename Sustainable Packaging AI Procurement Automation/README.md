# AI-Powered Sustainable Packaging Recommendation and Procurement Automation

## Overview

This workflow automates the process of recommending sustainable packaging solutions and managing procurement orders using AI and API integrations. It accepts product packaging requirements, leverages an AI model to generate eco-friendly packaging recommendations, automates procurement requests to suppliers, calculates cost-benefit and sustainability metrics, and sends a summary report via email.

---

## Workflow Components

### 1. Webhook - Receive Product Data

- **Type:** Webhook (HTTP POST)
- **Endpoint Path:** `/analyze-product-requirements`
- **Purpose:** Receives incoming product data including weight, dimensions, fragility, and volume.
- **Input:** JSON payload with product packaging details.
- **Output:** Passes product data downstream for extraction.

---

### 2. Extract Packaging Requirements

- **Type:** Function
- **Purpose:** Extracts relevant packaging attributes from the incoming product data.
- **Operation:** Parses the product JSON and extracts:
  - Weight
  - Dimensions
  - Fragility
  - Volume
- **Output:** JSON object with extracted `packagingRequirements`.

---

### 3. Generate Packaging Recommendations

- **Type:** OpenAI Node (GPT-4)
- **Purpose:** Uses AI to provide recommended sustainable packaging options.
- **Prompt Details:**
  - Specializes in sustainable packaging solutions.
  - Inputs the packaging requirements.
  - Requests a ranked list of packaging options based on:
    - Biodegradability
    - Recyclability
    - Carbon footprint
  - Includes estimated costs and supplier IDs.
- **Parameters:**
  - Model: `gpt-4`
  - Temperature: 0.3
  - Max tokens: 500
- **Output:** AI-generated JSON string containing packaging recommendations.

---

### 4. Parse Recommendations

- **Type:** Function
- **Purpose:** Parses the raw JSON string response from the AI node into structured JSON objects.
- **Operation:** 
  - Attempts to parse AI response.
  - Throws an error if parsing fails.
- **Output:** Array of recommendation objects.

---

### 5. Prepare Procurement Requests

- **Type:** Function
- **Purpose:** Prepares procurement orders for suppliers based on recommendations.
- **Operation:** For each recommendation, creates a procurement request JSON:
  - `supplierId`
  - `packagingOptionId`
  - `quantity` (defaulted to 100)
- **Output:** List of procurement request JSON objects.

---

### 6. Create Procurement Order

- **Type:** HTTP Request
- **Purpose:** Sends procurement orders to the supplier API endpoint.
- **Endpoint:** `https://api.supplier.com/procurements`
- **Method:** POST
- **Authentication:** Header Authentication (credentials stored securely)
- **Body Parameters:**
  - `supplierId`
  - `packagingOptionId`
  - `quantity`
- **Output:** Supplier API response for each order.

---

### 7. Calculate Cost-Benefit & Sustainability Impact

- **Type:** Function
- **Purpose:** Analyzes the total cost and sustainability impact of selected packaging options.
- **Operation:**
  - Calculates total estimated cost (sum of cost × quantity).
  - Computes average sustainability score.
  - Aggregates all recommendation details.
- **Output:** JSON summary with `totalCost`, `sustainabilityScore`, and `recommendations`.

---

### 8. Generate Sustainability Report

- **Type:** Set Node
- **Purpose:** Generates a textual summary report of procurement outcomes and sustainability scores.
- **Content:**
  ```
  Packaging Procurement Summary:

  Total Estimated Cost: $<totalCost>
  Average Sustainability Score: <sustainabilityScore>/10

  Details per packaging option:
  - Option Name: Cost $<estimatedCost>, Sustainability <sustainabilityScore>
  ```
- **Output:** Text report stored in JSON for email body.

---

### 9. Send Report Email

- **Type:** Email Send
- **Purpose:** Sends the sustainability procurement report to the business team.
- **Channel:** Email
- **Recipient:** `business@company.com`
- **Subject:** Sustainable Packaging Procurement Report
- **Body:** Dynamic text generated from the previous node.
- **Authentication:** SMTP credentials (secured).

---

## Data Flow Summary

1. **Receive** raw product data via webhook.
2. **Extract** packaging requirements from the product data.
3. **Generate** sustainable packaging recommendations using AI.
4. **Parse** AI-generated recommendations.
5. **Prepare** procurement requests for suppliers.
6. **Send** procurement orders via API calls.
7. **Calculate** overall cost and sustainability impact.
8. **Generate** and **send** a detailed email report.

---

## Prerequisites & Credentials

- **OpenAI API access** with GPT-4 capability.
- **Supplier API credentials** for procurement order API (HTTP Header Authentication).
- **SMTP credentials** for sending emails.
- Ensure all credentials are configured securely in n8n.

---

## Usage

- Deploy the workflow in n8n.
- Configure webhook URL and share with product data sources.
- Ensure all API keys and credentials are correctly set.
- Upon product data submission, the workflow executes end-to-end automatically.
- Monitor email reports for procurement insights.

---

## Notes

- Quantity for procurement orders is defaulted to 100 units; adjust in the `Prepare Procurement Requests` node as needed.
- AI recommendations rely on accurate packaging requirements; ensure input data integrity.
- Error handling is present for AI recommendation parsing; monitor workflow executions for failures.

---

## Contact

For questions or customization requests, please contact the workflow maintainer or development team.