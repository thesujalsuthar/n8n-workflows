# EcoAware Daily Sustainability Tip Delivery Workflow

This workflow automates the delivery of personalized daily sustainability tips to users via email or messaging platforms, based on their preferences and current environmental data.

---

## Workflow Overview

The workflow runs every day at 9:00 AM and performs the following steps:

1. **Daily Trigger**  
   Triggers the workflow execution once daily at 9:00 AM.

2. **Get Users**  
   Retrieves a list of users along with their preferences, email, messaging platform, and location from an authenticated API or database.

3. **Extract User Info**  
   Processes the raw user data to extract key fields: user ID, email, messaging platform, preferences, and location.

4. **Prepare Environmental Query**  
   Normalizes the user's location data to ensure it is ready for querying environmental information. Defaults to global coordinates if location is not present.

5. **Get Environmental Data**  
   Fetches current weather information relevant to the user's location using the [Open-Meteo API](https://open-meteo.com/).

6. **Combine Data**  
   Merges user preferences and environmental data into a single dataset per user.

7. **Generate Sustainability Tip**  
   Uses the OpenAI GPT-4o-mini model to generate a concise, actionable sustainability tip personalized to the user's preferences and local environmental conditions.

8. **Extract Tip Text**  
   Parses the AI-generated response to pull out the tip content.

9. **Split by Communication Method**  
   Determines the preferred communication channels for each user (email and/or messaging platform) and separates the data accordingly.

10. **Send Email / Send Message**  
    Sends the personalized sustainability tip either via email or messaging platform based on the user's available contact methods.

---

## Nodes Description

### 1. Daily Trigger  
- **Type:** Schedule Trigger  
- **Trigger Time:** 9:00 AM daily  
- **Function:** Starts the workflow every day at the specified time.

### 2. Get Users  
- **Type:** HTTP Request  
- **Operation:** List users from an API endpoint  
- **Authentication:** HTTP Basic Auth (configured via credentials)  
- **Purpose:** Pulls user data including preferences, contact info, and location.

### 3. Extract User Info  
- **Type:** Function  
- **Function:** Extracts relevant data fields from the user list: `userId`, `email`, `messagingPlatform`, `preferences`, and `location`.

### 4. Prepare Environmental Query  
- **Type:** Function  
- **Function:** Sets location for environmental query, defaults to latitude 40.7128 and longitude -74.006 if user location does not exist.

### 5. Get Environmental Data  
- **Type:** HTTP Request  
- **Endpoint:** `https://api.open-meteo.com/v1/forecast`  
- **Parameters:** latitude, longitude, current_weather=true  
- **Purpose:** Retrieves current weather data for user’s location.

### 6. Combine Data  
- **Type:** Function  
- **Function:** Combines user ID, preferences, location, and environmental data into a single entry per user.

### 7. Generate Sustainability Tip  
- **Type:** OpenAI  
- **Model:** `gpt-4o-mini`  
- **Temperature:** 0.7  
- **Max Tokens:** 300  
- **Prompt:**  
  - System instructs the model to act as an AI assistant generating personalized sustainability tips.  
  - User content includes user's preferences, local environmental data, and location.  
- **Purpose:** Creates a personalized sustainability tip for each user.

### 8. Extract Tip Text  
- **Type:** Function  
- **Function:** Extracts the message content (tip) from the OpenAI response.

### 9. Split by Communication Method  
- **Type:** Function  
- **Function:** Checks if users have email, messaging platform, or both, and prepares the output for corresponding sending nodes.

### 10. Send Email  
- **Type:** Email Send  
- **To:** User's email  
- **Subject:** "Your EcoAware Daily Sustainability Tip"  
- **Body:** Includes the personalized tip and a friendly sign-off.  
- **Credentials:** SMTP configured credentials.

### 11. Send Message  
- **Type:** Messaging Platform Send Message  
- **Channel:** User's messaging platform channel ID  
- **Message:** Sends the personalized sustainability tip with a friendly greeting.  
- **Credentials:** Messaging Platform API credentials.

---

## Credentials Required

- **UserAPI Credentials:** For authenticating API requests to retrieve users.  
- **OpenAI API:** For interacting with the GPT-4o-mini language model.  
- **SMTP Credentials:** For sending emails to users.  
- **Messaging Platform API:** For sending messages via the user's chosen platform.

---

## How to Use

1. Configure the API credentials for user retrieval, OpenAI, SMTP, and Messaging Platform nodes.  
2. Adjust user API endpoint and authentication as necessary.  
3. Ensure user data includes preferences, contact details, and location coordinates for accurate environmental querying.  
4. Deploy the workflow in your n8n instance.  
5. The workflow will automatically run daily at 9:00 AM, sending personalized sustainability tips to users via their preferred communication method.

---

## Notes

- The environmental data defaults to New York City coordinates if user location is missing.  
- The AI-generated tips aim to be actionable and concise based on real-time environmental data and user preferences.  
- Both email and messaging platform delivery options are supported; users may receive tips via one or both channels.