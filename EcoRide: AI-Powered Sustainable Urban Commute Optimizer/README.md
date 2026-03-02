# EcoRide: AI-Powered Sustainable Urban Mobility and Carbon Footprint Optimizer

EcoRide is an automated workflow designed to optimize urban commute routes for sustainability using real-time urban mobility data, calculate carbon footprints, generate personalized emission reduction suggestions, log user commute data, and visualize carbon footprint trends through a dashboard. The workflow leverages AI-driven scoring and comprehensive analytics to encourage eco-friendly travel choices.

---

## Workflow Overview

### 1. Setup Database
- **Node:** Setup Database  
- **Type:** Postgres  
- **Description:** Initializes the PostgreSQL database table `eco_ride_commutes` with columns to store user commute data including `userId`, `travelMode`, `distanceKm`, `carbonFootprint`, and `date`.

### 2. Fetch Real-Time Urban Mobility Data
- **Node:** Fetch Real-Time Urban Mobility Data  
- **Type:** HTTP Request  
- **Description:** Retrieves current urban mobility data from the API endpoint `https://api.urbanmobilitydata.com/realtime` using a GET request. The data contains routes with attributes like bike lane percentage, public transport availability, traffic congestion, travel mode, and distance.

### 3. Optimize Routes for Sustainability
- **Node:** Optimize Routes for Sustainability  
- **Type:** Function  
- **Description:** Processes the fetched mobility data to calculate a sustainability score for each route. The score is computed as:  
  `sustainabilityScore = (bikeLanePercentage * 0.4) + (publicTransportAvailability * 0.4) - (trafficCongestion * 0.2)`  
  Routes are sorted in descending order of sustainability score to identify the most sustainable options.

### 4. Calculate Carbon Footprints
- **Node:** Calculate Carbon Footprints  
- **Type:** Function  
- **Description:** Calculates carbon footprints for each route based on travel mode and distance. The calculation applies predefined carbon emission factors (kg CO2 per km):  
  - Car: 0.192  
  - Public Transport: 0.105  
  - Bike / Walk: 0  
  The carbon footprint is computed as `carbonFactors[travelMode] * distanceKm`.

### 5. Generate Emission Reduction Suggestions
- **Node:** Generate Emission Reduction Suggestions  
- **Type:** Function  
- **Description:** Analyzes the best (top-scoring) route and its carbon footprint to generate personalized suggestions for reducing emissions. Examples include switching to public transport, carpooling, adjusting departure time, or encouragement for sustainable modes like biking or walking.

### 6. Send Personalized Notification
- **Node:** Send Personalized Notification  
- **Type:** Send Email  
- **Description:** Sends an email to the user containing their optimized route details (mode, distance, estimated carbon footprint) and emission reduction suggestions.  
- **Dynamic Email To:** `={{$json["userEmail"]}}`  
- **Subject:** "Your EcoRide Daily Commute Recommendations"  
- **Body:** Includes route info and suggestions formatted in HTML.

### 7. Log Commute Data
- **Node:** Log Commute Data  
- **Type:** Postgres  
- **Description:** Inserts the commute record (userId, travelMode, distanceKm, carbonFootprint, date) for the best route into the `eco_ride_commutes` database table, enabling longitudinal tracking.

### 8. Fetch User Commute Analytics
- **Node:** Fetch User Commute Analytics  
- **Type:** Postgres  
- **Description:** Queries the last 30 days of commute data per user, aggregating average carbon footprint by date and travel mode to support analytics and trend visualization.

### 9. Format Analytics Data
- **Node:** Format Analytics Data  
- **Type:** Function  
- **Description:** Formats the fetched SQL analytics results into structured JSON suitable for chart rendering, organizing by date and travel mode with average carbon footprint values.

### 10. Render Analytics Dashboard
- **Node:** Render Analytics Dashboard  
- **Type:** HTTP Response  
- **Description:** Produces an HTML dashboard featuring a line chart (using Chart.js) visualizing carbon footprint trends over the past month by travel mode. The dashboard distinguishes car, public transport, and bike/walk routes with color coding and axes labels.

---

## Data Flow and Connections

- Real-time data is fetched → routes are optimized → carbon footprints calculated → emission suggestions generated → personalized notification sent & commute data logged → user analytics fetched → analytics data formatted → dashboard rendered.
- Commute data logging triggers analytics fetching enabling up-to-date visualization.
- User email and ID are dynamically used to personalize notifications and log data.

---

## Requirements and Credentials

- **Database:** PostgreSQL instance with credentials named `Postgres EcoRide DB`.
- **API Access:** Urban Mobility Data API at `https://api.urbanmobilitydata.com/realtime`.
- **Email Service:** Configured email credentials connected for sending notifications.
- **Javascript Runtime:** For function nodes processing data and calculations.
- **Chart.js CDN:** Used for client-side rendering of analytics charts in the dashboard.

---

## Usage

1. Configure PostgreSQL credentials (`Postgres EcoRide DB`).
2. Ensure user data input includes `userId` and `userEmail`.
3. Run the workflow to fetch latest mobility data and process.
4. Users receive personalized commute recommendations daily.
5. Access the rendered dashboard endpoint to view recent carbon footprint trends.

---

## Summary

EcoRide provides an end-to-end solution for enhancing sustainable urban mobility through real-time data utilization, AI-powered route optimization, carbon footprint tracking, personalized user engagement, and actionable analytics visualization—all automated within a single workflow.