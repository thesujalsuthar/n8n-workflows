# Sustainable AI Urban Commute Optimizer

## Overview
The **Sustainable AI Urban Commute Optimizer** is an automated workflow designed to provide eco-friendly daily commute plans for urban users. Leveraging real-time user location data, public transport and ride-sharing APIs, along with live traffic updates, the workflow intelligently recommends the most sustainable and efficient commute option. An AI-generated summary is emailed daily to the user, highlighting optimal routes, carbon footprint estimations, and traffic alerts.

---

## Workflow Schedule
- **Trigger:** The workflow runs automatically every day at 6:00 AM (cron schedule: `0 6 * * *`).
- This ensures users receive their daily personalized commute plan early each morning.

---

## Workflow Components & Details

### 1. Daily Trigger
- **Node:** `Daily Trigger`
- **Purpose:** Initiates the workflow daily at 6:00 AM.

### 2. Get User Location
- **Node:** `Get User Location`
- **Type:** HTTP Request
- **Description:** Fetches the current latitude and longitude of the user from a location service API (`https://api.user-location.com/getCurrentLocation`).
- **Response Format:** JSON

### 3. Extract Coordinates
- **Node:** `Extract Coordinates`
- **Type:** Function
- **Description:** Processes the API response to extract precise latitude (`lat`) and longitude (`lon`) values for subsequent steps.

### 4. Fetch Public Transport Routes
- **Node:** `Fetch Public Transport Routes`
- **Type:** HTTP Request
- **API Endpoint:** `https://api.public-transport.example.com/v1/routes`
- **Description:** Retrieves available public transport options from the user's current location to their destination.
- **Authentication:** Uses Bearer token from `Public Transport API Credentials`.
- **Query Parameters:**
  - `originLat`, `originLon`: User's current coordinates.
  - `destinationLat`, `destinationLon`: User's destination coordinates.
- **Response Format:** JSON

### 5. Fetch Ride-Sharing Options
- **Node:** `Fetch Ride-Sharing Options`
- **Type:** HTTP Request
- **API Endpoint:** `https://api.rideshare.example.com/v1/options`
- **Description:** Retrieves available ride-sharing options covering origin and destination points.
- **Authentication:** Uses Bearer token from `Ride Share API Credentials`.
- **Query Parameters:**
  - `start_lat`, `start_lon`: User's current coordinates.
  - `end_lat`, `end_lon`: User's destination coordinates.
- **Response Format:** JSON

### 6. Fetch Real-Time Traffic
- **Node:** `Fetch Real-Time Traffic`
- **Type:** HTTP Request
- **API Endpoint:** `https://api.traffic.example.com/v1/updates`
- **Description:** Retrieves real-time traffic status information for the user's current location.
- **Query Parameters:**
  - `lat`, `lon`: User's current coordinates.
- **Response Format:** JSON

### 7. Optimize Commute Route
- **Node:** `Optimize Commute Route`
- **Type:** Function
- **Description:** Combines data from public transport, ride-sharing options, and real-time traffic to score and sort commute options by estimated carbon footprint.
- **Logic:**
  - Assigns lower carbon scores to public transport routes compared to ride-sharing.
  - Adds penalties for heavy traffic conditions.
  - Picks the best (lowest carbon footprint) route while keeping all sorted options.
- **Outputs:**
  - `bestRoute`: The recommended eco-friendly commute option.
  - `allOptions`: Sorted list of all possible commute choices with carbon scores.

### 8. Generate Summary with AI
- **Node:** `Generate Summary with AI`
- **Type:** ChatGPT
- **Model:** GPT-4
- **Description:** Uses the AI to create a user-friendly daily summary based on the best commute route. The summary includes:
  - Highlights of eco-friendly commute plan.
  - Details on public transit or ride-share options.
  - Estimated carbon footprint.
  - Alerts on current traffic conditions.
- **Prompt:**
  - System role sets AI as an eco-friendly urban commuter assistant.
  - User asks for a summary based on the best route data.
  - Assistant is fed the optimization results to craft a clear, engaging message.

### 9. Send Commute Plan Email
- **Node:** `Send Commute Plan Email`
- **Type:** Email Send
- **Description:** Sends the AI-generated summary directly to the user's email.
- **Email Details:**
  - Recipient email is dynamically set (`{{$json.userEmail}}`).
  - Subject: **Your Eco-Friendly Commute Plan for Today**
  - Body: AI-generated summary from the previous node.

---

## Credentials Required

- **Public Transport API Credentials**
  - Used to authenticate when fetching public transport options.
- **Ride Share API Credentials**
  - Used to authenticate when fetching ride-sharing service options.

---

## How It Works - Data Flow Summary
1. Trigger runs every morning.
2. User location is fetched and latitude/longitude extracted.
3. Three parallel API calls fetch:
   - Public transport routes,
   - Ride-sharing options,
   - Real-time traffic updates.
4. All fetched data is processed to rank commute options by sustainability.
5. The best route and detailed options are summarized by AI.
6. Summary email sent to user for actionable daily commute guidance.

---

## Customization & Extensibility
- Destination coordinates (`destLat`, `destLon`) should be configured as per user preferences.
- API endpoints and credentials need to be set with real service providers.
- Carbon scoring logic in the function node can be adjusted for more complex models or additional factors.
- Email sender settings to be configured with appropriate SMTP or email node settings in n8n.

---

## Benefits
- Promotes sustainable urban transit choices.
- Combines multiple data sources for comprehensive decision-making.
- Provides actionable, easy-to-understand daily notifications.
- Supports personalized urban mobility empowered by AI.

---

> *Start your day empowered with the best eco-friendly commute options, optimized by real-time data and AI insights!*