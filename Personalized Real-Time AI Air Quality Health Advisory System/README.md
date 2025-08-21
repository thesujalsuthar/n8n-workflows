# AI-Powered Personalized Real-Time Air Quality Health Advisory System

## Overview
This workflow provides personalized real-time air quality health advisories to users based on their location and health profiles. It fetches current air quality data, analyzes the potential impact on the user’s health, and sends notifications via email and SMS.

---

## Workflow Nodes Description

### 1. User Data Input  
- **Type:** Set  
- **Purpose:** Captures user-specific data needed for processing.  
- **Inputs:**
  - `userEmail`: User’s email address (string).  
  - `userPhone`: User’s phone number (string).  
  - `userLocation`: Coordinates of the user’s location (string, e.g. `"latitude,longitude"`).  
  - `userHealthProfile`: JSON string containing health conditions, e.g.  
    ```json
    {
      "asthma": false,
      "cardiac": false,
      "otherConditions": []
    }
    ```  
- This node initializes the workflow with user-specific parameters.

### 2. Fetch Air Quality Data  
- **Type:** HTTP Request  
- **Method:** GET  
- **Endpoint:** `https://api.openaq.org/v2/latest`  
- **Parameters:**  
  - `coordinates`: Uses the value from `userLocation` for location-based querying.
  - `radius`: 5000 meters to fetch air quality data within a 5 km radius around the user.  
- **Function:** Retrieves the latest available air quality measurements near the user’s location from the OpenAQ API.

### 3. Analyze Health Impact  
- **Type:** Function  
- **Purpose:** Evaluates the retrieved air quality data against user’s health profile to generate personalized advisory messages.  
- **Logic:**  
  - Examines key pollutants: PM2.5, PM10, NO2, and O3.  
  - Applies thresholds for each pollutant (e.g., PM2.5 > 35 µg/m³ is considered elevated).  
  - If pollutant levels exceed thresholds:
    - Checks if user has asthma or cardiac conditions.
    - Generates tailored health risk messages accordingly.
  - Produces messages indicating either elevated pollutant risk or safe air quality status.  
- **Output:**  
  - User contact info: email and phone.  
  - An array `advisories` with pollutant, measurement value, and corresponding health advisory messages.

### 4. Send Email Notification  
- **Type:** Email Send  
- **To:** User’s email address from `userEmail`.  
- **Subject:** "Your Real-Time Air Quality Health Advisory"  
- **Body:** Personalized message listing all advisory messages for the user’s current location and health profile.  
- **Purpose:** Deliver a detailed air quality health advisory directly to the user’s inbox.

### 5. Send SMS Notification  
- **Type:** Twilio SMS Send  
- **To:** User’s phone number from `userPhone`.  
- **Message:** Concise advisory summarizing air quality risks as SMS alerts.  
- **Purpose:** Provide quick, actionable air quality alerts on the user’s mobile device.

---

## Data Flow Summary

1. **Input Collection:** User data including contact details, location coordinates, and health profile are set manually or via upstream systems in *User Data Input*.  
2. **Data Retrieval:** Using the provided coordinates, *Fetch Air Quality Data* queries the OpenAQ API for the latest pollutant measurements within a 5 km radius.  
3. **Health Impact Analysis:** The *Analyze Health Impact* node processes pollutant levels relative to user vulnerabilities, generating specific advisory messages.  
4. **User Notification:** Advisory messages are simultaneously sent via:
   - Email through *Send Email Notification*  
   - SMS via Twilio in *Send SMS Notification*

---

## Setup & Configuration

- **User Data Input:**  
  Populate user’s email, phone, geocoordinates (latitude, longitude), and health profile conditions before triggering the workflow.

- **API Integration:**  
  This workflow uses the [OpenAQ API](https://docs.openaq.org/) which is a free and open air quality data source; no authentication required. Ensure internet access to call this API.

- **Email Notification:**  
  Configure the email node with valid SMTP settings or integrated email provider credentials for sending emails.

- **SMS Notification:**  
  Requires a Twilio account with an active phone number and API credentials configured in the workflow environment for SMS sending capabilities.

---

## Health Advisory Logic Details

- Pollutants considered: PM2.5, PM10, NO2, O3.  
- Threshold values (example):  
  - PM2.5: 35 µg/m³  
  - PM10: 50 µg/m³  
  - NO2: 100 ppb  
  - O3: 120 ppb  
- Advisory messages vary based on:
  - Elevated pollutant levels.  
  - User’s pre-existing asthma or cardiac conditions.  
  - General sensitive population alerts if no known conditions.

---

## Example Advisory Message (Email)

```
Dear User,

Here is your personalized air quality health advisory based on current data:

- Elevated PM2.5 level. Sensitive individuals should take care.
- High risk for asthma sufferers due to elevated NO2.

Please take appropriate preventive measures.

Stay safe,
Air Quality Advisory System
```

---

## Extensibility

- Enhance the health impact function with advanced AI/ML models for more nuanced risk assessment.  
- Expand pollutant list and thresholds based on local regulations or new scientific research.  
- Integrate additional communication channels (push notifications, app alerts).  
- Incorporate dynamic user location acquisition and health profile updates.

---

## Disclaimer

This advisory system is intended for informational purposes only and is not a substitute for professional medical or environmental advice. Users should consult healthcare providers or local authorities for critical decisions.