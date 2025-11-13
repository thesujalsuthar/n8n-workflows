# EcoTravel AI: Sustainable Itinerary & Carbon Offset Workflow

## Overview
This workflow, **EcoTravel AI**, is designed to provide users with sustainable travel itineraries combined with carbon footprint calculations and carbon offset recommendations. Upon receiving a start location, destination, travel date, and user email, it generates an eco-friendly travel plan, estimates the total carbon emissions for the trip, suggests carbon offset options, and sends a notification with all these details.

---

## Workflow Breakdown

### 1. Geocode Start Location
- **Node Name:** Geocode Start Location  
- **Type:** Travel API (Geocoding)  
- **Purpose:** Converts the user-provided start location into geographical coordinates (latitude and longitude).  
- **Parameters:**  
  - `location`: Uses the input JSON value for `"start_location"`.

### 2. Geocode Destination
- **Node Name:** Geocode Destination  
- **Type:** Travel API (Geocoding)  
- **Purpose:** Converts the user-provided destination into geographical coordinates (latitude and longitude).  
- **Parameters:**  
  - `location`: Uses the input JSON value for `"destination"`.

### 3. Fetch Sustainable Travel Options
- **Node Name:** Fetch Sustainable Travel Options  
- **Type:** Travel API  
- **Purpose:** Retrieves eco-friendly travel itineraries between the geocoded start and end coordinates for the specified travel date.  
- **Parameters:**  
  - `startCoordinates`: Coordinates from **Geocode Start Location** node.  
  - `endCoordinates`: Coordinates from **Geocode Destination** node.  
  - `date`: User travel date from input JSON.  
  - `mode`: Set explicitly to `"eco"` to prioritize sustainable transport options.

### 4. Calculate Carbon Footprint
- **Node Name:** Calculate Carbon Footprint  
- **Type:** Function Node  
- **Purpose:** Computes the total carbon emissions in kilograms (kg CO₂) estimated from the itinerary legs returned by the previous node.  
- **Logic:**  
  - Iterates through each leg of the itinerary.  
  - Sums the `carbonEmissionKg` values for all legs.  
  - Outputs the total carbon footprint and retains the itinerary for further reference.

### 5. Get Carbon Offset Recommendations
- **Node Name:** Get Carbon Offset Recommendations  
- **Type:** Carbon Offset API  
- **Purpose:** Fetches recommended carbon offset projects and options matching the total carbon emissions calculated.  
- **Parameters:**  
  - `carbonEmissionKg`: Takes the total carbon footprint calculated by the previous node.

### 6. Prepare Notification Message
- **Node Name:** Prepare Notification Message  
- **Type:** Function Item Node  
- **Purpose:** Formats a message combining the carbon emissions data, offset recommendations, and eco-friendly travel tips to notify the user.  
- **Details Included:**  
  - Estimated carbon emissions for the trip (formatted to 2 decimals).  
  - List of recommended carbon offset options (name, price, description).  
  - Additional eco-friendly travel tips for responsible travel.

### 7. Send User Notification
- **Node Name:** Send User Notification  
- **Type:** Notification (Email) Node  
- **Purpose:** Sends the generated message to the user's email address.  
- **Parameters:**  
  - `toEmail`: User email taken from input JSON under `"user_email"`.  
  - `subject`: "Your Sustainable Travel Itinerary & Carbon Offset Options"  
  - `message`: Content formatted in **Prepare Notification Message** node.

---

## Input Data Required
- `start_location`: String, user’s starting point (e.g., city or address).  
- `destination`: String, end travel location.  
- `travel_date`: Date string representing the planned travel date.  
- `user_email`: Email address to which notifications will be sent.

---

## How the Data Flows
1. **User Inputs:** The workflow starts by taking the user-supplied `start_location`, `destination`, `travel_date`, and `user_email`.
2. **Geocoding:** Converts locations to latitude and longitude.
3. **Fetch Itinerary:** Retrieves eco-friendly travel options based on coordinates and date.
4. **Carbon Calculation:** Sums carbon emissions from all legs of the itinerary.
5. **Offset Suggestions:** Uses total emissions to find fitting carbon offset projects.
6. **Message Preparation:** Compiles all data and recommendations into a comprehensive message.
7. **Notification:** Sends the message to the user’s email inbox.

---

## Summary
**EcoTravel AI** empowers travelers to plan trips responsibly by:  
- Providing sustainable itinerary options.  
- Transparently calculating their environmental impact.  
- Offering carbon offset solutions to neutralize travel emissions.  
- Encouraging greener habits with actionable tips.  

This workflow harnesses APIs and intelligent processing to promote sustainable travel practices effortlessly.

---

*End of document.*