# EcoAware: AI-Driven Personalized Sustainability Tips & Habit Motivation

EcoAware is an automated workflow designed to provide users with personalized daily sustainability tips and habit motivation based on their preferences and local environmental data. This system integrates user data management with real-time environmental insights to encourage sustainable habits tailored to individual needs.

---

## Workflow Overview

### 1. User Registration Trigger
- **Type:** HTTP POST Trigger
- **Endpoint:** `/registerUser`
- **Purpose:** Captures new user registration data including basic info and sustainability preferences.

### 2. Parse and Normalize User Data
- **Type:** Function Node
- **Function:** Normalizes incoming user data into a standard format, ensuring all necessary fields are populated with defaults if missing.
- **Data Captured:**
  - User ID (from `userId`, `id`, or `email`)
  - Name (default: "User")
  - Email
  - Preferences
    - Focus Areas (default: `[energy, water, waste]`)
    - Habit Level (default: `beginner`)
    - Notification Time (default: `08:00`)
    - Location (default: empty string)

### 3. Store User Data
- **Type:** MongoDB Upsert Operation
- **Collection:** `users`
- **Purpose:** Inserts or updates the user information in the MongoDB EcoAware database based on the user ID.

---

### 4. Daily Tip HTTP Trigger
- **Type:** HTTP GET Trigger
- **Endpoint:** `/dailyTip`
- **Purpose:** Initiates the process to fetch and generate a personalized daily sustainability tip along with motivation messages.

### 5. Retrieve User Preferences
- **Type:** MongoDB Find Operation
- **Collection:** `users`
- **Purpose:** Retrieves the stored user preferences based on provided identifiers (`userId`, `id`, or `email`) from the query.

### 6. Fetch Local Environmental Data
- **Type:** HTTP Request
- **API:** OpenWeather Air Quality API
- **Parameters:**
  - Latitude and Longitude retrieved from user preferences location data.
  - API Key stored as a credential (`OpenWeather API Key`).
- **Purpose:** Fetches current air quality data to adapt tips based on local environmental conditions.

### 7. Generate Personalized Tip & Motivation
- **Type:** Function Node
- **Logic:**
  - Determines tip category based on user focus areas and local air quality.
  - Tip categories include: `air`, `energy`, `water`, `waste`, and `general`.
  - Selects a random tip from predefined tips aligned to the chosen category.
  - Chooses motivational text based on the user’s habit level (`beginner`, `intermediate`, `advanced`).
- **Outputs:**
  - `tipCategory`: Category assigned to the tip.
  - `tip`: Selected sustainability tip.
  - `motivation`: Personalized motivational message to encourage habit formation.

### 8. Respond with Daily Tip & Motivation
- **Type:** HTTP Response
- **Purpose:** Returns the JSON response with the personalized tip and habit motivation back to the requester.

---

## Data Structure

### User Data Format
```json
{
  "id": "string",
  "name": "string",
  "email": "string|null",
  "preferences": {
    "focusAreas": ["energy", "water", "waste", "air"],
    "habitLevel": "beginner|intermediate|advanced",
    "notificationTime": "HH:mm",
    "location": {
      "latitude": "number",
      "longitude": "number"
    } | ""
  }
}
```

### Daily Tip Response Format
```json
{
  "tipCategory": "string",
  "tip": "string",
  "motivation": "string"
}
```

---

## Credentials Required

- **MongoDB EcoAware:** Access credentials for the MongoDB instance hosting user data.
- **OpenWeather API Key:** API key for accessing OpenWeather air quality data.

---

## Endpoints Summary

| Method | Endpoint      | Description                          |
|--------|---------------|------------------------------------|
| POST   | `/registerUser` | Register or update user information |
| GET    | `/dailyTip`     | Fetch personalized daily sustainability tip and motivation |

---

## Notes

- Location information is critical for fetching relevant environmental data; users without location data will receive general tips.
- Tips and motivational messages are randomized within their categories to keep the content fresh.
- The workflow supports upsert operations for seamless user data management.

---

This workflow ensures users receive sustainability tips and motivation personalized both by their preferences and the environmental context they live in, thereby supporting consistent and meaningful habit formation for a greener planet.