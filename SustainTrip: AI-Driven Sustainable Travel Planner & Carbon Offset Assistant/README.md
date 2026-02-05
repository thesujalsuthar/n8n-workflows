# Personalized AI-Powered Sustainable Travel Planner and Carbon Offset Advisor

This n8n workflow provides a personalized, AI-driven sustainable travel planning experience along with carbon footprint estimation and automated carbon offset purchasing. It is designed to help users create optimized eco-friendly travel itineraries, receive sustainability alerts, and confirm their travel plans via email.

---

## Workflow Overview

1. **Trigger HTTP Request**  
   Endpoint: `POST /plan-itinerary`  
   Accepts user travel details and preferences.

2. **Parse User Input**  
   Extracts and prepares user data (origin, destination, travel dates, preferences).

3. **AI Route Optimization**  
   Sends user data to OpenAI's GPT-4 model for generating an optimized travel itinerary focusing on eco-friendly transport options, multi-modal routes, travel duration limits, and carbon footprint estimates.

4. **Parse AI Response**  
   Parses the AI-generated itinerary JSON response for structured downstream handling.

5. **Generate Eco-Friendly Alerts**  
   Creates custom alerts based on user preferences, encouraging greener travel choices.

6. **Merge Itinerary with Alerts**  
   Combines the AI itinerary output with eco-friendly alerts to form a comprehensive response.

7. **Purchase Carbon Offset**  
   Automatically submits a carbon offset purchase request based on the total estimated carbon emissions for the trip, integrating with a carbon offset service.

8. **Create User Notification**  
   Generates a user-friendly message summarizing their sustainable travel plan, total carbon emissions, and offset purchase confirmation.

9. **Send Email Confirmation**  
   Sends a detailed email to the user, including the itinerary, emissions data, alerts, and confirmation of carbon offset purchase.

---

## Node Details

### 1. Trigger HTTP Request  
- **Type:** HTTP Trigger  
- **Method:** POST  
- **Path:** `/plan-itinerary`  
- Accepts JSON payload with user travel details and preferences.

### 2. Parse User Input  
- Extracts key fields from the HTTP request:  
  ```json
  {
    "destination": "string",
    "origin": "string",
    "departureDate": "ISO date string",
    "returnDate": "ISO date string",
    "preferences": {
        "ecoFriendlyTransportOnly": "boolean",
        "maxTravelTimeHours": "number"
    }
  }
  ```

### 3. AI Route Optimization  
- Uses OpenAI GPT-4 API via HTTP Request.  
- Sends a system prompt positioning the AI as a sustainable travel assistant.  
- User prompt requests an optimized itinerary including:  
  - Multi-modal eco-friendly transport options.  
  - Compliance with user preferences (eco-only transport, max travel time).  
  - Estimated carbon emissions per segment.  
- Temperature set to 0.7 to balance creativity and accuracy.

### 4. Parse AI Response  
- Parses JSON response from AI, expecting:  
  ```json
  {
    "itinerary": [
      {
        "segment": "string",
        "transportMode": "string",
        "durationHours": "number",
        "carbonEmissionKg": "number"
      }
    ],
    "totalCarbonEmissionKg": "number"
  }
  ```  
- Handles malformed responses gracefully by returning raw content if JSON parsing fails.

### 5. Generate Eco-Friendly Alerts  
- Analyzes user preferences to generate tailored messages such as:  
  - Suggesting eco-friendly transport if not chosen.  
  - Warning about long travel durations increasing emissions.

### 6. Merge Itinerary with Alerts  
- Combines the parsed itinerary and the generated alerts into one unified JSON object for further processing.

### 7. Purchase Carbon Offset  
- Sends a POST request to a carbon offset service API with:  
  - `offsetAmountKg`: total carbon emissions from the itinerary.  
  - `userEmail`: user's email for confirmation/record.  
- Requires API key credentials for authorization.

### 8. Create User Notification  
- Constructs a notification message summarizing:  
  - Confirmation of the planned itinerary.  
  - Total estimated carbon emissions in kg CO2.  
  - Confirmation that carbon offset purchase was successful.  
  - A friendly green journey wish.  
- Includes the itinerary information.

### 9. Send Email Confirmation  
- Sends an email to the user with:  
  - Subject: "Sustainable Travel Itinerary & Carbon Offset Confirmation"  
  - Body: HTML formatted message containing the notification and detailed itinerary JSON.  
- Uses configured SMTP credentials.

---

## Input Data Format

The payload sent to the `/plan-itinerary` POST endpoint should be a JSON object as follows:

```json
{
  "origin": "City A",
  "destination": "City B",
  "departureDate": "YYYY-MM-DDTHH:mm:ssZ",
  "returnDate": "YYYY-MM-DDTHH:mm:ssZ",
  "preferences": {
    "ecoFriendlyTransportOnly": true,
    "maxTravelTimeHours": 8
  },
  "userEmail": "user@example.com"
}
```

- **origin**: Starting location of the trip.  
- **destination**: End location of the trip.  
- **departureDate**: ISO formatted departure date/time.  
- **returnDate**: ISO formatted return date/time.  
- **preferences**: User travel preferences related to sustainability.  
- **userEmail**: Email address for sending confirmations.

---

## Required Credentials

- **OpenAI API Key**  
  Credentials for accessing the OpenAI GPT-4 chat completions endpoint.

- **Carbon Offset API Key**  
  API key to authenticate requests with the carbon offset purchasing service.

- **SMTP Email Credentials**  
  SMTP server access credentials for sending email confirmations.

---

## Example User Request

```bash
curl -X POST https://your-n8n-instance/plan-itinerary \
-H "Content-Type: application/json" \
-d '{
  "origin": "San Francisco, CA",
  "destination": "Portland, OR",
  "departureDate": "2024-07-01T09:00:00Z",
  "returnDate": "2024-07-07T17:00:00Z",
  "preferences": {
    "ecoFriendlyTransportOnly": true,
    "maxTravelTimeHours": 10
  },
  "userEmail": "jane.doe@example.com"
}'
```

---

## Summary

This workflow orchestrates an end-to-end sustainable travel planning process by leveraging AI for itinerary optimization, calculating carbon footprints, offering personalized eco-friendly travel alerts, purchasing carbon offsets automatically, and notifying users with detailed travel plans and confirmations—all integrated into a seamless user experience.