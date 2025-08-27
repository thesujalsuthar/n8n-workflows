# AI-Driven Real-Time Public Transit Delay Prediction and Commuter Notification System

This workflow implements a real-time system to predict public transit delays using AI and notify commuters based on their preferences through email, SMS, or messaging apps.

---

## Workflow Overview

The workflow consists of the following main steps:

1. **Fetch Real-Time Transit Data**  
   Retrieves live public transit data from the API endpoint:  
   `https://api.publictransitdata.com/realtime`  
   The response is expected in JSON format.

2. **Clean and Format Data**  
   Processes and structures the raw transit data into a clean, consistent format suitable for AI model consumption. Key attributes extracted include route ID, trip ID, timestamps, stop IDs, scheduled and actual arrival times, delay in seconds, and vehicle ID.

3. **AI Delay Prediction**  
   Sends the cleaned data to an AI prediction API (`https://api.your-aipredictor.com/predictTransitDelay`) with authentication via an API key in headers. The AI returns predicted delay durations in seconds for each transit segment.

4. **Filter Significant Delays**  
   Filters out insignificant delays, passing forward only predictions with delays greater than 120 seconds (2 minutes) for notifications.

5. **Attach User Preferences**  
   Maps each delay event to commuter preferences (email address, phone number, preferred notification channel) based on `tripId` or `routeId`. These preferences determine how users will be notified.

6. **Determine Notification Channel**  
   Determines the notification channel (`Email`, `SMS`, or `MessagingApp`) for each user based on their preferences, marking the item accordingly to route it to the correct notification path.

7. **Notification Routing and Sending**  
   Based on the chosen channel, the workflow splits into three conditional branches:

   - **Email Channel**:  
     Sends an email using SMTP with delay details.  
     Example subject:  
     `Transit Delay Alert: Route [routeId] Trip [tripId]`  
     Email content notifies about predicted delay seconds and advises the commuter to plan accordingly.

   - **SMS Channel**:  
     Sends an SMS via Twilio to the user's phone number with a brief delay alert message.

   - **Messaging App Channel**:  
     Sends a messaging app notification (e.g., Telegram) to the user's chat ID with the delay alert.

---

## Nodes Detail

### 1. Fetch Real-Time Transit Data  
- **Type:** HTTP Request  
- **Function:** Retrieves real-time JSON transit data from the public transit API.

### 2. Clean and Format Data  
- **Type:** Function  
- **Function:** Cleans API raw data, ensures all required fields are present or defaulted, and formats for AI input.

### 3. AI Delay Prediction  
- **Type:** HTTP Request  
- **Function:** Sends cleaned data to AI prediction service with authentication. Receives predicted delay in seconds.

### 4. Filter Significant Delays  
- **Type:** Function  
- **Function:** Filters results to only include delays over 2 minutes (120 seconds).

### 5. Attach User Preferences  
- **Type:** Function  
- **Function:** Uses sample hardcoded preferences mapping to attach user contact info and notification channel per trip or route.

### 6. Determine Notification Channel  
- **Type:** Function  
- **Function:** Adds a `notificationChannel` field based on user preferences to route notifications accordingly.

### 7. IF Nodes (Email, SMS, Messaging App)  
- **Type:** IF nodes  
- **Function:** Branches the workflow into the correct notification type.

### 8. Send Email Notification  
- **Type:** Email Send  
- **Function:** Sends email alerts using configured SMTP credentials.

### 9. Send SMS Notification  
- **Type:** Twilio  
- **Function:** Sends SMS alerts using configured Twilio credentials.

### 10. Send Messaging App Notification  
- **Type:** Telegram  
- **Function:** Sends messaging app alerts using configured Telegram API credentials.

---

## Setup Instructions

1. **API Keys & Credentials**  
   - Replace `YOUR_AI_API_KEY` with your AI prediction service API key in the "AI Delay Prediction" node.  
   - Configure SMTP credentials in "Send Email Notification" node for sending emails.  
   - Add your Twilio API credentials in "Send SMS Notification" node to enable SMS messaging.  
   - Set Telegram API credentials in "Send Messaging App Notification" node for in-app notifications.

2. **User Preferences**  
   Adjust the user preferences mapping inside the "Attach User Preferences" function node to reflect actual commuter contact data and preferred notification methods.

3. **API Endpoint Customization**  
   Update `https://api.publictransitdata.com/realtime` to the correct real-time data source if necessary.

4. **Threshold Customization**  
   Modify delay filtering threshold (`120` seconds) in "Filter Significant Delays" as needed.

---

## Notification Messages Template

- **Email Subject:**  
  `Transit Delay Alert: Route [routeId] Trip [tripId]`

- **Email Body:**  
  ```
  Dear Commuter,

  There is a predicted delay of [predictedDelaySeconds] seconds on your transit route [routeId] (Trip ID: [tripId]).

  Please plan accordingly.

  Thank you,
  Transit Alerts Team
  ```

- **SMS Message:**  
  ```
  Transit Delay Alert: Delay of [predictedDelaySeconds] sec on Route [routeId] (Trip [tripId]). Please adjust your plans accordingly.
  ```

- **Messaging App Message:**  
  ```
  Transit Delay Alert: Delay of [predictedDelaySeconds] seconds on Route [routeId] (Trip [tripId]). Please stay informed.
  ```

---

## Workflow Visualization

The workflow proceeds linearly from fetching data -> cleaning -> AI prediction -> filtering -> user attachment -> channel determination -> conditional routing -> notification sending.

---

## Notes

- Ensure all credential nodes (SMTP, Twilio, Telegram) are properly set up with valid credentials.  
- Customize user preferences for real-world user management or integrate with a live database for scalability.  
- The AI prediction API must conform to expected input/output schema for seamless integration.

---

This system enables transit authorities or service providers to proactively inform commuters, improving their experience and operational transparency through AI-driven insights and automated notification delivery.