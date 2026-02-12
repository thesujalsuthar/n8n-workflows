# AI-Powered Sustainable Packaging Inventory and Order Automation System

This workflow automates the forecasting, ordering, and notification process for sustainable packaging inventory using AI and integrations with supplier APIs.

---

## Workflow Overview

1. **Get Inventory Data**  
   Fetches current inventory data via an HTTP GET request from your inventory system API.

2. **AI Predict Inventory**  
   Sends the inventory data to the OpenAI GPT-4 model to predict inventory requirements for the next month, focusing on sustainable packaging materials. The output is a JSON containing items and their predicted quantities.

3. **Parse AI Prediction**  
   Parses the AI response JSON into structured data for further processing.

4. **Map to Supplier Orders**  
   Maps predicted inventory items to corresponding eco-friendly suppliers with their API order endpoints. Prepares the order payloads accordingly.

5. **Place Orders to Eco Suppliers**  
   Sends HTTP POST requests to suppliers' APIs to automatically place orders using the prepared payloads.

6. **Check Low Inventory**  
   Identifies items with predicted quantities considered low (less than 10 units) to flag possible inventory risks.

7. **Send Notification**  
   Sends email alerts to the supply chain team if any items are flagged as low inventory.

---

## Node Details

### Get Inventory Data
- **Type:** HTTP Request (GET)  
- **Purpose:** Retrieve current inventory data in JSON format from your inventory system API endpoint (`https://api.yourinventorysystem.com/inventory`).

### AI Predict Inventory
- **Type:** OpenAI GPT-4  
- **Prompt:**  
  Parses inventory data and predicts monthly inventory requirements for sustainable packaging materials. The prompt requests a JSON output containing:
  - `item`: Name of the item
  - `predicted_quantity`: Forecasted quantity needed for next month

- **Parameters:**  
  - `maxTokens`: 500  
  - `temperature`: 0.3 (to reduce randomness)

### Parse AI Prediction
- **Type:** Function  
- **Purpose:** Converts AI returned raw text into JSON objects for each predicted item with `item` and `predicted_quantity` fields.

### Map to Supplier Orders
- **Type:** Function  
- **Purpose:** Maps each predicted item to its eco-friendly supplier URL and formats an order payload.  
- **Suppliers included:**  
  - Recycled Cardboard  
  - Biodegradable Plastics  
  - Plant-based Inks

### Place Orders to Eco Suppliers
- **Type:** HTTP Request (POST)  
- **Purpose:** Sends orders to supplier APIs using their respective endpoints and order payloads.

### Check Low Inventory
- **Type:** Function  
- **Purpose:** Detects items with predicted quantities less than 10 and flags them for alerting.

### Send Notification
- **Type:** Email Send  
- **Purpose:** Sends a notification email to `supplychain@company.com` listing low inventory items.  
- **Email Subject:** Inventory Replenishment Notification

---

## Connections Flow

1. **Get Inventory Data** → **AI Predict Inventory**  
2. **AI Predict Inventory** → **Parse AI Prediction**  
3. **Parse AI Prediction** → **Map to Supplier Orders**  
4. **Parse AI Prediction** → **Check Low Inventory**  
5. **Map to Supplier Orders** → **Place Orders to Eco Suppliers**  
6. **Check Low Inventory** → **Send Notification**  

---

## Prerequisites

- Access to your inventory API with read permissions.
- OpenAI API key with GPT-4 access.
- Supplier API endpoints and authentication (if applicable).
- Email setup configured in n8n for sending notifications.

---

## Benefits

- Leverages AI to efficiently forecast sustainable packaging inventory needs.
- Automates supplier ordering process minimizing manual intervention.
- Provides proactive low inventory alerts to keep supply chain smooth.
- Focuses on eco-friendly, sustainable packaging suppliers aligning with green business goals.

---

## Notes

- Ensure supplier URLs and payload schemas match the actual supplier API specifications.
- Customize email recipient addresses as needed.
- Monitor AI output regularly and adjust prompt for optimal prediction accuracy.

---

This workflow can be imported and run inside [n8n](https://n8n.io/) automation platform.