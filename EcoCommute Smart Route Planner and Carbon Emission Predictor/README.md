# EcoCommute Smart Route Planner and Carbon Emission Predictor

## Overview

EcoCommute is an intelligent workflow designed to help users find the most eco-friendly commuting routes by analyzing traffic data and public transit schedules. It calculates estimated carbon emissions for multiple transportation modes, ranks the routes based on their carbon footprint, and delivers the top three suggestions via email and Slack.

---

## Workflow Nodes Description

### 1. Set Input Data

- **Type:** Set Node  
- **Purpose:** Provides the initial input data required by the workflow, including:
  - Origin coordinates (latitude,longitude)  
  - Destination coordinates (latitude,longitude)  
  - Date and time of travel (ISO 8601 format)  
  - User's email address for sending results  
  - User's Slack channel for notifications  
- **Example Values:**
  ```json
  {
    "origin": "40.7128,-74.0060",
    "destination": "40.785091,-73.968285",
    "datetime": "2024-06-01T08:00:00Z",
    "userEmail": "user@example.com",
    "userSlackChannel": "#eco-commute"
  }
  ```

---

### 2. Fetch Traffic Data

- **Type:** HTTP Request  
- **Purpose:** Retrieves real-time traffic-based route options for car travel from an external traffic API.  
- **Request Details:**  
  - **URL:** `https://api.trafficservice.com/traffic?origin={origin}&destination={destination}`  
  - **Headers:** Authorization with Bearer token (`YOUR_TRAFFIC_API_KEY`)  
  - **Response Format:** JSON  
- **Data Extracted:** Routes suitable for car usage including distance (km) and duration (minutes).

---

### 3. Fetch Transit Schedules

- **Type:** HTTP Request  
- **Purpose:** Fetches public transit route options from an external public transit API.  
- **Request Details:**  
  - **URL:** `https://api.publictransit.com/routes?origin={origin}&destination={destination}&datetime={datetime}`  
  - **Headers:** Authorization with Bearer token (`YOUR_PUBLIC_TRANSIT_API_KEY`)  
  - **Response Format:** JSON  
- **Data Extracted:** Transit routes including legs specifying mode of transport (bus, train, bike, walk), distance (km), and duration (minutes).

---

### 4. Calculate and Sort Emissions

- **Type:** Function Node  
- **Purpose:**  
  - Extracts route details from both traffic and transit data.  
  - Calculates estimated carbon emissions for each route segment using standardized emission factors (in kg CO2 per km):
    - Car: 0.192  
    - Bus: 0.105  
    - Train: 0.041  
    - Bike: 0  
    - Walk: 0  
  - Combines all routes into a single list and sorts them by lowest carbon emissions.  
  - Selects the top 3 route suggestions for output.

- **Output:** Returns JSON containing an array of suggested routes, each with:
  - `mode` (transportation mode)  
  - `distance` (in km)  
  - `duration` (in minutes)  
  - `carbonEmissionKg` (estimated carbon emission)  
  - `routeDetails` (full route metadata)

---

### 5. Send Email with Suggestions

- **Type:** Email Send Node  
- **Purpose:** Sends an email to the user with the top 3 most eco-friendly commuting routes.  
- **Inputs:**  
  - Recipient email from `userEmail` input.  
  - Email subject: "Your Eco-Friendly Commute Route Suggestions"  
  - Email body: A formatted list of suggested routes including mode, distance, duration, and estimated carbon emissions.

- **Sender Email:** `n8n@example.com`

---

### 6. Send Slack Message

- **Type:** Slack Node  
- **Purpose:** Posts a message in the specified Slack channel summarizing the top 3 eco-friendly route suggestions.  
- **Inputs:**  
  - Slack channel from `userSlackChannel` input.  
  - Message text listing each route's mode, distance, duration, and carbon emissions.  

- **Credentials:** Requires Slack API credentials configured with appropriate permissions to send messages.

---

## Workflow Execution Flow

1. **Input Data Set** (Set Input Data) is provided and triggers parallel requests to:
   - Traffic API (Fetch Traffic Data)  
   - Transit API (Fetch Transit Schedules)

2. Both traffic and transit data outputs are fed into the **Calculate and Sort Emissions** function.

3. The function processes and combines routes, then outputs the three best options.

4. The results are sent out to the user via two channels concurrently:  
   - Email (Send Email with Suggestions)  
   - Slack message (Send Slack Message)

---

## Prerequisites

- Obtain API keys and permissions for:
  - Traffic data API (`YOUR_TRAFFIC_API_KEY`)  
  - Public transit API (`YOUR_PUBLIC_TRANSIT_API_KEY`)  
  - Slack (Slack API credentials configured in n8n)  

- Configure sender email account in the email node to allow outbound emails.

- Adjust input values (origin, destination, datetime, userEmail, userSlackChannel) as needed per usage.

---

## How to Use

1. Update the **Set Input Data** node with your desired trip origin, destination, travel time, and user contact details.

2. Replace the placeholders in HTTP Request nodes:
   - `YOUR_TRAFFIC_API_KEY`  
   - `YOUR_PUBLIC_TRANSIT_API_KEY`  

3. Configure email sending credentials and Slack integration inside n8n for messaging capabilities.

4. Trigger the workflow to run, process data, and deliver personalized, eco-friendly commuting recommendations.

---

## Additional Notes

- Emission factors are approximations based on average values; actual emissions may vary based on vehicle and transit conditions.

- The workflow currently limits suggestions to the top 3 routes with the lowest carbon emissions.

- The workflow supports common transportation modes: car, bus, train, bike, and walking.

---

Thank you for using **EcoCommute Smart Route Planner and Carbon Emission Predictor** — making your daily commute sustainable and environment-friendly!