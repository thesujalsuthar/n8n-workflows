# AI-Driven Personalized Sustainability Tip Scheduler

This workflow automates the process of generating and sending personalized daily sustainability tips to users based on their lifestyle preferences, local environmental data, and up-to-date sustainability trends while logging user engagement for continuous optimization.

---

## Workflow Overview

The workflow consists of the following main components:

1. **Receive User Preferences**  
   Accepts user-submitted preferences via an HTTP POST request.

2. **Validate User Preferences**  
   Checks the submitted preferences for required fields such as lifestyle and location.

3. **Fetch Local Environmental Data**  
   Retrieves real-time local air quality data using the OpenWeatherMap Air Pollution API based on user's geographic coordinates.

4. **Generate Personalized Tip**  
   Calls the OpenAI GPT-4 API to generate a personalized sustainability tip leveraging:
   - User lifestyle preferences
   - Local environmental conditions (air quality)
   - Current sustainable living trends

5. **Extract Tip Text**  
   Extracts the generated tip content from the OpenAI API response.

6. **Schedule Next Tip Time**  
   Sets the scheduled time for sending the next daily tip, defaulting to 9 AM local time.

7. **Daily Trigger**  
   Fires daily at 9 AM to initiate the tip sending process.

8. **Prepare Email Content**  
   Formats the email with the personalized tip and recipient email address.

9. **Send Email Notification**  
   Sends the sustainability tip email via SMTP.

10. **Log User Engagement**  
    Creates a log entry capturing user email, sent tip, timestamp, and action for tracking purposes.

11. **Save Engagement to DB**  
    Inserts engagement logs into a MongoDB collection (`user_engagement_logs`).

12. **Fetch Engagement History**  
    Queries past engagement logs for the user from MongoDB.

13. **Optimize Tip Prompt Based on Engagement**  
    Adjusts the AI prompt for tip generation dynamically based on user engagement history to enhance relevance and engagement.

---

## Detailed Node Descriptions

### 1. Receive User Preferences (HTTP Trigger)  
- **Type:** HTTP POST trigger  
- **Path:** `/submitPreferences`  
- **Function:** Receives JSON payload containing user preferences including lifestyle, location (latitude & longitude), timezone, and email.

### 2. Validate User Preferences (Function Node)  
- Validates presence of essential data (`lifestyle` and `location`).  
- Throws an error if validation fails.

### 3. Fetch Local Environmental Data (HTTP Request)  
- Requests current air pollution data from OpenWeatherMap API using user location.  
- Requires OpenWeatherMap API Key via credential.

### 4. Generate Personalized Tip (HTTP Request)  
- Sends a chat completion request to OpenAI GPT-4.  
- Input includes user lifestyle, local air quality index, and latest sustainability trends.  
- API Key stored securely as OpenAI credential.

### 5. Extract Tip Text (Function Node)  
- Extracts and trims the sustainability tip from OpenAI's response JSON.

### 6. Schedule Next Tip Time (Function Node)  
- Calculates and stores next tip send time (9 AM user local time).  
- Defaults to UTC if user's timezone unknown.

### 7. Daily Trigger (Schedule Trigger)  
- Executes the tip sending process daily at 09:00.

### 8. Prepare Email Content (Function Node)  
- Structures the email content with user email and generated tip.

### 9. Send Email Notification (Email Send)  
- Sends tip to user's email using SMTP credentials.  
- From address: `no-reply@sustainabilitytip.com`.

### 10. Log User Engagement (Function Node)  
- Captures engagement details for storage and analysis.

### 11. Save Engagement to DB (MongoDB Node)  
- Stores the engagement log into MongoDB's `user_engagement_logs` collection.  
- Uses MongoDB credentials.

### 12. Fetch Engagement History (MongoDB Node)  
- Retrieves past engagement data for the user by email to inform personalization improvements.

### 13. Optimize Tip Prompt Based on Engagement (Function Node)  
- Analyzes engagement frequency.  
- Modifies AI prompt to increase tip engagement if user engagement is low.

---

## Required Credentials

- **OpenWeatherMap API Key:** For fetching local environmental data  
- **OpenAI API Key:** For AI-driven tip generation (GPT-4)  
- **SMTP Credentials:** For sending email notifications  
- **MongoDB Credentials:** For storing and querying user engagement logs

---

## How It Works

1. Users submit their sustainability lifestyle preferences and location to the `/submitPreferences` endpoint.
2. The workflow validates the input and fetches current local air quality data.
3. It then calls GPT-4 to generate a personalized tip incorporating user preferences, local data, and sustainability trends.
4. The tip is scheduled for daily delivery at 9 AM user's local time.
5. Each day, a scheduled trigger sends the email containing the personalized tip.
6. User engagement is logged and saved to MongoDB.
7. The engagement history is analyzed to continuously optimize future tip generation prompts.

---

## Customization and Extensions

- Adjust the scheduled send time by modifying the `sendHour` variable in the **Schedule Next Tip Time** node.
- Extend environmental data sources or integrate additional user preferences for deeper personalization.
- Enhance email templates or add alternative notification channels (e.g., SMS, push notifications).
- Use engagement data insights to implement advanced machine learning for tip relevancy improvements.

---

## License

This workflow is provided as-is for personal or educational purposes. Modify and use according to your project requirements.

---

End of README.md content.