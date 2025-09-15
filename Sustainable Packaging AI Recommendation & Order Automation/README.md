# AI-Powered Sustainable Packaging Recommendation System

This workflow automates the process of generating eco-friendly packaging recommendations for products using AI, and facilitates follow-up actions such as emailing the recommendation report to the client and placing material orders with suppliers.

---

## Workflow Overview

The workflow receives product information through a webhook, sends a custom prompt to an AI model (GPT-4) to generate sustainable packaging recommendations, processes the AI response, and executes two parallel actions:

1. Email a detailed recommendation report to the specified business email.
2. Prepare and place a material order with a packaging supplier via an API.

---

## Nodes Description

### 1. **Webhook Receive Product Info**
- **Type:** Webhook (POST)
- **Path:** `/submit-product-info`
- **Purpose:** Accepts incoming POST requests containing product details such as product type, industry, sustainability goals, and business email address.

### 2. **Prepare AI Prompt**
- **Type:** Function
- **Purpose:** Constructs a tailored prompt for the AI model, incorporating product information from the webhook.
- **Output:** JSON object containing the prompt requesting:
  - Recommended eco-friendly packaging materials
  - Design suggestions
  - Compliance notes formatted as JSON with keys: `materials` (array), `designSuggestions` (string), and `complianceNotes` (string).

### 3. **AI Packaging Recommendation**
- **Type:** OpenAI Node (GPT-4)
- **Parameters:**
  - Model: `gpt-4`
  - Prompt: Provided dynamically from the previous node
  - Max Tokens: 500
  - Temperature: 0.7
- **Purpose:** Generates detailed sustainable packaging recommendations based on the prompt.

### 4. **Parse AI Response**
- **Type:** Function
- **Purpose:** Parses the AI response content from a JSON string into a usable JSON object.
- **Error Handling:** Throws an error if the response cannot be parsed, ensuring data integrity.

### 5. **Format Report Text**
- **Type:** Set
- **Purpose:** Creates a human-readable text report combining:
  - Recommended materials (formatted JSON)
  - Design suggestions
  - Compliance notes
- **Output:** Multi-line string used for email content.

### 6. **Send Report Email**
- **Type:** Email Send
- **Parameters:**
  - From: `no-reply@sustainablepackaging.ai`
  - To: Business email extracted from the initial webhook data
  - Subject: `Eco-Friendly Packaging Recommendation Report`
  - Body: Formatted report text
- **Purpose:** Sends the recommendation report to the client.

### 7. **Prepare Supplier Order Payload**
- **Type:** Function (Parallel branch)
- **Purpose:** Prepares the order payload for supplier API, transforming material recommendations into an appropriate format with default quantities.
- **Output:** JSON object containing product type, industry, and materials for ordering.

### 8. **Place Material Order**
- **Type:** HTTP Request (POST)
- **URL:** `https://api.supplier.example.com/orders`
- **Authentication:** Header Authentication (`SupplierApiHeaderAuth`)
- **Purpose:** Sends the order payload to the supplier's API to initiate material purchase based on AI recommendations.

---

## Data Flow Summary

1. **Input:** POST product information to `/submit-product-info` webhook.
2. **Processing:**
   - Generate AI prompt → Get AI recommendation → Parse AI response.
3. **Branch Actions:**
   - Format and send report email.
   - Prepare order and place material order.
   
---

## Input Payload Example

```json
{
  "productType": "Organic skincare jar",
  "industry": "Cosmetics",
  "sustainabilityGoals": "Biodegradable, minimal plastic usage",
  "businessEmail": "client@example.com"
}
```

---

## Expected AI JSON Response Format

```json
{
  "materials": [
    {"name": "Recycled glass", "quantity": 200},
    "Bamboo fiber"
  ],
  "designSuggestions": "Use minimalistic labeling with soy-based inks and ensure the packaging is reusable.",
  "complianceNotes": "Meets ISO 14021 environmental claims standards for biodegradable materials."
}
```

---

## Prerequisites & Setup

- **n8n** environment with the following credentials:
  - OpenAI API access configured for GPT-4
  - Email sending credentials for the `Send Report Email` node
  - Supplier API HTTP header authentication named `SupplierApiHeaderAuth`
- Endpoint exposed publicly (or accessible internally) to accept incoming product info POST requests.

---

## Notes

- Ensure the AI response complies with the JSON format; otherwise, the workflow will halt at the parsing step.
- Modify supplier API details and authentication credentials to match actual integration requirements.
- Customize the email sender address and email server configurations as needed.