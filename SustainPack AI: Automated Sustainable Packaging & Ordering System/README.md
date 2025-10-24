# AI-Powered Sustainable Packaging Recommendation and Order Automation

This workflow automates the recommendation of sustainable packaging options, forecasts packaging needs based on sales data, optimizes packaging inventory, selects vendors for restocking orders, places orders with suppliers, and sends notifications about inventory status using AI and integration APIs.

---

## Workflow Overview

1. **Fetch Product Details**  
   Retrieves product information from the company API.

2. **AI Packaging Recommendation**  
   Uses OpenAI GPT-4 to generate sustainable packaging recommendations based on product details such as name, dimensions, weight, and category.

3. **Fetch Sales Data**  
   Retrieves historical sales data for the last six months to analyze sales trends.

4. **Forecast Packaging Needs**  
   Processes sales data to forecast packaging requirements for the next month, assuming 10% growth.

5. **Fetch Packaging Inventory**  
   Retrieves current packaging inventory status.

6. **Optimize Inventory & Identify Restock Needs**  
   Compares forecasted packaging needs with current inventory and identifies packaging types and quantities that require restocking.

7. **Fetch Vendors**  
   Retrieves a list of packaging vendors including their sustainability ratings and costs.

8. **Select Vendors & Create Purchase Orders**  
   Matches restock needs with vendors, selecting the best vendor based on a weighted score of sustainability rating over cost, and creates purchase orders.

9. **Place Orders with Suppliers**  
   Submits purchase orders to suppliers through the supplier portal API.

10. **Notification Trigger**  
    Checks if restock orders were created and triggers appropriate notifications.

11. **Send Notification**  
    Sends the notification message to a Telegram chat.

---

## Node Details

### 1. Fetch Product Details
- **Type:** HTTP GET  
- **Endpoint:** `https://api.yourcompany.com/products`  
- **Output:** JSON product details passed to AI Packaging Recommendation.

### 2. AI Packaging Recommendation
- **Type:** Function (JavaScript + API call)  
- **Function:** Sends product info to OpenAI GPT-4 via chat completion API to generate packaging recommendations focused on sustainability and suitability.  
- **Credentials:** OpenAI API Key required.

### 3. Fetch Sales Data
- **Type:** HTTP GET  
- **Endpoint:** `https://api.yourcompany.com/salesdata?period=last_6_months`  
- **Output:** JSON sales records used for forecasting.

### 4. Forecast Packaging Needs
- **Type:** Function  
- **Logic:** Sums sales quantities per product over 6 months, calculates average monthly sales, projects 10% growth, outputs forecasted packaging quantity per product.

### 5. Fetch Packaging Inventory
- **Type:** HTTP GET  
- **Endpoint:** `https://api.yourcompany.com/packagingInventory`  
- **Output:** Current packaging inventory.

### 6. Optimize Inventory & Identify Restock Needs
- **Type:** Function  
- **Logic:** Compares forecasted packaging quantities with on-hand inventory and identifies quantity shortfalls needing restocking.

### 7. Fetch Vendors
- **Type:** HTTP GET  
- **Endpoint:** `https://api.yourcompany.com/vendors?category=packaging`  
- **Output:** List of vendors with details including sustainability rating, cost, and products supplied.

### 8. Select Vendors & Create Purchase Orders
- **Type:** Function  
- **Logic:** Matches restock products with vendors, sorts vendors by sustainability rating/cost ratio, selects best vendor, creates purchase orders with vendor and product information.

### 9. Place Orders with Suppliers
- **Type:** HTTP POST  
- **Endpoint:** `https://api.supplierportal.com/orders`  
- **Payload:** vendorId, productId, quantity parameters from purchase orders.

### 10. Notification Trigger
- **Type:** Function  
- **Logic:** Generates notification message based on whether any restocking orders were placed.

### 11. Send Notification
- **Type:** Telegram Node  
- **Action:** Sends notification message to specified Telegram chat ID.  
- **Credentials:** Telegram API token required.

---

## Credentials Required

- **OpenAI API Key:** For GPT-4 model access.
- **Telegram API Token:** For sending notification messages.

---

## How It Works

1. The workflow fetches product and sales data needed for both packaging recommendation and demand forecasting.
2. An AI model recommends eco-friendly packaging tailored to each product.
3. Sales data is analyzed to forecast upcoming packaging needs.
4. Inventory is checked and compared against forecasts to identify restocking requirements.
5. Vendors are evaluated by sustainability and cost metrics to select optimal suppliers.
6. Purchase orders are automatically placed with chosen suppliers.
7. Notification is sent via Telegram summarizing the ordering status.

---

## API Endpoints

| Purpose                  | Method | URL                                             |
|--------------------------|--------|-------------------------------------------------|
| Fetch Products           | GET    | https://api.yourcompany.com/products             |
| Fetch Sales Data         | GET    | https://api.yourcompany.com/salesdata?period=last_6_months |
| Fetch Packaging Inventory | GET    | https://api.yourcompany.com/packagingInventory   |
| Fetch Vendors            | GET    | https://api.yourcompany.com/vendors?category=packaging |
| Place Supplier Orders    | POST   | https://api.supplierportal.com/orders             |

---

## Summary

This end-to-end automated workflow combines AI-driven packaging recommendations with data-driven inventory and order management to promote sustainable packaging practices and efficient supply operations. Notifications keep stakeholders informed of inventory needs and order statuses seamlessly.