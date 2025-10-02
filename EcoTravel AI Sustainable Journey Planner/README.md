# EcoTravel AI Sustainable Journey Planner Workflow

This workflow leverages n8n to create an interactive sustainable travel planning assistant via Telegram. Users provide trip details in JSON format, and EcoTravel AI responds with tailored sustainable transport, accommodation, activities, carbon footprint estimation, and carbon offset project recommendations. Results are sent via email and SMS.

---

## Workflow Overview

### 1. User Input (Telegram Trigger)
- **Node Type:** Telegram Trigger
- **Purpose:** Listens for user text input on Telegram.
- **Operation:** Sends a welcome message inviting the user to provide trip details in JSON format including destinations, dates, preferences, email, and phone number.

### 2. Parse Trip Details (Function)
- **Node Type:** Function
- **Purpose:** Parses incoming Telegram JSON text.
- **Operation:** Attempts to parse the user's input text as JSON. If parsing fails, an error is thrown indicating invalid JSON format.
- **Expected JSON format example:**
  ```json
  {
    "destinations": ["Paris", "Lyon"],
    "dates": { "start": "2024-07-01", "end": "2024-07-10" },
    "preferences": { "transport": ["train", "bus"], "accommodation": ["eco-hotel"], "activities": ["hiking"] },
    "email": "user@example.com",
    "phone": "+1234567890"
  }
  ```

### 3. Fetch Sustainable Transport Options (HTTP Request)
- **Node Type:** HTTP Request
- **Purpose:** Queries the EcoTravel API for sustainable transport options.
- **Request:** POST to `/sustainable-transport` with selected destinations and user preferences.
- **Response:** JSON object containing transport options.

### 4. Fetch Sustainable Accommodations (HTTP Request)
- **Node Type:** HTTP Request
- **Purpose:** Queries the EcoTravel API for sustainable accommodation options.
- **Request:** POST to `/sustainable-accommodations` with destinations, dates, and preferences.
- **Response:** JSON object containing accommodation options.

### 5. Fetch Sustainable Activities (HTTP Request)
- **Node Type:** HTTP Request
- **Purpose:** Queries the EcoTravel API for sustainable activity options.
- **Request:** POST to `/sustainable-activities` with destinations, dates, and preferences.
- **Response:** JSON object containing activity options.

### 6. Calculate Carbon Footprint (HTTP Request)
- **Node Type:** HTTP Request
- **Purpose:** Sends the collected transport, accommodation, and activity options with dates to a carbon footprint API.
- **Request:** POST to `https://api.carbonfootprint.io/calculate` including all the selected sustainable options.
- **Response:** JSON with estimated carbon footprint in kg CO2e.

### 7. Fetch Carbon Offset Projects (HTTP Request)
- **Node Type:** HTTP Request
- **Purpose:** Retrieves recommendations for carbon offset projects.
- **Request:** GET from `https://api.carbonoffset.com/projects/recommendations`.
- **Response:** JSON containing a list of recommended carbon offset projects.

### 8. Compile Travel Plan Summary (Function)
- **Node Type:** Function
- **Purpose:** Combines data collected from previous nodes into a human-readable trip summary.
- **Details Included:**
  - Destinations and trip dates
  - Sustainable transportation options (names, descriptions, booking links)
  - Sustainable accommodations (names, types, descriptions, booking/contact info)
  - Sustainable activities (names, descriptions, info links)
  - Estimated carbon footprint
  - Top 3 carbon offset project recommendations (names, descriptions, support URLs)
- **Output:** Summary text with user’s email and phone number from the input JSON.

### 9. Send Email Summary (Email Send)
- **Node Type:** Email Send
- **Purpose:** Emails the sustainable trip summary to the user.
- **Email Sent From:** ecotravel@domain.com (Replace with real SMTP credentials)
- **Recipient:** User's email provided in input
- **Subject:** “Your EcoTravel AI Sustainable Journey Plan”
- **Body:** Trip summary text compiled previously

### 10. Send SMS Summary (Twilio)
- **Node Type:** Twilio SMS
- **Purpose:** Sends the sustainable trip summary via SMS to the user.
- **Recipient:** User's phone number provided in input
- **Message:** Same trip summary text as email
- **Credentials:** Your Twilio API credentials required

---

## Data Flow & Connections

1. **User Input** → **Parse Trip Details**
2. **Parse Trip Details** → **Fetch Sustainable Transport Options**, **Fetch Sustainable Accommodations**, **Fetch Sustainable Activities** (parallel requests)
3. Each of the three fetch nodes → **Calculate Carbon Footprint** (collect responses for calculation)
4. **Calculate Carbon Footprint** → **Fetch Carbon Offset Projects**
5. **Fetch Carbon Offset Projects** → **Compile Travel Plan Summary**
6. **Compile Travel Plan Summary** → **Send Email Summary** and **Send SMS Summary** (parallel notifications)

---

## Setup & Configuration

- **Telegram Bot:** Create a bot and connect it to the Telegram Trigger node.
- **SMTP Server:** Configure your SMTP credentials in the Email Send node.
- **Twilio API:** Add your Twilio API credentials in the SMS node.
- **EcoTravel API:** Ensure access and valid API endpoints for the sustainable transport, accommodations, and activities.
- **Carbon Footprint API:** Access to `https://api.carbonfootprint.io/calculate` with necessary authentications if required.
- **Carbon Offset API:** Open or credentialed access to `https://api.carbonoffset.com/projects/recommendations`.

---

## Example User Input JSON

```json
{
  "destinations": ["Amsterdam", "Rotterdam"],
  "dates": { "start": "2024-08-01", "end": "2024-08-07" },
  "preferences": {
    "transport": ["electric train"],
    "accommodation": ["green hotel"],
    "activities": ["cycling", "nature walks"]
  },
  "email": "user@example.com",
  "phone": "+1234567890"
}
```

---

## Summary

This workflow provides a seamless, automated method to plan sustainable trips by integrating multiple data sources and delivering personalized travel plans with carbon impact awareness via Telegram, Email, and SMS. It empowers users to make environmentally responsible travel choices easily.

---