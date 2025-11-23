# AI-Powered Personalized Wildlife Conservation Assistant

## Overview

This workflow is designed to automate the process of monitoring wildlife populations and habitats using public APIs and drone surveillance data, analyze habitat conditions with AI, and send personalized conservation alerts to registered users based on their location and interests.

---

## Workflow Breakdown

### 1. Fetch Wildlife Population Data
- **Node:** `Fetch Wildlife Population Data`
- **Type:** HTTP Request
- **Description:** Retrieves wildlife population data from a public API for a specific region.
- **Parameters:**
  - `url`: `https://public-wildlife-api.example.org/populations?region={{$json.region}}`
  - Returns data in JSON format with a timeout of 30 seconds.

---

### 2. Fetch Drone Surveillance Feed
- **Node:** `Fetch Drone Surveillance Feed`
- **Type:** HTTP Request
- **Description:** Retrieves drone surveillance data feed for the specified region.
- **Parameters:**
  - `url`: `https://api.drone-surveillance.example.com/feeds?location={{$json.region}}`
  - Returns data in JSON format with a timeout of 30 seconds.

---

### 3. Combine Wildlife & Drone Data
- **Node:** `Combine Wildlife & Drone Data`
- **Type:** Function
- **Description:** Combines the wildlife population data and drone surveillance data into a single JSON object for further processing.
- **Logic:**
  ```javascript
  const populations = items[0].json.data || [];
  const droneData = items[1].json.data || [];

  return [{ json: { populations, droneData } }];
  ```

---

### 4. Prepare AI Model Input
- **Node:** `Prepare AI Model Input`
- **Type:** Function
- **Description:** Formats and filters the combined data into an input structure suitable for the AI habitat condition analysis model.
- **Logic:**
  ```javascript
  const populations = items[0].json.populations;
  const droneData = items[0].json.droneData;

  const habitatInput = {
    populations,
    droneOverview: droneData.map(d => ({
      timestamp: d.timestamp,
      habitatImages: d.images,
      detectedSpecies: d.speciesDetected
    }))
  };

  return [{ json: { habitatInput } }];
  ```

---

### 5. AI Habitat Condition Analysis
- **Node:** `AI Habitat Condition Analysis`
- **Type:** OpenAI
- **Description:** Uses the `habitat-condition-analyzer-v1` AI model to predict habitat conditions and risks based on the prepared input.
- **Parameters:**
  - `model`: `habitat-condition-analyzer-v1`
  - `task`: `predict`
  - `input`: JSON stringified habitat input from previous node

---

### 6. Fetch Registered Users
- **Node:** `Fetch Registered Users`
- **Type:** HTTP Request
- **Description:** Retrieves user preferences and registration data filtered by their location of interest.
- **Parameters:**
  - `authentication`: predefined credential type
  - `resource`: `userPreferences`
  - `operation`: `getAll`
  - `filters`: `{ filterType: "location_interest" }`

---

### 7. Generate User-Specific Alerts
- **Node:** `Generate User-Specific Alerts`
- **Type:** Function
- **Description:** Filters the AI analysis outcomes against user preferences and generates personalized alerts.
- **Logic:**
  ```javascript
  const users = items[0].json.users || [];
  const analysis = $json;

  const alerts = users.reduce((acc, user) => {
    const matchingPopulations = analysis.populations ? analysis.populations.filter(p => p.region === user.location) : [];
    const riskFactors = analysis.risks || [];

    const userInterests = user.interests || [];

    if (matchingPopulations.length === 0) return acc;

    const relevantRisks = riskFactors.filter(risk => userInterests.includes(risk.type));

    if (relevantRisks.length === 0) return acc;

    acc.push({
      userId: user.id,
      email: user.email,
      phone: user.phone,
      pushToken: user.pushToken,
      alerts: relevantRisks.map(r => ({
        message: `Alert for ${r.type}: ${r.description}`,
        recommendation: r.recommendation
      }))
    });

    return acc;
  }, []);

  return alerts.map(alert => ({ json: alert }));
  ```

---

### 8. Send Email Alerts
- **Node:** `Send Email Alerts`
- **Type:** Email Send
- **Description:** Sends personalized wildlife conservation alerts via email.
- **Parameters:**
  - `operation`: `send`
  - `toEmail`: dynamically set to user email
  - `subject`: "Wildlife Conservation Alert"
  - `text`: concatenated alert messages and recommendations

---

### 9. Send SMS Alerts
- **Node:** `Send SMS Alerts`
- **Type:** Twilio (SMS)
- **Description:** Sends personalized alerts via SMS messages.
- **Parameters:**
  - `resource`: `message`
  - `operation`: `send`
  - `phoneNumber`: dynamically set to user's phone number
  - `message`: concatenated alert messages and recommendations separated by semicolons

---

### 10. Send Mobile Push Notifications
- **Node:** `Send Mobile Push Notifications`
- **Type:** Push Notification
- **Description:** Delivers alerts directly as push notifications to users' mobile devices.
- **Parameters:**
  - `operation`: `sendPushNotification`
  - `deviceToken`: user's push notification token
  - `title`: "Wildlife Conservation Alert"
  - `body`: concatenated alert messages
  - `data`: JSON object containing alerts for contextual notification data

---

## Data Flow Summary

1. Wildlife population data and drone surveillance feeds are fetched independently.
2. Both data sources are combined into a unified data structure.
3. The data is cleansed and formatted as input for the AI habitat condition analyzer.
4. AI predictions identify habitat risks.
5. Registered users and their preferences are retrieved.
6. Personalized alerts are generated based on the overlap between AI findings and user interests.
7. Alerts are simultaneously sent via email, SMS, and push notifications.

---

## Setup Requirements

- **APIs:**
  - Access to public wildlife population data API.
  - Access to drone surveillance data API.
  - User preferences API with proper authentication.
- **AI Model:** `habitat-condition-analyzer-v1` deployed and accessible.
- **Communication:**
  - Email sending service configured.
  - Twilio account setup for SMS dispatch.
  - Push notification service configured with device tokens.
- **Environment:**
  - Supports JavaScript function execution.
  - JSON data processing capabilities.

---

## Notes

- This workflow is configurable by changing region input to monitor different geographic locations.
- User preferences should include location and species or habitat interests for relevant personalized alerts.
- Timeout settings ensure requests do not hang indefinitely.
- The AI model should be trained to analyze combined ecological and image data for precise habitat condition insights.

---

## License

This workflow is provided as-is for use in wildlife conservation projects. Modify and extend as needed to fit specific use cases.