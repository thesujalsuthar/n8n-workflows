# AI-Powered Sustainable Fashion Outfit Recommender

This workflow leverages AI and real-time weather data to provide personalized sustainable fashion outfit recommendations tailored to the user's preferences, current weather conditions, and eco-friendly guidelines. Recommendations are sent to the user via email and messaging platforms.

---

## Workflow Overview

1. **Set Test Input**  
   Initializes sample user data including style preferences, occasion, location, email, and chat ID.  
   - User Preferences: style, preferred colors, and avoided materials  
   - Occasion: e.g., "weekend outing"  
   - Location: e.g., "San Francisco"  
   - Contact info: email and chat ID for notifications  

2. **Fetch Weather Data**  
   Retrieves current weather information for the user's location via the WeatherAPI.  
   - API Endpoint: `https://api.weatherapi.com/v1/current.json`  
   - Query Parameters:  
     - `key`: API key from environment variable `WEATHER_API_KEY`  
     - `q`: User location from input data  

3. **Prepare Context Data**  
   Combines user preferences, occasion, fetched weather data, and predefined eco-friendly fashion guidelines into a unified context object to be passed to the AI assistant.  
   - Eco Guidelines include:  
     - Sustainable materials: organic cotton, recycled polyester, linen, hemp  
     - Natural dyes  
     - Certifications: GOTS, Fair Trade, Bluesign  

4. **Generate Outfit Recommendations**  
   Uses OpenAI GPT-4o-mini with a specialized system prompt to act as an AI stylist focused on sustainable fashion.  
   - Inputs: User preferences, occasion, current weather, and eco guidelines  
   - Outputs:  
     - Personalized outfit components  
     - Explanation of sustainability score  
     - Links to sustainable brands or products  

5. **Send Email Notification**  
   Sends an email to the user containing the AI-generated sustainable outfit recommendations.  
   - From: `no-reply@sustainablefashion.ai`  
   - To: User's email address from input data  
   - Subject: "Your Personalized Sustainable Outfit Recommendations"  
   - Body: Friendly message with the outfit recommendations  

6. **Send Messaging Notification**  
   Sends the same personalized recommendations as a message through Telegram using the user's chat ID.  
   - Chat ID: From user input  
   - Message: Contains greeting and outfit details  

---

## Nodes Description

### Set Test Input
- Type: Function  
- Purpose: Provide sample user data to start the workflow.  
- Outputs an object with user preferences, occasion, location, email, and chat ID.

### Fetch Weather Data
- Type: HTTP Request  
- Purpose: Access current weather data for the user’s location via WeatherAPI.  
- Uses environment variable `WEATHER_API_KEY` for authorization.  

### Prepare Context Data
- Type: Function  
- Purpose: Aggregate user data, weather info, and sustainability guidelines into a single object.  
- Outputs a consolidated JSON for the AI prompt.

### Generate Outfit Recommendations
- Type: OpenAI ChatCompletion  
- Purpose: Generate personalized sustainable fashion outfit suggestions.  
- Model: GPT-4o-mini  
- System prompt instructs the AI to focus on sustainability, including materials, occasion, weather, and certifications.

### Send Email Notification
- Type: Email Send  
- Purpose: Email the outfit recommendations to the user.

### Send Messaging Notification
- Type: Telegram  
- Purpose: Send the same recommendations as a Telegram message to the user’s chat.

---

## Environment Variables

- `WEATHER_API_KEY` — API key for accessing WeatherAPI. Required for fetching current weather data.

---

## Running the Workflow

1. Set the `WEATHER_API_KEY` in your environment.
2. Trigger the workflow (starting from **Set Test Input**).
3. The workflow will fetch weather, prepare data, generate recommendations, and notify the user by email and Telegram.

---

## Sample Input Example

```json
{
  "userPreferences": {
    "style": "bohemian",
    "colors": ["earth tones", "green", "beige"],
    "avoidedMaterials": ["leather", "synthetic fibers"]
  },
  "occasion": "weekend outing",
  "userLocation": "San Francisco",
  "userEmail": "user@example.com",
  "userChatId": "123456789"
}
```

---

## Conclusion

This workflow automates delivering eco-conscious, weather-appropriate outfit recommendations personalized for each user, promoting sustainable fashion choices through AI-driven insights and real-time data.