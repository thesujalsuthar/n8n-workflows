# AI-Powered Smart Home Air Quality Monitoring & Alert System

This workflow continuously monitors indoor air quality using sensor data and sends alerts when pollutant levels or environmental conditions exceed safe thresholds. Users can receive alerts via email or push notifications based on their preferences.

---

## Workflow Overview

The system performs the following steps on a 5-minute interval:

1. **Trigger - Poll Air Quality Sensors**  
   Initiates the workflow every 300 seconds (5 minutes) to fetch the latest air quality data.

2. **HTTP Request - Get Air Quality Data**  
   Retrieves air quality data in JSON format from the smart home API endpoint:  
   `http://smart-home.local/api/air-quality`.

3. **Function - Analyze Air Quality Data**  
   Analyzes the fetched sensor data against predefined pollutant and environmental thresholds. It checks for:  
   - PM2.5 concentration (threshold: > 35 µg/m³)  
   - CO2 concentration (threshold: > 1000 ppm)  
   - VOCs (Volatile Organic Compounds) level (threshold: > 500 ppb)  
   - Humidity (threshold: > 60%)  
   - Temperature (threshold: > 30°C)  

   Generates alerts with recommendations when any threshold is exceeded.

4. **IF - Alerts Present?**  
   Checks if any alerts were generated. If no alerts exist, the workflow ends with a no-op action.

5. **User Settings**  
   Defines user-specific settings including:  
   - Recipient email address  
   - Preferred alert method ("email" or "push")

6. **Alert Delivery**  
   Depending on the user's preferred alert method:  
   - **Email Send - Air Quality Alert**  
     Sends an email containing all alerts and recommendations.  
   - **Push Notification - Air Quality Alert**  
     Sends a push notification with alert details using Pushbullet.

7. **No-op**  
   Represents the end of the workflow after alert dispatch or if no alerts are present.

---

## Detailed Node Descriptions

### 1. Trigger - Poll Air Quality Sensors

- Type: Interval Trigger  
- Interval: Every 300 seconds (5 minutes)  
- Purpose: Periodically starts the workflow.

### 2. HTTP Request - Get Air Quality Data

- Type: HTTP Request  
- Method: GET  
- URL: `http://smart-home.local/api/air-quality`  
- Response: JSON containing air quality metrics such as pm25, co2, voc, humidity, and temp.

### 3. Function - Analyze Air Quality Data

- Type: Function Node  
- Input: JSON air quality data  
- Logic: Compares current sensor readings against specified thresholds.  
- Output:  
  - `data`: Original sensor data  
  - `alerts`: Array of alert messages for any exceeded thresholds  
  - `alertPresent`: Boolean flag indicating if alerts exist

### 4. IF - Alerts Present?

- Type: Conditional Node  
- Condition: Checks if `alertPresent` is true  
- True Path: Proceeds to user settings for alert dispatch  
- False Path: Ends workflow with no further action (No-op)

### 5. User Settings

- Type: Set Node  
- Holds user configuration:  
  - `email`: User's email address  
  - `preferredAlertMethod`: `"email"` or `"push"`  

### 6a. Email Send - Air Quality Alert

- Type: Email Send Node  
- From: `no-reply@smarthome.local`  
- To: User's email from `User Settings` node  
- Subject: `"Indoor Air Quality Alert"`  
- Body: Lists all alerts and recommendations in text and HTML format  
- Credentials: Requires SMTP credentials set as `"SMTP Credentials"`

### 6b. Push Notification - Air Quality Alert

- Type: Pushbullet Node  
- Title: `"Indoor Air Quality Alert"`  
- Message: Detailed alert messages  
- Priority: High  
- Credentials: Requires Pushbullet API key `"Pushbullet API"`

### 7. No-op

- Type: No Operation Node  
- Purpose: Ends the workflow gracefully after alert dispatch or if no alerts exist.

---

## How to Use

1. **Set Up API Access:**  
   Configure your smart home air quality sensors and expose their data via the HTTP API endpoint at `http://smart-home.local/api/air-quality`.

2. **Configure Credentials:**  
   - Add SMTP credentials to enable email sending.  
   - Add Pushbullet API credentials if push notifications are preferred.

3. **Update User Settings:**  
   Modify the `User Settings` node to specify the alert recipient email and preferred notification method (`email` or `push`).

4. **Activate Workflow:**  
   Enable and activate the workflow in your n8n instance.

5. **Monitor & Respond:**  
   Alerts will be triggered and sent when indoor air quality parameters exceed safe thresholds. Take recommended actions to improve air quality.

---

## Thresholds Used

| Parameter | Safe Limit       | Unit   | Recommendation                                      |
|-----------|------------------|--------|----------------------------------------------------|
| PM2.5     | 35               | µg/m³  | Increase ventilation                                |
| CO2       | 1000             | ppm    | Open windows or increase airflow                    |
| VOC       | 500              | ppb    | Reduce VOC sources and ventilate                    |
| Humidity  | 60               | %      | Use a dehumidifier to prevent mold                  |
| Temperature | 30             | °C     | Consider cooling options                             |

Thresholds can be adjusted in the Function node as per your needs.

---

## Notes

- Ensure network access to the smart home API endpoint is stable and the endpoint follows the expected JSON schema.
- SMTP and Pushbullet credentials must be securely added to your n8n credentials for notifications to work.
- The system runs automatically every 5 minutes but interval can be adjusted by modifying the Trigger node.

---

*This AI-Powered Smart Home Air Quality Monitoring & Alert System helps maintain a healthy indoor environment by keeping users informed and providing actionable recommendations.*