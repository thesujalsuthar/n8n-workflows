# AI-Driven Personalized Daily Sustainability Tip Delivery and Habit Tracker

## Overview
This workflow provides users with personalized daily sustainability tips, tracks their eco-friendly habits, calculates their progress and environmental impact, and delivers updates via email, messaging platforms, and a user dashboard. It leverages user habit data from an external API and sends actionable suggestions to encourage sustainable practices.

---

## Workflow Nodes Description

### 1. Get User Habit Data
- **Type:** HTTP Request
- **Function:** Retrieves the user's current sustainability habit status from an external Habit Tracker API.
- **Authentication:** Uses a bearer token API key stored in credentials.
- **Input:** Requires `userId` from incoming JSON.
- **Output:** User habit data such as composting, recycling, reducing plastic use, and energy saving statuses.

### 2. Generate Daily Tip and Personalized Suggestions
- **Type:** Function
- **Function:** 
  - Selects a random sustainability tip from a pre-defined list.
  - Analyzes the retrieved user habit data.
  - Generates personalized suggestions encouraging improvement in non-adhered habits.
  - If all habits are followed, encourages user positively.
- **Output:** 
  - `dailyTip`: Randomly chosen tip.
  - `personalizedSuggestions`: Array of personalized tips or encouragement.
  - `userHabitData`: The user’s current habit data.

### 3. Calculate Habit Progress and Impact
- **Type:** Function
- **Function:** 
  - Calculates the percentage of habits adhered to by the user.
  - Estimates environmental impact saved based on the number of habits followed (mock data).
- **Output:** 
  - `habitAdherence`: Percentage adherence to habits.
  - `environmentalImpactSaved`: Estimated impact saved (arbitrary units).

### 4. Send Daily Email
- **Type:** Email Send
- **Function:** Sends the user an email with their daily tip, personalized suggestions, habit adherence progress, and estimated environmental impact saved.
- **From:** noreply@sustainabilityapp.com
- **To:** User’s email (`userEmail` from JSON)
- **Subject:** "Your Personalized Daily Sustainability Tips and Progress"
- **Credentials:** SMTP Email credentials configured.

### 5. Send Message
- **Type:** Slack
- **Function:** Sends a Slack message to the user’s preferred messaging channel with the same content as the email.
- **Channel:** Uses `userMessagingChannel` or defaults to a preset channel.
- **Credentials:** Slack API credentials configured.

### 6. Prepare Dashboard Data
- **Type:** Function
- **Function:** Structures the summary data for dashboard updating, including date, adherence percent, impact saved, daily tip, and personalized suggestions.
- **Output:** `dashboardSummary` object.

### 7. Update User Dashboard
- **Type:** HTTP Request (POST)
- **Function:** Sends the prepared dashboard summary data to an external Dashboard API for updating the user's sustainability tracker dashboard.
- **Authentication:** Bearer token API key stored in credentials.
- **Content-Type:** application/json

---

## Workflow Execution Flow

1. **Get User Habit Data** node fetches current sustainability habit status using the user's ID.
2. Data flows into **Generate Daily Tip and Personalized Suggestions**, which produces a random tip and personalized advice based on habit data.
3. The results are passed to **Calculate Habit Progress and Impact**, which computes progress metrics.
4. Calculated results trigger parallel actions:
   - **Send Daily Email** to deliver the tip and progress via email.
   - **Send Message** to send the same information via Slack.
   - **Prepare Dashboard Data** to format the data for dashboard integration.
5. Finally, **Update User Dashboard** posts the daily summary to the user's dashboard service.

---

## Credentials Required

- **Habit Tracker API:** API key with access to user habit status.
- **SMTP Email:** Credentials for sending emails (SMTP server, email, password).
- **Slack API:** API token with permissions to post messages to user channels.
- **Dashboard API:** API key with permission to update user dashboards.

---

## Input Data Requirements

Input JSON should contain at least:

- `userId`: Identifier for fetching habit data.
- `userEmail`: Email address to send updates.
- `userMessagingChannel`: (Optional) Slack channel or user ID for messaging.

---

## Example User Habit Data Structure

```json
{
  "composting": false,
  "recycling": false,
  "reducingPlastic": true,
  "energySaving": false
}
```

---

## Sustainability Tips List

- Use a reusable water bottle instead of single-use plastic.
- Turn off lights when not in use to save energy.
- Opt for public transportation or carpooling.
- Reduce food waste by planning meals ahead.
- Plant native trees and shrubs in your garden.
- Use energy-efficient appliances.
- Take shorter showers to conserve water.
- Buy locally-produced products to reduce carbon footprint.
- Recycle paper, plastic, and glass properly.
- Support companies with sustainable practices.

---

## Output Summary Sent to Users

- Daily personalized sustainability tip.
- Specific personalized suggestions based on habits.
- Habit adherence progress as percentage.
- Estimated environmental impact saved in units.

---

## Additional Notes

- The environmental impact saved is an estimated value based on the number of adhered habits.
- Messages use simple markdown-style formatting to enhance readability.
- The dashboard update payload includes a date stamp for accurate tracking.
- The workflow is designed to be extensible to add more habits or delivery channels.

---

End of README.md