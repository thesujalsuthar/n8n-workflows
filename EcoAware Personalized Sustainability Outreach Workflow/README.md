# EcoAware Daily Sustainability Tips Workflow

This workflow automates the process of sending personalized daily sustainability tips and calls to action to users based on their preferences and local environmental data. It leverages MongoDB for user data storage, external APIs for environmental information, OpenAI for content generation, and supports delivery via email and SMS.

---

## Workflow Overview

1. **Daily Trigger**  
   Runs every day at 08:00 AM to start the workflow.

2. **Get Users**  
   Retrieves all users from the `users` collection in MongoDB.

3. **Prepare Users**  
   Processes user data to extract relevant fields, including location (city and country), contact info, and preferences.

4. **Fetch Environmental Data**  
   For each user:
   - Fetches the latest **air quality data** for the user's city from the OpenAQ API.
   - Fetches current **weather data** for the user’s city and country from the OpenWeatherMap API.

5. **Combine Environmental Data**  
   Merges the air quality and weather data into a unified structure attached to each user’s data.

6. **Generate Personalized Message**  
   Uses OpenAI's GPT-4 model with the "EcoAware AI" prompt to generate a personalized, friendly daily sustainability tip and a call to action based on the user’s preferences and current environmental data.

7. **Parse AI Response**  
   Parses the JSON response from OpenAI. If parsing fails, defaults to a fallback tip and action.

8. **Craft Email/SMS Content**  
   Creates the message subject and body based on the user's engagement score and AI-generated tips.

9. **Filter Users by Contact Method**  
   Segregates users who prefer to be contacted by email or SMS.

10. **Send Notifications**  
    - Sends emails to users preferring email via SMTP.
    - Sends SMS messages to users preferring SMS via Twilio.

11. **Track Message Sent**  
    Updates or inserts a record in the `userEngagement` MongoDB collection to track the number of sent messages and the last sent timestamp.

12. **Update Engagement Score**  
    Adjusts the user's engagement score based on placeholder logic to simulate interaction feedback.

13. **Save Engagement Score**  
    Saves the updated engagement score back to MongoDB.

---

## Nodes Description

| Node Name                 | Type                   | Purpose                                                                                   |
|---------------------------|------------------------|-------------------------------------------------------------------------------------------|
| Daily Trigger             | Cron                   | Triggers the workflow daily at 08:00 AM.                                                 |
| Get Users                 | MongoDB                | Queries all users from the `users` collection.                                           |
| Prepare Users             | Function               | Extracts and enriches user info with location and preferences.                           |
| Fetch Air Quality Data    | HTTP Request           | Calls OpenAQ API to get air quality information based on user's city.                    |
| Fetch Weather Data        | HTTP Request           | Calls OpenWeatherMap API to get current weather data.                                   |
| Combine Environmental Data| Function               | Combines air quality and weather data into one object per user.                          |
| Generate Personalized Message | OpenAI              | Uses GPT-4 to generate personalized sustainability tips and calls to action.            |
| Parse AI Response         | Function               | Parses JSON response from OpenAI, with fallback on error.                               |
| Craft Email/SMS Content    | Function               | Formats subject and message body depending on engagement score and tips.                |
| Filter Email Users        | Function               | Filters users preferring email delivery.                                                |
| Filter SMS Users          | Function               | Filters users preferring SMS delivery.                                                  |
| Send Email                | Email Send             | Sends tip via email using SMTP credentials.                                             |
| Send SMS                  | Twilio                 | Sends tip via SMS using Twilio credentials.                                             |
| Track Message Sent        | MongoDB                | Updates/inserts message sent info in `userEngagement` collection.                       |
| Update Engagement Score   | Function               | Updates user engagement score (dummy logic).                                           |
| Save Engagement Score     | MongoDB                | Saves updated engagement score back to MongoDB.                                        |

---

## Credentials Required

- **MongoDB Credentials**  
  Access for querying and updating collections (`users`, `userEngagement`).

- **OpenWeatherMap API**  
  API key required for fetching current weather data.

- **OpenAI API**  
  GPT-4 API key for generating personalized messages.

- **SMTP Credentials**  
  For sending emails to users.

- **Twilio Account**  
  For sending SMS messages to users.

---

## Configuration Details

### Daily Trigger
- Runs at 8:00 AM every day.

### MongoDB Nodes
- `Get Users`: Queries all user documents.
- `Track Message Sent` & `Save Engagement Score`: Use `update` with `upsert` enabled to track engagement.

### HTTP Requests
- Air quality API: `https://api.openaq.org/v2/latest?city={{city}}`
- Weather API: `https://api.openweathermap.org/data/2.5/weather?q={{city}},{{country}}&appid={{apiKey}}&units=metric`

### OpenAI Node
- Model: GPT-4
- Prompt includes user preferences and environmental data.
- Expected output: JSON containing a `tip` and an `action`.

### Sending Messages
- Email and SMS messages vary subject line based on user engagement score.
- SMS and Email are sent according to user’s preferred contact method.

---

## Error Handling

- If the AI response JSON parsing fails, the workflow defaults to a generic sustainability tip and action.
- Placeholder logic is used for engagement score updating and can be replaced with actual analytics.

---

## How to Use

1. Import this workflow into your n8n instance.
2. Configure all required credentials:
   - MongoDB
   - OpenWeatherMap API
   - OpenAI API
   - SMTP email
   - Twilio
3. Adjust user preferences and ensure location data (city and country) is populated.
4. Activate the workflow.
5. Users will receive personalized daily sustainability tips via their preferred contact method each day at 8 AM.

---

Harness the power of AI and real-time environmental data to keep your user base engaged with meaningful, actionable sustainability advice daily!