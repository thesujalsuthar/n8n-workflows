# EcoAware Daily Sustainability Tip Workflow

This workflow automates the process of delivering personalized daily sustainability tips to users via email and chat notifications. It leverages user preferences and local environmental data to craft relevant tips that encourage eco-friendly habits.

---

## Workflow Overview

1. **Fetch All Users**: Retrieves the list of all users from the user database API.
2. **Format User Data**: Extracts user IDs, email addresses, and chat IDs from the retrieved user list for downstream processing.
3. **Get User Profile**: Fetches detailed user profiles, including preferences and location, for each user.
4. **Extract Preferences**: Extracts the user's sustainability preferences and location information from the profile.
5. **Get Local Environmental Data**: Fetches real-time local environmental data based on the user's geographic location.
6. **Generate Personalized Tip**: Creates a customized sustainability tip based on the user's preferences and current local environmental conditions.
7. **Send Email Notification**: Sends the personalized tip to the user's email address.
8. **Send Chat Notification**: Sends the same tip as a chat message via Telegram.

---

## Node Details

### 1. Fetch All Users

- **Type**: HTTP Request
- **Purpose**: Fetches all users from the user database API.
- **URL**: `https://api.youruserdatabase.com/users`
- **Authentication**: Header Bearer Token (`YOUR_API_KEY`)
- **Response**: JSON list of users containing user identifiers, emails, and chat IDs.

### 2. Format User Data

- **Type**: Function
- **Purpose**: Extracts and formats only essential data (`userId`, `userEmail`, `chatId`) from each user for subsequent API calls.
- **Code**:
  ```javascript
  return items.map(item => {
    return {
      json: {
        userId: item.json.userId,
        userEmail: item.json.email,
        chatId: item.json.chatId
      }
    };
  });
  ```

### 3. Get User Profile

- **Type**: HTTP Request
- **Purpose**: Retrieves detailed profile information for each user individually.
- **URL**: `https://api.youruserdatabase.com/users/${userId}`
- **Authentication**: Header Bearer Token (`YOUR_API_KEY`)
- **Parameters**: Uses dynamic URL to fetch data per `userId`.
- **Response**: JSON object including user preferences and location.

### 4. Extract Preferences

- **Type**: Function
- **Purpose**: Extracts the user’s sustainability preferences and location details from their profile.
- **Code**:
  ```javascript
  const user = items[0].json;
  return [{
    json: {
      preferences: user.preferences || {},
      location: user.location || {}
    }
  }];
  ```

### 5. Get Local Environmental Data

- **Type**: HTTP Request
- **Purpose**: Fetches local environmental data such as air quality and weather based on user coordinates.
- **URL**: `https://api.environmentdata.com/local?lat=${latitude}&lon=${longitude}`
- **Parameters**: Uses latitude and longitude dynamically extracted from user location.
- **Response**: JSON object with environmental metrics (e.g., air quality index, weather conditions).

### 6. Generate Personalized Tip

- **Type**: Function
- **Purpose**: Produces a tailored sustainability tip combining user preferences with local environmental data.
- **Logic**:
  - If user prefers energy tips and air quality is good, suggest airing out home and using natural light.
  - If user prefers water conservation, suggest shorter showers.
  - If user prefers transport advice and it’s rainy, suggest public transport or carpooling.
  - Otherwise, suggest waste reduction practices.
- **Sample Output**: One randomly selected tip from applicable suggestions.

- **Code**:
  ```javascript
  const prefs = items[0].json.preferences;
  const env = items[1].json;

  function generateTip(preferences, environment) {
    let tips = [];

    if (preferences.energy && environment.airQualityIndex < 50) {
      tips.push("Great air quality today! Consider airing out your home and saving energy by using natural light.");
    }
    if (preferences.water) {
      tips.push("Remember to reduce water usage today by taking shorter showers.");
    }
    if (preferences.transportation && environment.weather === 'rainy') {
      tips.push("It's rainy today — consider using public transport or carpooling to reduce emissions.");
    }
    if (tips.length === 0) {
      tips.push("Practice waste reduction by recycling and composting.");
    }
    return tips[Math.floor(Math.random() * tips.length)];
  }

  const tip = generateTip(prefs, env);

  return [{ json: { tip }}];
  ```

### 7. Send Email Notification

- **Type**: Email Send
- **Purpose**: Sends the personalized sustainability tip to the user's email.
- **From**: `ecoaware@yourdomain.com`
- **To**: User’s email address (`userEmail`)
- **Subject**: `"Your EcoAware Daily Sustainability Tip"`
- **Body**: Includes the personalized tip and a motivational message.
- **Text Example**:
  ```
  Hello! Here is your personalized sustainability tip for today:

  [Personalized Tip]

  Keep up the great work forming eco-friendly habits!
  ```

### 8. Send Chat Notification

- **Type**: Telegram Node
- **Purpose**: Sends the sustainability tip as a chat message via Telegram.
- **Chat ID**: User's Telegram chat identifier (`chatId`)
- **Message**: Formatted with a leaf emoji header and the personalized tip.
- **Example Message**:
  ```
  🌿 Daily EcoAware Tip:
  [Personalized Tip]
  ```

---

## Setup and Configuration

1. **API Keys & Endpoints**
   - Replace `YOUR_API_KEY` with your actual authorization token for the user database API.
   - Ensure the environmental data API endpoint is correct and accessible.

2. **Email Settings**
   - Update the `fromEmail` address to your verified sending email.
   - Configure actual email sending credentials and service as necessary.

3. **Telegram Bot**
   - Set up a Telegram bot and ensure you have user chat IDs to send notifications.
   - Configure the Telegram credentials within the platform where the workflow will run.

4. **User Data Requirements**
   - Ensure user profiles include valid `userId`, `email`, `chatId`, `preferences`, and `location` with latitude and longitude.

---

## Usage

- Trigger the workflow manually or schedule it to run daily.
- The workflow will automatically process all users to deliver personalized sustainability tips through both email and Telegram notifications.

---

## Summary

This workflow provides an automated, personalized approach to encourage users toward sustainable practices by combining individual preferences with real-time environmental insights. It enhances user engagement via dual communication channels — email and chat notifications — enabling an effective eco-awareness campaign.