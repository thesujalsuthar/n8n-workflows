# EcoSmart Fridge Inventory & Expiration Alert System

A comprehensive workflow designed to monitor your EcoSmart fridge inventory daily, identify items nearing expiration, suggest recipes based on user preferences, send timely reminders, manage restocking, and generate usage analytics.

---

## Workflow Overview

This workflow automates the process of managing fridge inventory by performing the following key functions every day at 9:00 AM:

1. **Fetch Fridge Inventory**  
   Retrieves the current inventory data from the EcoSmart Fridge API.

2. **Filter Soon-To-Expire Items**  
   Filters items that are set to expire within the next 3 days.

3. **Get User Preferences**  
   Retrieves the user's preferences such as recipe categories and contact details by calling an external workflow.

4. **Suggest Recipes**  
   Suggests recipes based on items nearing expiration and the user’s preferred recipe types.

5. **Send Reminders**  
   Sends an email with details of soon-to-expire items and recipe suggestions, and pushes a notification to the user's device.

6. **Identify Items to Restock**  
   Checks inventory quantities to determine which items are low in stock and need reordering.

7. **Place Restock Order**  
   Automatically places a restock order with the grocery delivery service for items identified as low stock.

8. **Compute Usage Analytics**  
   Analyzes consumption data to generate insights on food usage patterns.

9. **Send Analytics Data**  
   Sends aggregated usage statistics to a remote analytics data warehouse.

---

## Node Breakdown

### 1. Daily Trigger  
- **Type:** Schedule Trigger  
- **Function:** Executes the workflow every day at 09:00 AM.

### 2. Fetch Fridge Inventory  
- **Type:** HTTP Request  
- **Function:** Sends a GET request to `https://api.ecosmartfridge.local/inventory` with an API key for authorization to fetch the current fridge inventory data.  
- **Authentication:** Header Bearer token using stored credential `ecoSmartFridgeApi.apiKey`.

### 3. Filter Soon-To-Expire Items  
- **Type:** Function  
- **Function:** Filters inventory items that will expire within the next 3 days. Items without an expiry date are ignored.

### 4. Get User Preferences  
- **Type:** Workflow Call  
- **Function:** Calls a separate workflow (`FindUserPreferences`) to fetch user-specific settings such as email, device token, name, and preferred recipe categories.

### 5. Suggest Recipes  
- **Type:** Function  
- **Function:** Suggests recipes based on soon-to-expire items that align with the user’s preferred recipe categories. Creates personalized recipe suggestions.

### 6. Send Email Reminder  
- **Type:** Email Send  
- **Function:** Sends an email reminder to the user’s email address with the list of items approaching expiration and recipe suggestions.  
- **Variables Used:** User's name, soon-to-expire items, recipe suggestion list.

### 7. Send Push Notification  
- **Type:** Push Notification  
- **Function:** Sends a push notification to the user’s device summarizing the number of items near expiration.  
- **Variables Used:** Device token from user preferences.

### 8. Identify Items To Restock  
- **Type:** Function  
- **Function:** Identifies items with quantities less than or equal to a threshold (2 units) for restocking.

### 9. Place Restock Order  
- **Type:** HTTP Request  
- **Function:** Sends a POST request to `https://api.grocerydelivery.local/order` to place an order for low-stock items.  
- **Authentication:** Header Bearer token using stored credential `groceryApi.apiKey`.

### 10. Compute Usage Analytics  
- **Type:** Function  
- **Function:** Analyzes weekly usage data from the inventory to summarize consumption statistics by item category.

### 11. Send Analytics Data  
- **Type:** HTTP Request  
- **Function:** Sends the computed usage analytics data as a POST request to a centralized data warehouse at `data-warehouse.ecosmart.local/usage-analytics`.

---

## Key Credentials Required

- `ecoSmartFridgeApi.apiKey` — API key for authenticating with EcoSmart Fridge API.
- `groceryApi.apiKey` — API key for authenticating with the grocery delivery service.

---

## Execution Flow

- Workflow starts daily triggered at 9:00 AM.
- Fetches fridge inventory and user preferences in parallel.
- Filters and analyzes inventory for soon-to-expire items, recipes, restocks, and usage stats.
- Sends notifications and orders as needed.
- Posts analytics data for further analysis.

---

## Customization

- **Trigger Time:** Adjust the "Daily Trigger" node to change the scheduled execution time.
- **Expiration Window:** Modify the filter in "Filter Soon-To-Expire Items" node to change how many days ahead to check for expiry.
- **Low Stock Threshold:** Change the threshold in "Identify Items To Restock" node for different restocking levels.
- **Recipe Logic:** Enhance "Suggest Recipes" function for more complex recommendation algorithms.
- **Notification & Email Templates:** Edit the corresponding nodes to personalize messaging.

---

## Summary

This workflow provides an automated ecosystem centered around efficient fridge management with proactive notifications, recipe inspiration, inventory replenishment, and usage analytics — all working seamlessly to help users minimize food waste and optimize their meal planning.