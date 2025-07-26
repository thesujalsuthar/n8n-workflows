# AI-Powered Environmental Noise Pollution Monitor & Alert System

## Overview  
This workflow continuously monitors environmental noise pollution levels for specified user locations using the OpenAQ API. When noise levels exceed user-defined safe thresholds, it sends immediate alerts via email and SMS to notify users of the potential noise pollution hazard.

---

## Workflow Components

### 1. Initialize User Data  
- **Node Type:** Function  
- **Purpose:** Initializes user-specific data, including location coordinates, safe noise threshold (in decibels), user email, and phone number.  
- **Details:**  
  - `locationName`: Friendly name of the monitoring location.  
  - `latitude` & `longitude`: Geographic coordinates of the location.  
  - `safeNoiseThreshold`: Maximum safe noise level (in dB) defined by the user.  
  - `userEmail`: Recipient email address for alerts.  
  - `userPhone`: Recipient phone number for SMS alerts.

### 2. Set Default Locations  
- **Node Type:** Set  
- **Purpose:** Defines the monitoring location parameters with latitude, longitude, and safe noise threshold.  
- **Notes:** You can modify this node to add or change locations and thresholds as needed. This node feeds location info to the noise data fetch process.

### 3. Fetch Noise Data  
- **Node Type:** HTTP Request  
- **API:** [OpenAQ Latest Measurements API](https://api.openaq.org/v2/latest)  
- **Purpose:** Retrieves the latest noise pollution data within a 10 km radius of the provided coordinates.  
- **Parameters:**  
  - Coordinates dynamically composed from input latitude and longitude.  
  - Radius set to 10,000 meters.  
  - Filters to only include noise-related measurements using `parameters[]=noise`.

### 4. Analyze Noise Level  
- **Node Type:** Function  
- **Purpose:** Processes the fetched noise pollution data and identifies the maximum noise level detected at the location.  
- **Output:**  
  - Location details with the maximum measured noise level.  
  - Parses the safe noise threshold into a numerical value for comparison.

### 5. Check Threshold Exceed  
- **Node Type:** Function  
- **Purpose:** Evaluates if the detected noise level exceeds the defined safe threshold.  
- **Logic:**  
  - If no noise data is available, processing stops.  
  - If noise level > safe threshold, the workflow proceeds to send alerts; otherwise, it halts.

### 6. Send Email Alert  
- **Node Type:** Email Send  
- **Purpose:** Sends an email alert to the user when noise levels surpass the safe threshold.  
- **Parameters:**  
  - `fromEmail`: Set as `no-reply@envnoisealert.com`.  
  - `toEmail`: Dynamically set to the user’s email.  
  - Subject and body include the location name, detected noise level, and threshold exceeded.

### 7. Send SMS Alert  
- **Node Type:** Twilio (or compatible SMS service)  
- **Purpose:** Sends an SMS notification to the user about the noise alert.  
- **Parameters:**  
  - `phoneNumber`: Dynamically set to the user’s phone number.  
  - Message contains location name, noise level, and safe threshold exceedance.

---

## Workflow Execution Flow

1. **Initialize User Data** provides user and location information.  
2. **Set Default Locations** confirms or expands on location details.  
3. Both user data and location data feed into **Fetch Noise Data**, retrieving noise measurements from OpenAQ API.  
4. **Analyze Noise Level** identifies the highest noise measurement captured.  
5. **Check Threshold Exceed** determines if the noise surpasses the safe noise threshold.  
6. If exceeded, **Send Email Alert** and **Send SMS Alert** notify the user.  
7. If not exceeded or no data, the workflow ends silently.

---

## Configuration & Customization

- **User Data:** Modify the `Initialize User Data` node's function code to update location, threshold, user email, and phone number.  
- **Locations:** Add multiple location entries using additional Set nodes if monitoring several areas.  
- **Thresholds:** Adjust `safeNoiseThreshold` per location to reflect individual safety standards.  
- **Notifications:** Update email sender information or SMS service credentials as necessary.  
- **Polling Frequency:** Schedule this workflow on a timer trigger for periodic noise monitoring (not included in this workflow).

---

## Requirements

- **n8n Automation Platform** to run this workflow.  
- **Internet Access** for OpenAQ API calls and sending email/SMS notifications.  
- **Email Service Integration** configured for the Email Send node.  
- **Twilio or compatible SMS API account** configured for SMS alerts.

---

## Note

- Noise data availability depends on OpenAQ data coverage for the specified locations.  
- Ensure compliance with API rate limits and authentication requirements if applicable.  
- Update alert recipient details and sender configurations before activating the workflow.

---

## License

This workflow is provided as-is for educational and monitoring purposes. Modify and extend it to suit your environmental noise monitoring needs.