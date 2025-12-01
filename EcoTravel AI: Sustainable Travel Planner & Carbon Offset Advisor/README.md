# EcoTravel AI: Sustainable Itinerary & Carbon Offset Planner

## Overview

EcoTravel AI is an automated workflow designed to create sustainable travel itineraries combined with personalized carbon offset planning. It fetches travel options, calculates the associated carbon footprint, suggests verified carbon offset projects, compiles the data into a comprehensive itinerary, and sends the information via email to the user.

---

## Workflow Breakdown

### 1. **Start Node**

- **Purpose:** Initiates the workflow by collecting user input.
- **Input Data Schema:**
  - `name`: User's full name (string)
  - `email`: User's email address (string)
  - `origin`: Starting location (string)
  - `destination`: Trip destination (string)
  - `destinationRegion`: Region for filtering carbon offset projects (string)
  - `departureDate`: Departure date (YYYY-MM-DD)
  - `returnDate`: Return date (YYYY-MM-DD)

---

### 2. **Fetch Travel Options**

- **Node Type:** HTTP Request
- **Function:** Queries a travel data API to retrieve travel options based on the user's origin, destination, and travel dates.
- **Authentication:** Header-based Bearer Token using `Travel API Credentials`.
- **API Endpoint:** `https://api.traveldata.com/v1/search`
- **Query Parameters:**
  - `origin`
  - `destination`
  - `departureDate`
  - `returnDate`
- **Response:** Returns travel options including transport modes, routes, and distances.

---

### 3. **Calculate Carbon Footprint**

- **Node Type:** HTTP Request
- **Function:** Calculates the carbon footprint for each transport mode and distance based on the travel options.
- **Authentication:** Header-based API Key using `Carbon API Credentials`.
- **API Endpoint:** `https://api.carbonfootprint.com/v1/calculate`
- **Query Parameters:**
  - `transportMode` (e.g., plane, train, bus)
  - `distanceKm`
- **Response:** Provides estimated carbon emissions per transport mode and distance.

---

### 4. **Fetch Carbon Offset Projects**

- **Node Type:** HTTP Request
- **Function:** Retrieves a list of verified carbon offset projects based on the user's destination region.
- **Authentication:** Bearer Token using `Carbon Offset API Credentials`.
- **API Endpoint:** `https://api.carbonoffsetprojects.com/v1/projects`
- **Query Parameters:**
  - `maxResults`: Limits results to 5 projects
  - `region`: Filter projects by destination region
  - `minRating`: Filters projects with a minimum rating of 4
- **Response:** Returns an array of suitable carbon offset projects for user consideration.

---

### 5. **Compile Sustainable Itinerary**

- **Node Type:** Function
- **Function:** Processes and combines data from previous nodes to build:
  - Detailed itinerary options with transport mode, route, distance, and carbon footprint.
  - Recommended carbon offset projects.
  - Useful sustainability tips.
- **Output:** JSON object including itinerary data, offset project recommendations, and tips for sustainable travel.

---

### 6. **Send Sustainable Travel Email**

- **Node Type:** Email Send
- **Function:** Sends an email to the user containing:
  - The sustainable travel itinerary with carbon footprints.
  - Recommended carbon offset projects.
  - Practical sustainability tips.
- **Authentication:** SMTP credentials (`EcoTravel SMTP Credentials`).
- **Email Details:**
  - From: `ecotravel.ai@example.com`
  - To: User's email address from the input
  - Subject: "Your Sustainable Travel Itinerary & Carbon Offset Plan"
  - Body: A detailed message with itinerary, offset projects, and tips formatted for readability.

---

## Credentials Required

- **Travel API Credentials:** API key for travel data.
- **Carbon API Credentials:** API key for carbon footprint calculations.
- **Carbon Offset API Credentials:** API token for carbon offset project data.
- **EcoTravel SMTP Credentials:** For sending emails.

---

## How to Use

1. Provide all required user inputs: name, email, origin, destination, destinationRegion, departureDate, and returnDate.
2. The workflow automatically:
   - Fetches travel options.
   - Calculates carbon emissions.
   - Retrieves carbon offset projects.
   - Compiles a comprehensive sustainable travel itinerary.
   - Sends a detailed email to the user.
3. The user receives an email with their eco-friendly travel plan and actionable sustainability tips.

---

## Sustainability Tips Provided

- Choose trains or buses over flights when possible.
- Pack light to reduce carbon emissions.
- Use reusable water bottles and minimize plastic use.
- Offset your carbon footprint through verified projects.

---

## Summary

EcoTravel AI empowers travelers to make environmentally responsible choices by integrating real-time travel data, carbon footprint calculation, and verified offset options into one seamless workflow — helping users travel smarter and greener.