# AI-Powered Sustainable Urban Mobility Optimizer

This workflow optimizes urban mobility by integrating real-time public transit data, traffic sensor information, and user feedback to generate AI-driven route and schedule optimizations. It stores the results in a database and sends notifications to city planners.

---

## Workflow Overview

1. **Fetch Public Transit Data**  
   Retrieves real-time vehicle positions from a public transit API using OAuth2 authentication.

2. **Fetch Traffic Sensor Data**  
   Acquires live traffic sensor data via a direct HTTP GET request.

3. **Fetch User Feedback**  
   Pulls recent commuter feedback from a user feedback API, authenticated via Bearer token in headers.

4. **Normalize and Combine Data**  
   Parses and aggregates data from the three sources. Normalizes and prepares combined data for AI processing.

5. **AI Optimize Routes & Scheduling**  
   Simulates AI-based prediction and optimization of transit routes and schedules. Outputs optimized routes, schedules, emission reduction estimates, and predicted commuter satisfaction.

6. **Store Optimized Routes**  
   Upserts the optimized route data into a PostgreSQL database (`optimized_routes` table).

7. **Store Commuter Satisfaction**  
   Upserts the predicted commuter satisfaction metric into a PostgreSQL database (`commuter_experience_metrics` table).

8. **Format Notification Message**  
   Creates a detailed message summarizing the AI optimization results for dissemination.

9. **Email Report**  
   Sends the formatted notification via email to city planners using SMTP credentials.

---

## Node Details

### 1. Fetch Public Transit Data
- **Type:** HTTP Request  
- **Authentication:** OAuth2 (Public Transit API OAuth2 credentials)  
- **Endpoint:** `GetRealtimeVehiclePositions`  
- **Purpose:** Retrieve live vehicle positions on transit routes.

### 2. Fetch Traffic Sensor Data
- **Type:** HTTP Request  
- **URL:** `https://api.urbantraffic.example.com/traffic_sensors/realtime`  
- **Headers:** Accepts JSON  
- **Purpose:** Get current traffic flow and conditions from city sensors.

### 3. Fetch User Feedback
- **Type:** HTTP Request  
- **Authentication:** Header Auth with Bearer token (User Feedback API Key)  
- **URL:** `https://user-feedback.example.com/api/feedbacks?since={{$now.toISOString()}}` (fetch feedback since current time)  
- **Purpose:** Collect latest commuter feedback for quality considerations.

### 4. Normalize and Combine Data
- **Type:** Function  
- **Operation:**  
  - Extract data from transit, traffic, and feedback APIs.  
  - Normalize and merge datasets into a structured format suitable for AI input.

### 5. AI Optimize Routes & Scheduling
- **Type:** Function  
- **Operation:**  
  - Simulate AI route optimization based on combined data.  
  - Outputs optimized routes with schedules, estimated emission reductions, and predicted commuter satisfaction.

### 6. Store Optimized Routes
- **Type:** PostgreSQL  
- **Operation:** Upsert `optimized_routes` table with AI-optimized route data.

### 7. Store Commuter Satisfaction
- **Type:** PostgreSQL  
- **Operation:** Upsert `commuter_experience_metrics` table with predicted satisfaction.

### 8. Format Notification Message
- **Type:** Function  
- **Operation:** Construct a message summarizing:  
  - Optimized routes and their schedules.  
  - Emission reductions.  
  - Predicted commuter satisfaction percentage.

### 9. Email Report
- **Type:** Email Send  
- **To:** `city.planners@urbanmobility.example.com`  
- **Subject:** Daily AI Urban Mobility Optimization Report  
- **Body:** Contains the formatted notification message.  
- **Credentials:** SMTP Credentials for email sending.

---

## Credentials Required

- **Public Transit API OAuth2 Credentials** – for accessing transit data securely.
- **User Feedback API Key** – Bearer token for authorized feedback API access.
- **Urban Mobility Database (PostgreSQL)** – for storing optimization results.
- **SMTP Credentials** – for sending email reports.

---

## Execution Flow

1. Fetch data from all three sources concurrently.
2. Normalize and combine incoming datasets into a unified input.
3. Pass combined data to AI simulation for route and schedule optimization.
4. Store results and predicted satisfaction metrics in PostgreSQL.
5. Format a notification with key outcome summaries.
6. Email the report to city planners for operational review.

---

## Customization Tips

- Replace placeholder AI logic with actual model integrations as needed.
- Update API endpoints and authentication details per your service.
- Modify SQL queries for database schema compatibility.
- Adjust email recipient addresses and SMTP settings to match infrastructure.

---

## License

This workflow is provided "as-is" for demonstration and customization purposes.