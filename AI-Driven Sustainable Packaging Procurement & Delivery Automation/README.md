# AI-Powered Sustainable Packaging Recommendation & Order Automation

This workflow automates the process of analyzing packaging requirements, recommending sustainable packaging options using AI, placing orders, and managing delivery notifications. It integrates AI capabilities with external APIs for packaging data and order management, streamlining supply chain operations.

---

## Workflow Overview

1. **Receive Packaging Requirements**  
   An HTTP POST endpoint `/analyze-packaging` triggers the workflow by receiving packaging requirements in JSON format.

2. **Prepare AI Prompt**  
   Converts the received packaging requirements into a prompt designed to request three sustainable packaging options, including material, size, cost estimate, and sustainability score.

3. **AI: Packaging Recommendation**  
   Sends the prompt to OpenAI's GPT-4 model for generating sustainable packaging recommendations.

4. **Parse AI Response**  
   Parses the JSON response from the AI into individual recommendation objects.

5. **Query Packaging Database**  
   For each recommendation, queries an external sustainable packaging database API to fetch detailed packaging option data based on material and size. Authenticated via API credentials.

6. **Prepare Order Details**  
   Constructs order details for each fetched packaging option, including supplier ID, packaging ID, a predefined quantity (100 units), and the delivery address (Company Warehouse).

7. **Place Order**  
   Sends the order details as a POST request to the order management system API to place the packaging orders. Uses header-based authentication.

8. **Generate Confirmation Email Content**  
   Formats the order confirmation response into a readable email message with order ID, material, quantity, and expected delivery date.

9. **Send Order Confirmation Notification**  
   Sends the order confirmation email to the supply chain team (`supplychain@company.com`) from `orders@company.com` with relevant order details.

10. **Wait for Delivery Update Interval**  
    Pauses the workflow to wait for a set interval before checking delivery status updates.

11. **Get Delivery Tracking**  
    Retrieves the latest delivery tracking information for each order from the order management system API.

12. **Prepare Delivery Notification**  
    Formats the tracking information into a notification message including order ID, current status, and estimated delivery date.

13. **Send Delivery Tracking Notification**  
    Sends an email notification with updated delivery tracking information to the supply chain team.

---

## Detailed Node Descriptions

### 1. Receive Packaging Requirements (HTTP Trigger)  
- **Type:** HTTP Trigger (POST)  
- **Endpoint:** `/analyze-packaging`  
- **Input:** JSON object with packaging requirements  
- **Output:** Forwarded JSON payload for AI prompt creation

### 2. Prepare AI Prompt (Function)  
- Generates a natural language prompt describing packaging requirements, requesting 3 sustainable packaging options with required fields.

### 3. AI: Packaging Recommendation (OpenAI)  
- Uses the GPT-4 model to generate packaging recommendations based on the prompt.  
- Configured to return up to 500 tokens.

### 4. Parse AI Response (Function)  
- Parses the AI's JSON string response into separate JSON recommendation objects.  
- Handles parsing errors and throws exceptions if parsing fails.

### 5. Query Packaging Database (HTTP Request)  
- Sends POST request with material and size to an authenticated external API:  
  `https://api.sustainable-packaging-db.com/v1/options/search`  
- Retrieves detailed packaging options available for order.

### 6. Prepare Order Details (Function)  
- Prepares order payloads for each packaging option, including:  
  - Supplier ID  
  - Packaging ID  
  - Quantity (fixed at 100)  
  - Delivery address (Company Warehouse details)

### 7. Place Order (HTTP Request)  
- Sends order data to Order Management API (authenticated via header) at:  
  `https://api.ordersystem.com/v2/orders`  
- Initiates the purchase process.

### 8. Generate Confirmation Email Content (Function)  
- Creates readable email content summarizing the order confirmation details.

### 9. Send Order Confirmation Notification (Email Send)  
- Sends order confirmation email from `orders@company.com` to `supplychain@company.com` listing order details.

### 10. Wait for Delivery Update Interval (Wait)  
- Delays subsequent execution to allow time for delivery updates.

### 11. Get Delivery Tracking (HTTP Request)  
- Retrieves current delivery tracking information for the specific order from:  
  `https://api.ordersystem.com/v2/orders/{orderId}/tracking`

### 12. Prepare Delivery Notification (Function)  
- Formats the tracking data into a message, including current status and estimated delivery.

### 13. Send Delivery Tracking Notification (Email Send)  
- Emails delivery tracking updates to the supply chain team.

---

## Credentials Required

- **`packagingApiCredentialId`**: API credentials (header authentication) for the Sustainable Packaging Database API.  
- **`orderManagementApiCredentialId`**: API credentials (header authentication) for the Order Management System API.

---

## Usage

1. Deploy this workflow in your n8n environment.  
2. Configure the HTTP Trigger endpoint `/analyze-packaging`.  
3. Set up and provide necessary API credentials in n8n for the packaging database and order management APIs.  
4. Customize the delivery address and order quantity in the "Prepare Order Details" node as needed.  
5. Trigger the workflow by sending packaging requirements as JSON via HTTP POST to the trigger endpoint.  
6. Monitor orders and delivery updates through email notifications.

---

## Input Example

POST to `/analyze-packaging` with JSON body, e.g.:

```json
{
  "productType": "Electronics",
  "weight": 2.5,
  "dimensions": {
    "length": 30,
    "width": 20,
    "height": 10
  },
  "specialRequirements": ["biodegradable", "shock resistant"]
}
```

---

## Output

- Sends order confirmation emails with detailed information.  
- Periodically sends delivery tracking email updates until delivery is complete.

---

## Notes

- The workflow relies on the AI model for generating initial packaging recommendations, which are verified and supplemented by querying the packaging database.  
- Error handling is implemented for parsing AI responses.  
- Delivery updates run in a loop governed by the wait node to periodically check and notify.

---

# License

This workflow is provided as-is without warranty and may require customization for specific use cases.