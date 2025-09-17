# AI-Powered Sustainable Grocery Assistant

This workflow helps users create a personalized sustainable grocery list highlighting organic and local products while estimating the carbon footprint of each item. It also sends daily sustainable shopping tips via email to encourage eco-friendly habits.

---

## Overview

The workflow accepts a user's grocery query and email, fetches sustainable product recommendations from an external API focusing on local and organic items, estimates the carbon footprint for each product, formats the list with carbon data, and sends the compiled grocery list along with sustainable shopping tips via email.

Additionally, it sends a daily email with a sustainable shopping tip as a reminder to users.

---

## Workflow Nodes Description

### 1. Extract Input Data

- **Type:** Function  
- **Purpose:** Extracts the `query` and `email` fields from the incoming HTTP request payload.

---

### 2. Fetch Sustainable Products

- **Type:** HTTP Request (POST)  
- **Purpose:** Sends the user’s grocery query along with preferences (`local`, `organic`, `sustainable`) to an external sustainable products API (`https://api.sustainableproducts.example.com/v1/search`).  
- **Authentication:** API key in header (`YOUR_API_KEY`).  
- **Response:** Returns a list of products matching the query and preferences.

---

### 3. Filter Organic Local Products

- **Type:** Function  
- **Purpose:** Filters the API response to prioritize products tagged as `organic` and marked as `local`. If none match, all returned products are kept.

---

### 4. Create Grocery List

- **Type:** Function  
- **Purpose:** Creates a grocery list from filtered products including product `name`, `quantity` (default 1), and `category`.

---

### 5. Extract Product IDs

- **Type:** Function  
- **Purpose:** Extracts product IDs from the grocery list for use in the carbon footprint estimation API request.

---

### 6. Get Carbon Footprint Estimates

- **Type:** HTTP Request (POST)  
- **Purpose:** Queries an external carbon footprint estimation API (`https://api.carbonfootprint.example.com/v1/estimates`) using product IDs to get CO2 emission estimates.  
- **Authentication:** API key in header (`YOUR_CARBON_API_KEY`).

---

### 7. Attach Carbon Data to List

- **Type:** Function  
- **Purpose:** Merges the carbon footprint estimates with the grocery list, adding `carbonFootprintKgCO2` to each product item.

---

### 8. Prepare Sustainable Shopping Tip

- **Type:** Set  
- **Purpose:** Prepares a static shopping tip reminder:  
  *"Reminder: Choose reusable bags and buy only what you need to reduce waste. Check your grocery list for more sustainable options today!"*

---

### 9. Format Grocery List Message

- **Type:** Function  
- **Purpose:** Formats a text message listing each grocery item along with its estimated carbon footprint in kg CO2.

---

### 10. Send Grocery List Email

- **Type:** Email Send  
- **Purpose:** Sends an email to the user containing the formatted grocery list message and sustainable shopping tip.  
- **From:** no-reply@sustainablegroceries.example.com  
- **To:** User-provided email  
- **Subject:** "Your Sustainable Grocery List & Tips"  
- **SMTP Credentials Required**

---

### 11. Daily Reminder Trigger

- **Type:** Cron  
- **Purpose:** Triggers daily at a 24-hour interval (86400 seconds) to send sustainable shopping tips.

---

### 12. Prepare Daily Tip Text

- **Type:** Set  
- **Purpose:** Sets a daily sustainable shopping tip message:  
  *"🌿 Daily Sustainable Shopping Tip: Try to buy seasonal, local produce to lower your carbon footprint and support local farmers."*

---

### 13. Send Daily Tip Email

- **Type:** Email Send  
- **Purpose:** Sends the daily tip email to a preset email address (e.g., `user@example.com`).  
- **From:** no-reply@sustainablegroceries.example.com  
- **Subject:** "Your Daily Sustainable Shopping Tip"  
- **SMTP Credentials Required**

---

## Configuration Requirements

- **API Keys**:  
  - `YOUR_API_KEY` for sustainable products API authentication  
  - `YOUR_CARBON_API_KEY` for carbon footprint API authentication  

- **SMTP server credentials** for sending emails:  
  - User  
  - Password  
  - Host  
  - Port  
  - Secure (boolean)

---

## Usage

- Send a POST request to trigger the workflow with a JSON body containing the user’s `query` (grocery search term) and `email`.  
- The workflow will process and email a sustainable grocery list with carbon footprint estimates and shopping tips.  
- Daily sustainable tips will be sent automatically via cron trigger.

---

## Important Notes

- The workflow focuses on promoting sustainable shopping choices by highlighting organic, local, and low carbon footprint products.  
- Carbon footprint is shown per product in kg CO2 equivalent.  
- Emails are sent from `no-reply@sustainablegroceries.example.com`; configure SMTP settings accordingly.  
- Daily reminders support sustainable shopping awareness.

---

## Example Input Payload

```json
{
  "query": "fresh vegetables",
  "email": "user@example.com"
}
```

---

## Example Grocery List Message Sample

```
Your sustainable grocery list with estimated carbon footprint per item (kg CO2):
- Organic Carrots: 0.45
- Local Spinach: 0.30
- Broccoli: N/A
```

---

## License

This workflow is provided as-is for educational and personal use in promoting sustainable grocery shopping.