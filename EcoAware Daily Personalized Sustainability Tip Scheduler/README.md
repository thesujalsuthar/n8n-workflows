# EcoAware Personalized Sustainability Tip Scheduler

## Overview

The **EcoAware Personalized Sustainability Tip Scheduler** is an automated workflow designed to send daily personalized sustainability tips to users via email. These tips are tailored based on each user’s preferences and local environmental data, encouraging eco-friendly habits in a timely and relevant manner.

---

## Workflow Components

### 1. Daily Trigger

- **Type:** Schedule Trigger
- **Description:** Starts the workflow every day at 8:00 AM.
- **Purpose:** Ensures timely daily execution.

### 2. Fetch Users

- **Type:** MongoDB Node (Find Many)
- **Collection:** `users`
- **Operation:** Fetches up to 100 user documents with their preferences and location details.
- **Credentials:** Requires MongoDB connection credentials.
- **Purpose:** Retrieves user data to generate personalized tips.

### 3. Define Tips Database

- **Type:** Function Node
- **Description:** Defines a static set of sustainability tips, categorized into:
  - Water conservation
  - Energy efficiency
  - Waste reduction
  - Sustainable transport
- **Purpose:** Provides a tip source for generating recommendations.

### 4. Generate Personalized Tips

- **Type:** Function Node (v2)
- **Description:** 
  - Iterates over each fetched user.
  - Fetches local environmental data (weather, humidity, etc.) using OpenWeatherMap API based on user location.
  - Matches tip categories based on user preferences and local weather conditions.
  - Randomly selects a fitting tip from the chosen category.
- **Dependencies:** Requires `OPENWEATHERMAP_API_KEY` environment variable.
- **Purpose:** Creates customized, context-aware sustainability tips for each user.

### 5. Save Tips

- **Type:** MongoDB Node (Create Document)
- **Collection:** `scheduled_tips`
- **Operation:** Saves the generated tips along with user identifiers, email, category, date, and location.
- **Credentials:** Uses MongoDB credentials.
- **Purpose:** Persists tip history and ensures tracking.

### 6. Send Tip Email

- **Type:** Email Send
- **From:** `ecoaware@yourdomain.com`
- **To:** User's email (from generated tips)
- **Subject:** `Your Daily Sustainability Tip 🌍`
- **Content:** Personalized tip included both as plain text and HTML.
- **Credentials:** Requires SMTP credentials.
- **Purpose:** Delivers the daily personalized tip directly to the user's inbox.

---

## Environment & Credentials

- **MongoDB Credentials**  
  Required for both fetching user data and saving scheduled tips. Replace `MONGODB_CREDENTIALS_ID` with your actual credential ID.

- **SMTP Credentials**  
  Required to send emails. Replace `SMTP_CREDENTIALS_ID` with your actual credential ID.

- **OpenWeatherMap API Key**  
  Set the environment variable `OPENWEATHERMAP_API_KEY` with your valid API key to enable fetching local environmental data.

---

## Data Structure

### User Document (from `users` collection)

- `_id` or `id`: Unique identifier
- `email`: User email address
- `preferences`: Array of preferred tip categories (e.g., `["energy", "water"]`)
- `location`:
  - `latitude`: Number
  - `longitude`: Number

### Scheduled Tip Document (saved in `scheduled_tips` collection)

- `userId`: User identifier
- `email`: User email
- `category`: Tip category (water, energy, waste, transport)
- `tip`: Personalized sustainability tip string
- `date`: ISO string timestamp of tip generation
- `location`: User location object

---

## Execution Flow

1. **Trigger:** The workflow runs daily at 8:00 AM.
2. **Fetch Users:** Retrieves up to 100 active users.
3. **Load Tips:** Defines a static database of categorized tips.
4. **Generate Tips:** For each user,
   - Fetches local weather data if location available.
   - Selects tip category based on preferences and environment.
   - Picks a randomized tip.
5. **Save Tips:** Persists the personalized tips to the database.
6. **Send Emails:** Sends an email with the tip to each user's email address.

---

## Customization & Extensibility

- **Expand Tips Database:** Add more categories or tips into the `Define Tips Database` function.
- **Improve Environmental Logic:** Refine tip selection based on more detailed weather or other environmental data.
- **Batch Size:** Adjust the `limit` in the `Fetch Users` node as needed.
- **Email Templates:** Customize the email content and style in the `Send Tip Email` node.
- **Error Handling:** Add error-handling nodes/functions for robustness.

---

## Notes

- Ensure that all required credentials are properly set up in your n8n environment.
- The OpenWeatherMap API key must be added as an environment variable for environmental data fetching to work.
- This workflow assumes users have valid emails and stored location data.
- Review and comply with email sending limits and policies for your SMTP server.

---

Thank you for using EcoAware to promote greener daily habits!