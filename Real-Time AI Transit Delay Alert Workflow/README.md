# AI-Powered Real-Time Public Transit Delay Alert System

This workflow provides an automated system to monitor public transit delays in real-time and sends alerts to users based on their preferences. It leverages live transit data from an API, applies configurable filters, generates clear alert messages, and notifies users via email, SMS, and push notifications.

---

## Workflow Overview

The workflow comprises seven nodes orchestrated in sequence to fetch, process, and distribute transit delay alerts.

### Nodes Description

#### 1. Mock User Preferences
- **Type:** Function  
- **Purpose:** Simulates user preferences input such as transit lines to monitor, delay threshold in minutes, and contact details (email and phone number).  
- **Details:**  
  ```json
  {
    lines: ["Red Line", "Blue Line"],
    delayThresholdMinutes: 5,
    email: "user@example.com",
    phoneNumber: "+1234567890"
  }
  ```

#### 2. Fetch Live Transit Data
- **Type:** HTTP Request  
- **Purpose:** Retrieves real-time transit delay data from the endpoint:  
  `https://api.publictransit.example.com/v1/delays`  
- **Query Parameters:** Filters lines based on the user’s preferences passed from the previous node (`lines` parameter).  
- **Response Format:** JSON

#### 3. Filter Significant Delays
- **Type:** Function  
- **Purpose:** Filters the transit delays based on user-defined threshold and preferred lines.  
- **Logic:**  
  - Extract delays with a delay time greater than or equal to the threshold (default 5 minutes).  
  - Only includes delays for specified lines if user has defined any; otherwise, includes all lines.

#### 4. Generate Alert Messages
- **Type:** Function  
- **Purpose:** Constructs user-friendly alert messages for each filtered delay.  
- **Message Format:**  
  ```
  Delay Alert: Line <Line Name> is delayed by <Delay Minutes> minutes at stop <Stop Name>. Expected arrival now at <New Arrival Time>.
  ```

#### 5. Send Email Notification
- **Type:** Email Send  
- **Purpose:** Sends delay alert messages to the user's email.  
- **From:** alerts@transitalerts.example.com  
- **To:** User’s email from preferences  
- **Subject:** Transit Delay Alert  
- **Content:** The generated alert message text

#### 6. Send SMS Notification
- **Type:** Twilio  
- **Purpose:** Sends SMS notifications with delay alerts to the user's phone number.  
- **Phone Number:** From user preferences  
- **Message:** The generated alert message text

#### 7. Send Push Notification
- **Type:** Push Notification  
- **Purpose:** Sends push notifications to user's device.  
- **Notification Title:** Transit Delay Alert  
- **Notification Body:** The generated alert message text

---

## Workflow Execution Flow

1. `Mock User Preferences` node initializes user input data.
2. Preferences are passed to `Fetch Live Transit Data` node to request transit delay info.
3. The fetched data is filtered by `Filter Significant Delays` within user-defined constraints.
4. `Generate Alert Messages` creates personalized alert messages.
5. Alerts are simultaneously sent via `Send Email Notification`, `Send SMS Notification`, and `Send Push Notification`.

---

## Customization

- **User Preferences:**  
  In the `Mock User Preferences` node, update the following fields as needed:  
  - `lines`: List of transit lines to monitor (e.g., `["Red Line"]`)  
  - `delayThresholdMinutes`: Minimum delay (in minutes) to trigger an alert  
  - `email`: Recipient email address  
  - `phoneNumber`: Recipient phone number in E.164 format (e.g., `+1234567890`)

- **API Endpoint:**  
  Replace `https://api.publictransit.example.com/v1/delays` with your actual transit data provider API if different.

- **Notification Channels:**  
  Configure email, SMS (Twilio), and push notifications with valid credentials and service setup.

---

## Prerequisites & Setup

- Access to a public transit delay API that returns JSON data.
- Email sending service configured (SMTP or other supported by your platform).
- Twilio account configured with phone number and credentials for SMS.
- Push notification service set up to send to user devices.
- Workflow platform capable of running HTTP requests, JavaScript functions, email, SMS, and push notification nodes.

---

## Notes

- The workflow is designed for modularity; you may add more notification channels or modify filtering logic as per requirements.
- Ensure user preferences are securely handled and stored if adapted to production environments.
- Testing with mock preferences node allows easy simulation without external inputs.

---

## Version

- Workflow Version: 1.0  
- Created On: 2024

---

Thank you for using the AI-Powered Real-Time Public Transit Delay Alert System!