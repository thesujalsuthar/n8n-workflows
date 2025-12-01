# EcoAware Daily Personalized Sustainability Tip Scheduler

This workflow is designed to send daily personalized sustainability tips to users based on their preferences and the current environmental conditions in Los Angeles. It executes every day at 8:00 AM, collecting real-time environmental data and user preferences to generate and deliver relevant tips via the user's preferred communication channel (email or SMS).

---

## Workflow Components

### 1. Daily Trigger 8AM  
- **Node Type:** Cron Trigger  
- **Description:** Initiates the workflow every day at 8:00 AM.

### 2. Set Current Date  
- **Node Type:** Function  
- **Description:** Sets the current date in ISO format (YYYY-MM-DD) to be used later in the workflow.

### 3. Fetch Environmental Data  
- **Node Type:** HTTP Request  
- **API:** OpenAQ API - Latest air quality data for Los Angeles (parameter: PM2.5).  
- **Description:** Retrieves the latest PM2.5 air quality measurement to assess the air quality condition for the day.

### 4. Get User Preferences  
- **Node Type:** HTTP Request  
- **API:** EcoAware User Preferences API  
- **Authentication:** Bearer token using API key from credentials.  
- **Description:** Retrieves user-specific preferences, including preferred tip category, contact info (email and phone), and preferred communication channel.

### 5. Generate Personalized Tip  
- **Node Type:** Function  
- **Inputs:**  
  - Environmental data (PM2.5 value)  
  - User preferences (category, email, phone, preferred channel)  
- **Logic:**  
  - Extracts current PM2.5 value and categorizes air quality as *good*, *moderate*, or *poor* based on EPA standards:  
    - Good: PM2.5 ≤ 12  
    - Moderate: PM2.5 ≤ 35.4  
    - Poor: PM2.5 > 35.4  
  - Selects a sustainability tip message based on the user's preferred category (`energy`, `water`, `waste`, or `general`).  
  - Appends additional advice depending on the air quality condition.  
- **Output:** JSON containing user contact info, preferred channel, and the personalized tip message.

### 6. Route by Preferred Channel  
- **Node Type:** Function  
- **Description:** Routes the message to be sent either via Email or SMS based on the user’s preferred communication channel.

### 7. Send Email Notification  
- **Node Type:** Email Send  
- **Details:**  
  - From: no-reply@ecoaware.com  
  - To: User’s email from preferences  
  - Subject: "Your Daily Sustainability Tip 🌿"  
  - Email Body: Personalized tip message  

### 8. Send SMS Notification  
- **Node Type:** Twilio  
- **Credentials:** Twilio API  
- **Details:**  
  - From: ECOAWARE  
  - To: User’s phone number from preferences  
  - Message Body: Personalized tip message  

---

## Execution Flow

1. **Trigger:** The workflow starts at 8:00 AM daily.  
2. **Set Date:** Captures the current date for reference.  
3. **Data Fetch:**  
   - Retrieves latest air quality data for Los Angeles.  
   - Retrieves the user’s preferences from EcoAware API.  
4. **Tip Generation:** Combines environmental data and user preferences to generate a personalized sustainability tip.  
5. **Routing:** Determines whether to send the tip via email or SMS based on user preference.  
6. **Delivery:** Sends the tip by the chosen channel.

---

## Configuration Requirements

- **EcoAware API Credential:**  
  - API Key stored in credentials as `EcoAwareApi.apiKey` for authentication to fetch user preferences.

- **Twilio API Credential:**  
  - Twilio account SID, Auth Token, and phone number configured in the `Twilio API` credential for sending SMS.

- **Email Setup:**  
  - Ensure SMTP settings are correctly configured to send emails from `no-reply@ecoaware.com`.

---

## Supported Tip Categories

- **energy:** Tips related to energy conservation.  
- **water:** Tips on water-saving practices.  
- **waste:** Waste management and recycling tips.  
- **general:** General sustainability tips for eco-friendly behavior.

---

## Air Quality Levels & Messaging

| Air Quality Level | PM2.5 Range (μg/m³) | Additional Tip Message                                                                      |
|-------------------|---------------------|---------------------------------------------------------------------------------------------|
| Good              | ≤ 12                | "Air quality is good today; it is a perfect day to enjoy nature responsibly!"               |
| Moderate          | 12.1 - 35.4         | "The air quality is moderate today; consider wearing a mask if you have respiratory issues."|
| Poor              | > 35.4              | "Also, since air quality is poor today, consider limiting outdoor activities to reduce exposure."|

---

## Notes

- The workflow assumes environmental data is available for Los Angeles via the OpenAQ API.  
- The user preferences endpoint requires a valid API key and returns JSON with keys such as `email`, `phone`, `category`, and `preferredChannel`.  
- If `preferredChannel` is not set by the user, email is used as the default channel.  
- The tip messages are static for each category but dynamically augmented based on air quality data.  

---

## License

This project is provided as-is for use with the EcoAware platform and n8n automation workflows. Modify as needed for personal or organizational use.