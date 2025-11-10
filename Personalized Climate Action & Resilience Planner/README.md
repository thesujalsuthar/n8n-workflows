# AI-Driven Personalized Climate Resilience & Action Planner

This workflow leverages user inputs and multiple APIs to generate a customized daily climate resilience and action plan. It calculates the user's carbon footprint, fetches air quality data and local climate events, generates tailored recommendations, stores the plan, and sends a reminder via SMS.

---

## Workflow Overview

1. **Initialize Workflow**  
   Captures the user's ID (or sets it as "anonymous") and timestamps the workflow start.

2. **Get User Input**  
   Triggered manually to collect key user information:  
   - Location (city name or coordinates)  
   - Household size  
   - Transportation type (gasoline car, electric car, public transit, bicycle, walking)  
   - Monthly electricity usage (kWh)  
   - Diet preference (omnivore, vegetarian, vegan)

3. **Parse Coordinates**  
   Parses the `location` string to extract latitude and longitude coordinates if provided in "lat, lon" format.

4. **Fetch Air Quality Data**  
   Uses the OpenWeather API to retrieve air pollution data based on user coordinates.

5. **Calculate Carbon Footprint**  
   Calls an external carbon footprint API using the user's household size, transportation type, electricity usage, and diet to estimate their footprint.

6. **Generate Recommendations**  
   Analyzes the carbon footprint and diet preferences to build a list of actionable, personalized climate impact reduction tips. Example recommendations include:  
   - Reducing car usage  
   - Switching to energy-efficient appliances  
   - Incorporating more plant-based meals  
   - Installing solar panels or adopting green energy

7. **Fetch Local Climate Events**  
   Retrieves upcoming climate-related events in the user's location from a community events API.

8. **Create Personalized Action Plan**  
   Combines the recommendations and up to three local events into a daily action plan with tasks marked incomplete.

9. **Store Action Plan**  
   Upserts (creates or updates) the user's action plan in a PostgreSQL database under the `user_action_plans` table with user ID, plan date, action plan JSON, and last updated timestamp.

10. **Send Reminder**  
    Sends an SMS notification to the user through Twilio, alerting them that their personalized climate action plan is ready to view on their dashboard.

---

## Nodes Detailed Description

### 1. Initialize Workflow (Function Node)  
- Sets user ID default (“anonymous” if not provided)  
- Adds a timestamp for when the workflow started

### 2. Get User Input (Manual Trigger)  
- Accepts user-provided inputs with defined fields and defaults for:  
  - Location (string)  
  - Household size (number)  
  - Transportation (select from given options)  
  - Electricity usage in kWh/month (number)  
  - Diet preference (select from options)

### 3. Parse Coordinates (Function Node)  
- Checks if location is a coordinate string with latitude and longitude separated by a comma  
- Parses and assigns latitude and longitude as separate fields

### 4. Fetch Air Quality Data (HTTP Request)  
- Queries [OpenWeather Air Pollution API](https://api.openweathermap.org/data/2.5/air_pollution)  
- Uses parsed latitude, longitude, and API key stored in credentials

### 5. Calculate Carbon Footprint (HTTP Request)  
- Calls an external API (`https://climateapi.example.com/carbon-footprint`) passing:  
  - Household size  
  - Transportation type  
  - Electricity usage  
  - Diet  
- Receives estimated carbon footprint data

### 6. Generate Recommendations (Function Node)  
- Uses footprint and diet data to generate recommendations such as reducing car use, switching appliances, and adopting plant-based meals  
- Adds generic advice like solar panel installation or green energy use

### 7. Fetch Local Climate Events (HTTP Request)  
- Calls community API (`https://community-api.example.com/events`) with the user's location  
- Retrieves upcoming events related to climate action in user’s area

### 8. Create Personalized Action Plan (Function Node)  
- Aggregates recommendations and event participation tasks into a list of daily tasks  
- Includes task structure with `task` description, completion status (default false), and event ID when available

### 9. Store Action Plan (PostgreSQL Node)  
- Upserts data into `user_action_plans` table keyed by `userId`  
- Stores plan date, serialized action plan, and last updated timestamp

### 10. Send Reminder (Twilio Node)  
- Sends SMS to user's phone number with a custom message notifying them of their new climate action plan

---

## Credentials Required

- **OpenWeather API Key**: For air quality data requests
- **PostgreSQL Database Credentials**: To store user action plans
- **Twilio API Credentials**: For sending SMS reminders

---

## Supported User Inputs

| Field                | Type    | Description                             | Default        |
|----------------------|---------|-------------------------------------|----------------|
| Location             | String  | City name or coordinates (lat,lon)  | (empty)        |
| Household Size       | Number  | Number of people in household        | 1              |
| Transportation       | Option  | Method of transport                  | Car (Gasoline) |
| Electricity Usage    | Number  | kWh per month                       | 300            |
| Diet                 | Option  | Food consumption preference          | Omnivore       |

---

## How to Use

1. Trigger the workflow manually to input your details.  
2. Provide location, household size, transportation, electricity usage, and diet details.  
3. The workflow automatically fetches necessary data, calculates your footprint, and creates a personalized daily action plan.  
4. Your action plan is stored in a database and a reminder SMS is sent to your phone.  
5. Access your dashboard to review recommendations and upcoming local climate events.

---

## Notes

- The workflow defaults to anonymous user if no userId is provided.  
- Location parsing supports both city names and latitude/longitude coordinates formatted as `"lat, lon"`.  
- External API endpoints for carbon footprint and community events are placeholders and should be replaced with real services as applicable.  
- Ensure credentials for APIs and database are properly configured before execution.

---

Thank you for using the AI-Driven Personalized Climate Resilience & Action Planner! Together, we can take meaningful steps toward a sustainable future.