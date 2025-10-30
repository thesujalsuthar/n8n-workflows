# AI-Powered Sustainable Fashion Outfit Recommender

This n8n workflow provides a sustainable fashion outfit recommendation tailored to a user's preferences, location, and current weather by leveraging OpenAI's GPT-4.

---

## Workflow Overview

The workflow receives user input via a webhook, processes the data, generates an AI-powered personalized eco-friendly outfit recommendation, and returns the output.

---

## Nodes Description

### 1. Start
- The entry point of the workflow.

### 2. Webhook
- **Type:** HTTP POST endpoint.
- **Path:** `/recommend-outfit`
- **Purpose:** Accepts incoming requests containing user data.
- **Details:**
  - Waits for a POST request at `/recommend-outfit`.
  - Receives JSON including user preferences, location, and weather data.
  - Response is sent immediately when results are received.

### 3. Extract User Data
- **Type:** Function node.
- **Purpose:** Parses and extracts relevant information (`userPreferences`, `location`, `weather`) from the incoming request JSON.
- **Function Code:**
  ```javascript
  const userPreferences = $json.userPreferences || {};
  const location = $json.location || {};
  const weather = $json.weather || {};

  return [{ json: { userPreferences, location, weather } }];
  ```

### 4. Generate Outfit Recommendation
- **Type:** OpenAI (GPT-4) node.
- **Purpose:** Generates a detailed, personalized sustainable outfit suggestion.
- **Settings:**
  - Model: `gpt-4`
  - Temperature: `0.7` (balanced creativity)
  - Max Tokens: `600`
  - Prompt Template:
    ```
    You are a sustainable fashion expert AI assistant.

    Given the user's preferences, location, and current weather, create a personalized eco-friendly outfit recommendation. Consider:
    - Sustainable fashion principles: use of organic, recycled, or eco-friendly materials
    - Weather appropriate attire
    - User's style, comfort, and preferences

    Details Provided:
    User Preferences: {{ $json.userPreferences | json }}
    Location: {{ $json.location | json }}
    Weather: {{ $json.weather | json }}

    Please generate a detailed outfit recommendation with explanations on sustainability and style.
    ```

### 5. Format Output
- **Type:** Function node.
- **Purpose:** Formats the response from OpenAI into a JSON with a `recommendation` field.
- **Function Code:**
  ```javascript
  return [{
    json: {
      recommendation: $json.choices[0].message.content
    }
  }];
  ```

---

## How to Use

1. **Send a POST request** to the webhook endpoint `/recommend-outfit` with a JSON payload containing:

   ```json
   {
     "userPreferences": {
       "style": "casual",
       "colors": ["blue", "green"],
       "comfortLevel": "high",
       "materialPreferences": ["organic cotton", "recycled polyester"]
     },
     "location": {
       "city": "San Francisco",
       "country": "USA",
       "latitude": 37.7749,
       "longitude": -122.4194
     },
     "weather": {
       "temperature": 18,
       "condition": "cloudy",
       "humidity": 75
     }
   }
   ```

2. **Receive an eco-friendly outfit recommendation** tailored to the above inputs, including sustainable material suggestions and style considerations.

---

## Summary

This workflow automates personalized and sustainable fashion recommendations by:

- Accepting user-specific data about preferences, location, and weather
- Extracting and sanitizing the input
- Utilizing GPT-4 with a specialized prompt focused on sustainability principles
- Returning a well-structured, style and environment-conscious outfit suggestion

Enable the workflow to make it active and ready to receive requests on your n8n instance.