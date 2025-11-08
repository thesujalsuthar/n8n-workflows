# AI-Powered Smart Home Water Leak Detection & Automated Response System

This workflow integrates AI-powered anomaly detection with a smart home water leak monitoring system. When a water leak is detected by sensors, the system automatically triggers responses to minimize damage and notify relevant parties.

---

## Workflow Overview

1. **Water Leak Sensor Webhook**  
   Listens for incoming POST requests from water leak sensors via the `/water-leak-sensor-webhook` endpoint. It receives real-time sensor data including water level, sensor ID, and homeowner contact information.

2. **AI Anomaly Detection**  
   Processes incoming sensor data using embedded AI logic (simulated in this workflow with a threshold check). Determines whether a water leak event has occurred based on the `waterLevel` value exceeding a predefined threshold.

3. **If Leak Detected (Conditional Check)**  
   Filters events to only proceed when a leak is detected (`isLeakDetected = true`). Non-leak events do not proceed further.

4. **Compose Alert Message**  
   Generates a detailed alert message indicating the detection of a leak along with the sensor’s identifier.

5. **Shut Off Water Valve**  
   Sends a POST request to the smart home system’s water valve API endpoint (`https://api.smarthome.local/devices/water-valve/shutdown`) to automatically shut off the water supply, preventing further damage.

6. **Notify Homeowner**  
   Sends an SMS alert to the homeowner’s phone number using Twilio, informing them about the leak and the automatic response initiated.

7. **Notify Maintenance Service**  
   Sends an email notification to the maintenance service team with the alert message and full sensor data for immediate action.

---

## Node Details

### 1. Water Leak Sensor Webhook  
- **Type:** Webhook (POST)  
- **Path:** `/water-leak-sensor-webhook`  
- **Purpose:** Receives sensor data payloads.  
- **Expected Payload Example:**  
    ```json
    {
      "sensorId": "sensor-123",
      "waterLevel": 7,
      "homeownerPhone": "+1234567890"
    }
    ```

### 2. AI Anomaly Detection  
- **Type:** Function  
- **Logic:** Checks if `waterLevel` exceeds the threshold (`5`) and flags anomaly as water leak.  
- **Output:** JSON object with `isLeakDetected` boolean and original sensor data.

### 3. If Leak Detected  
- **Type:** If Conditional Node  
- **Condition:** Proceeds only if `isLeakDetected` is true.

### 4. Compose Alert Message  
- **Type:** Function  
- **Output:** Text message indicating the detected leak and sensor ID, e.g.,  
  `"Water leak detected at sensor sensor-123. Initiating automated response."`

### 5. Shut Off Water Valve  
- **Type:** HTTP Request (POST)  
- **URL:** `https://api.smarthome.local/devices/water-valve/shutdown`  
- **Authentication:** Bearer Token (replace `YOUR_SMARTHOME_API_TOKEN` with your smart home API token)  
- **Purpose:** Automatically cut water supply to affected area.

### 6. Notify Homeowner  
- **Type:** Twilio (SMS)  
- **Recipient:** Homeowner’s phone number from sensor data  
- **Message:** Alert message composed in the previous node  
- **Credentials:** Twilio API credentials required

### 7. Notify Maintenance Service  
- **Type:** Email Send  
- **Recipient:** `maintenance@smarthome.local`  
- **Subject:** `Urgent: Water Leak Detected`  
- **Body:** Includes alert message and JSON string of original sensor data

---

## Setup and Configuration

1. **Deploy Workflow** to your n8n instance.

2. **Configure Webhook URL**  
   Expose and secure the endpoint `/water-leak-sensor-webhook` for receiving POST requests from water leak sensors.

3. **Replace API Tokens and Credentials:**  
   - Update `YOUR_SMARTHOME_API_TOKEN` with your smart home system API token.  
   - Add your Twilio credentials in the designated credentials section.

4. **Configure Notification Recipients:**  
   - Ensure homeowner phone numbers are supplied in sensor webhook payloads.  
   - Confirm the maintenance service email address is correct.

5. **Test the System**  
   Send test sensor data exceeding the leak threshold to verify detection, valve shutdown, and notifications.

---

## Data Flow Summary

```
Sensor POST → Water Leak Sensor Webhook → AI Anomaly Detection → If Leak Detected → Compose Alert Message → [Shut Off Water Valve, Notify Homeowner (SMS), Notify Maintenance (Email)]
```

---

## Notes

- The AI anomaly detection is currently simulated using a static threshold check but can be replaced or enhanced with actual AI/ML services for improved accuracy.
- Ensure secure handling of all API credentials and sensitive data.
- Modify thresholds and notification logic as needed based on sensor capabilities and homeowner preferences.

---

## License

This workflow template is provided as-is. Adapt and extend according to your smart home system requirements.