# Air Quality Monitoring and Alert Workflow

This workflow automates the monitoring of air quality sensor data in a smart home environment, analyzes the readings against configurable thresholds, sends alerts via multiple channels when poor air quality is detected, and activates an air purifier automatically.

---

## Workflow Overview

1. **Start**  
   Initiates the workflow execution.

2. **Set User Configurable Parameters**  
   Defines thresholds for air quality parameters and user contact details for notifications.  
   Parameters include:  
   - PM2.5 threshold (µg/m³)  
   - CO2 threshold (ppm)  
   - VOC threshold (ppb)  
   - Humidity lower and upper thresholds (%)  
   - User email, phone number, and push notification ID  
   - Smart air purifier device ID

3. **Get Air Quality Sensor Data**  
   Retrieves data from the air quality sensor device using an authenticated HTTP request. It collects all available sensor data points related to air quality.

4. **Analyze Air Quality Data**  
   Processes the sensor data to compare current values against thresholds. It evaluates:  
   - PM2.5 levels  
   - CO2 levels  
   - VOC levels  
   - Humidity (too low or too high)  
   
   Generates alert messages for any values exceeding thresholds and flags if any alert condition is met.

5. **Check Alert Condition**  
   Checks whether any alert condition was triggered during data analysis.

6. **Send Alerts and Activate Air Purifier** _(Executed only if alert conditions are true)_  
   - **Send Email Alert**: Sends an email to the configured user email address with all alert messages.  
   - **Send SMS Alert**: Sends an SMS notification via Twilio to the configured phone number.  
   - **Send Push Notification**: Sends a push notification via Pushbullet to the user's device.  
   - **Activate Air Purifier**: Sends a request to turn the air purifier device to "auto" mode and powers it on.

---

## Node Details

### 1. Start
- Type: Start node  
- Purpose: Entry point of the workflow.

### 2. Set User Configurable Parameters
- Type: Set  
- Purpose: Defines the following parameters that can be customized by the user:  
  - `pm25` (number, default: 35) - PM2.5 threshold in µg/m³  
  - `co2` (number, default: 1000) - CO2 threshold in ppm  
  - `voc` (number, default: 500) - VOC threshold in ppb  
  - `humidityLow` (number, default: 30) - Minimum humidity threshold in %  
  - `humidityHigh` (number, default: 60) - Maximum humidity threshold in %  
  - `userEmail` (string, default: user@example.com) - Email for alerts  
  - `userPhone` (string, default: +1234567890) - Phone number for SMS alerts  
  - `userPushId` (string, default: user_push_id) - User ID for push notifications  
  - `airPurifierId` (string, default: purifier_device_id) - Smart air purifier device ID  

### 3. Get Air Quality Sensor Data
- Type: HTTP Request  
- Authentication: OAuth2  
- Operation: Get data from device resource for device type `airQualitySensor`  
- Returns: All sensor data with no filters applied

### 4. Analyze Air Quality Data
- Type: Function  
- Purpose: Analyzes the sensor readings for PM2.5, CO2, VOC, and humidity.  
- Logic:  
  - Compares each pollutant level to its threshold  
  - Adds alert messages if values exceed thresholds  
  - Flags `alertTriggered` if any alert exists  
- Output: JSON data with sensor values, array of alerts, and alert status

### 5. Check Alert Condition
- Type: If  
- Condition: Checks if `alertTriggered` is equal to `true`  
- True branch triggers alert outbound actions and air purifier activation  
- False branch ends workflow silently

### 6. Send Email Alert
- Type: Email Send  
- Parameters:  
  - From: your-email@example.com  
  - To: User configurable email address (`userEmail`)  
  - Subject: "Air Quality Alert - Smart Home"  
  - Text: Concatenated alert messages separated by new lines

### 7. Send SMS Alert
- Type: Twilio  
- Parameters:  
  - Message: Concatenated alert messages   
  - Phone Number: User configurable phone number (`userPhone`)

### 8. Send Push Notification
- Type: Pushbullet  
- Parameters:  
  - Message: Concatenated alert messages  
  - Title: "Air Quality Alert"  
  - User IDs: Array containing the user push notification ID (`userPushId`)

### 9. Activate Air Purifier
- Type: HTTP Request  
- Authentication: OAuth2  
- Operation: Update device resource for device type `airPurifier`  
- Parameters:  
  - Device ID: User configurable air purifier ID (`airPurifierId`)  
  - Data: Sets mode to "auto" and power to "on" to activate purifier

---

## How to Use

1. **Configure OAuth2 Credentials** for access to device APIs and Twilio/Pushbullet integrations.
2. **Customize User Parameters** in the "Set User Configurable Parameters" node for your environmental thresholds and contact information.
3. **Deploy the Workflow** in n8n and trigger it manually or schedule it for periodic execution according to your monitoring preferences.
4. **Receive Alerts** on exceeding threshold levels via email, SMS, and push notifications.
5. **Automatic Activation** of the smart air purifier to improve indoor air quality when necessary.

---

## Requirements

- n8n automation platform  
- Authorized access to air quality sensor and smart air purifier APIs with OAuth2 credentials  
- Twilio account for SMS sending  
- Pushbullet account for push notifications  
- Email sending capability configured in n8n

---

This workflow ensures your smart home environment maintains safe and healthy air quality by constantly monitoring sensors, notifying you without delay, and activating purification systems automatically.