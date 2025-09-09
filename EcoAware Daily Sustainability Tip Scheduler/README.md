# EcoAware Daily Sustainability Tip Delivery Workflow

This workflow automates the daily delivery of personalized sustainability tips to users via email and push notifications. It fetches tips from multiple sources, aggregates and personalizes them based on user preferences, and sends them out at the optimal times for each user.

---

## Workflow Overview

### 1. Trigger Daily (`Trigger Daily` node)
- Triggers the workflow once every day at 08:00 AM to start the tip fetching process.

### 2. Fetch Sustainability Tips
- **`Fetch Tip - EnvironmentTips` node**  
  Fetches the daily sustainability tip from the [EnvironmentTips API](https://api.environmenttips.org/dailytip). Requires an API key passed as a Bearer token in the `Authorization` header.  
- **`Fetch Tip - SustainableWorld` node**  
  Fetches the daily sustainability tip from the [SustainableWorld API](https://sustainableworld.api/tips/today). Requires an API key passed in the `x-api-key` header.

### 3. Aggregate Tips (`Aggregate Tips` node)
- Combines the tips fetched from both APIs into a single list named `aggregatedTips`.
- If one or both APIs fail to return a tip, it only includes the available tips.

### 4. Personalize Tips (`Personalize Tips` node)
- Defines a static list of users with their email, location, preferences (categories and best delivery time), and push notification tokens.
- Personalizes tips for each user. (Current logic delivers all aggregated tips to all users; can be extended for category-based filtering.)
- Includes the user's preferred time for receiving tips (`bestTime`).

### 5. Dynamic Scheduling

- **`Dynamic User Scheduler` node**  
  (This node is designed to dynamically trigger user-specific schedules daily and feed users for processing.)

- **`Calculate Schedule Time` node**  
  Calculates the exact ISO timestamp for each user's preferred delivery time (`bestTime`). If the preferred time for the day has passed, schedules for the next day.

- **`Filter by Schedule Time` node**  
  Filters users whose schedule time is within the next 5 minutes, so tips are sent only when it is the user's preferred time.

### 6. Delivery

- **`Send Email` node**  
  Sends the aggregated and personalized sustainability tips via email using:  
  - From: `ecoaware@yourdomain.com`  
  - To: User's email  
  - Subject: "Your Daily Sustainability Tip 🌿"  
  - Content includes plain text and HTML formats with location context and the list of tips.

- **`Send Push Notification` node**  
  Sends a push notification to the user's device via an HTTP POST request to `"https://push-service.yourdomain.com/send"`. It includes:  
  - Recipient push token  
  - Title: "EcoAware Daily Tip"  
  - Message: Aggregated tips combined with the pipe (`|`) separator

---

## Configuration Details

- **API Keys**:  
  Replace the placeholder API keys in `"Fetch Tip - EnvironmentTips"` (`YOUR_API_KEY_ENVIRONMENTTIPS`) and `"Fetch Tip - SustainableWorld"` (`YOUR_API_KEY_SUSTAINABLEWORLD`) nodes with valid keys.

- **Users and Preferences**:  
  User data is hardcoded in the `"Personalize Tips"` function node. Update with real users, locations, preferences, and push tokens as needed.

- **Email Sending**:  
  Configure SMTP credentials and domain appropriately in the n8n Email Send node settings to enable email dispatch.

- **Push Notifications**:  
  Replace `"https://push-service.yourdomain.com/send"` with the actual push notification service URL you use. Ensure no authentication is required or add as needed.

- **Scheduling Times**:  
  The workflow respects each user’s preferred delivery time specified in `bestTime` (in `HH:mm` format). The delivery nodes only trigger when the current time matches the user's preferred time (within a 5-minute window).

---

## Node Connections Summary

- Workflow starts at `Trigger Daily`
- Parallel fetches tips from two external APIs
- Aggregates the tips into one array
- Personalizes the tips for individual users
- Calculates and filters users based on personalized delivery times
- Sends emails and push notifications to each user at their preferred time daily

---

## Usage Notes

- This workflow is designed for n8n automation platform.
- Customize user list and API keys before activating.
- Ensure external endpoints (environment tips APIs, push notification service) are reachable and configured.
- Adjust scheduling times and frequency as per operational requirements.

---

*Thank you for contributing to a greener planet with EcoAware!*