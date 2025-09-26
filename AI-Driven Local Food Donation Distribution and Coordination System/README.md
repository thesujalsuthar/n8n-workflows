# AI-Powered Local Food Donation Optimization and Notification System

This workflow automates the process of optimizing local food donation distribution and notifying coordinators for efficient pickup and delivery arrangements. It leverages AI to predict donation amounts and forecast demand at shelters, ensuring surplus food is effectively matched with food banks and shelters that have capacity.

---

## Workflow Overview

1. **Fetch Surplus Food Donations**  
   Queries a local PostgreSQL database to retrieve businesses currently estimating surplus food donations.

2. **AI Predict Donation Amount**  
   Sends business donation data to an AI API which predicts more accurate donation amounts based on historical trends.

3. **Fetch Available Food Banks and Shelters**  
   Queries the PostgreSQL database for shelters/food banks with available capacity to accept new donations.

4. **AI Forecast Demand**  
   Sends shelter data to an AI API to forecast demand levels, helping prioritize distribution more effectively.

5. **Optimize Distribution Logistics**  
   A custom function node matches each donation to the nearest shelter or food bank with remaining capacity, based on geographic coordinates and capacity constraints.

6. **Send Email Notifications**  
   Sends detailed pickup assignment emails to shelter coordinators to arrange donation pickups based on optimized assignments.

7. **Send Messaging Platform Notifications**  
   Posts assignment summaries to a Slack channel to alert all coordinators and volunteers.

---

## Node Details

### 1. Fetch Surplus Food Donations  
- **Type:** PostgreSQL Query  
- **Query:**  
  ```sql  
  SELECT business_id, business_name, surplus_food_estimate, location FROM local_businesses WHERE surplus_food_estimate > 0  
  ```  
- **Purpose:** Retrieve businesses with available surplus food donations.

### 2. AI Predict Donation Amount  
- **Type:** HTTP Request (POST)  
- **Operation:** `predictDonationAmount`  
- **Input:** Data from "Fetch Surplus Food Donations"  
- **Authentication:** Basic Auth (`api_user` / `api_password`)  
- **Purpose:** Request AI-powered prediction to refine donation quantity estimates.

### 3. Fetch Available Food Banks and Shelters  
- **Type:** PostgreSQL Query  
- **Query:**  
  ```sql  
  SELECT shelter_id, name, address, capacity, current_demand FROM food_banks_shelters WHERE capacity > current_demand  
  ```  
- **Purpose:** Retrieve shelters with capacity to accept more donations.

### 4. AI Forecast Demand  
- **Type:** HTTP Request (POST)  
- **Operation:** `forecastDemand`  
- **Input:** Data from "Fetch Available Food Banks and Shelters"  
- **Authentication:** Basic Auth (`api_user` / `api_password`)  
- **Purpose:** Forecast shelter demand to inform distribution logistics.

### 5. Optimize Distribution Logistics  
- **Type:** Function Node  
- **Inputs:**  
  - Donations with predicted amounts  
  - Shelters with forecasted demand and capacity data  
- **Operation:**  
  - Calculates geographic distance using latitude & longitude.  
  - Assigns each donation to the nearest shelter with remaining capacity.  
  - Updates shelter demand in real-time during assignment.  
- **Output:** Assignment list containing business, shelter details, donation amount, and distance.

### 6. Send Email Notifications  
- **Type:** Email Send  
- **From:** `noreply@localfoodnetwork.org`  
- **To:** Dynamic based on shelter name (e.g. `shelterName@coordinator.localfoodnetwork.org`)  
- **Subject:** `New Pickup Assignment: Donation to {{shelterName}}`  
- **Body:** Contains donation and delivery details to instruct coordinators.

### 7. Send Messaging Platform Notifications  
- **Type:** Slack Message Post  
- **Channel:** `coordinators_channel`  
- **Message:** Summarizes new pickup assignments with business name, donation amount, shelter name, and distance.  
- **Purpose:** Keep coordinators and volunteers informed in real-time via Slack.

---

## Credentials Required

- **PostgreSQL_LocalDB** – Access to local database containing businesses and shelters data.  
- **HTTP Basic Auth** – Authentication for the AI prediction and forecast APIs (`api_user` / `api_password`).  
- **SMTP_LocalFoodNetwork** – SMTP server credentials to send email notifications.  
- **Slack_Coordinators_Channel** – Slack bot token with permission to post messages to the coordinators channel.

---

## How It Works

- The workflow starts by loading current surplus food data and available shelters.
- It sends these datasets to AI services that improve quantity predictions and estimate incoming demand.
- Then, a logistics function smartly pairs donations with the closest shelter that can accept them, optimizing delivery routes and shelter usage.
- Finally, automated email and Slack notifications are sent out to ensure coordinators are immediately informed, enabling quick action for pickups and distributions.

---

## Benefits

- Data-driven donation allocation reduces food waste and maximizes the impact of community surplus.
- AI prediction improves accuracy over static estimates.
- Automated notifications streamline communication flow with coordinators.
- Logistics optimization helps minimize delivery distances and times.

---

## Notes

- This workflow assumes the location fields include latitude and longitude for distance calculations.
- Shelter coordinate and capacity updating happen dynamically inside the function node to avoid over-allocation.
- Email and Slack notifications trigger concurrently to cover different communication preferences.

---

End of documentation.