# AI-Driven Local Food Waste Redistribution and Donation Optimization Workflow

This workflow automates the process of redistributing surplus food from local businesses to nearby charities and food banks, optimizing for freshness and proximity. It simulates data retrieval, matches surplus items with appropriate charities, schedules pickups, and sends notifications to all involved parties.

---

## Workflow Nodes Description

### 1. Fetch Surplus Food Data
- **Type:** Function
- **Purpose:** Simulates fetching surplus food data from local businesses.
- **Details:** Returns mock data including business ID, name, surplus food items (with quantities and expiration dates), and geographic location (latitude and longitude).
- **Usage:** Replace with real API calls or database queries in a production environment.

### 2. Fetch Charity Data
- **Type:** Function
- **Purpose:** Simulates fetching information about local charities and food banks.
- **Details:** Provides charity ID, name, the list of food items needed, and geographic location.
- **Usage:** Replace with real API/database calls to dynamically retrieve charity requirements.

### 3. Match Surplus to Charities
- **Type:** Function
- **Purpose:** Matches surplus food items to charities based on their needs and physical proximity.
- **Logic:**
  - Iterates over surplus items and charity needs.
  - Checks if the charity requires a surplus item.
  - Calculates the distance between the business and the charity using the Haversine formula.
  - Only considers charities within a 5 km radius.
- **Output:** List of matched surplus items with corresponding charities and calculated distances.
- **Notes:** This node enables precise identification of potential donation matches based on local demand and location.

### 4. Optimize Donation Schedule
- **Type:** Function
- **Purpose:** Creates an optimal donation pickup schedule.
- **Logic:**
  - Sorts matches first by earliest expiration date, then by closest distance.
  - Assigns pickup times at 30-minute intervals starting from the current time.
- **Output:** Detailed pickup schedule including scheduled pickup times for each matched item.
- **Notes:** Ensures food is collected in time to maintain freshness and minimize waste.

### 5. Notify Surplus Business
- **Type:** Email Send
- **Purpose:** Sends notification emails to the local businesses with scheduled pickup details.
- **Email Details:**
  - **From:** notifications@waste-redistribution.local
  - **To:** Business contact (formatted as `"Business Name <business_contact@example.com>"`)
  - **Subject:** "Donation Pickup Scheduled for Surplus Food: [Item Name]"
  - **Body:** Includes item details, quantity, scheduled pickup time, and recipient charity information.
- **Usage:** Automatically informs businesses of upcoming donation pickups.

### 6. Notify Charity
- **Type:** Email Send
- **Purpose:** Sends notification emails to charities about scheduled donation pickups.
- **Email Details:**
  - **From:** notifications@waste-redistribution.local
  - **To:** Charity contact (formatted as `"Charity Name <charity_contact@example.com>"`)
  - **Subject:** "Donation Pickup Scheduled: [Item Name] from [Business Name]"
  - **Body:** Includes item details, quantity, scheduled pickup time, and donor information.
- **Usage:** Notifies charities in advance to prepare for the incoming donation.

---

## Workflow Execution Flow

1. **Fetch Surplus Food Data** and **Fetch Charity Data** nodes run in parallel to simulate data retrieval.
2. Data from both sources connect into the **Match Surplus to Charities** node to identify valid matches within a 5 km radius.
3. Matched items are passed to the **Optimize Donation Schedule** node, which prioritizes pickups based on expiration and proximity, then schedules them at regular intervals.
4. Finally, the optimized donation schedule triggers two parallel email notifications:
   - One to the surplus food business.
   - One to the receiving charity.

---

## Customization and Integration

- Replace the simulated data in **Fetch Surplus Food Data** and **Fetch Charity Data** with dynamic data sources (e.g., APIs, databases).
- Update recipient email addresses in the notification nodes to real contact information.
- Adjust the 5 km proximity radius or scheduling intervals as per local logistics capabilities.
- Integrate with real email services for notifications.

---

## Summary

This workflow provides an end-to-end solution to reduce local food waste by intelligently matching surplus food inventory to charity needs, optimizing donation scheduling by perishability and location, and automating notifications to stakeholders. It is designed for easy adaptation and scaling within community food redistribution initiatives.