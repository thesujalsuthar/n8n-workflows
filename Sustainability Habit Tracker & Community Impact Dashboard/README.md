# AI-Powered Sustainability Habit Tracker & Real-Time Community Impact Dashboard

## Overview

This workflow integrates with Habitica to track users' sustainability-related habits, generates AI-powered personalized sustainability tips, and updates a real-time community impact dashboard. It leverages Habitica's API, OpenAI's GPT-4 for AI insights, MongoDB for data storage and aggregation, and a dashboard visualization node for community metrics display.

---

## Workflow Steps

### 1. Start
- The entry point of the workflow initiating the data flow.

### 2. Fetch Habit Data from Habitica
- **Type:** HTTP Request
- **Function:** Retrieves the current user's tasks from Habitica via the Habitica API.
- **Authentication:** OAuth2 with custom headers `x-api-user` and `x-api-key`.
- **Output:** Raw list of all user tasks.

### 3. Process Habit Data
- **Type:** Function
- **Function:** Filters Habitica tasks to select only those of type `habit`.
- Extracts:
  - Habit name (`text`),
  - Completion status (`isCompleted`),
  - Current date.
- **Output:** Structured array `trackedHabits` representing today's tracked habits with completion status.

### 4. Generate Personalized Tips
- **Type:** OpenAI (GPT-4)
- **Prompt:** Takes the `trackedHabits` array and requests personalized sustainability tips to help the user improve or maintain these habits.
- **Output:** JSON object containing an array of tips, e.g.,
  ```json
  { "tips": ["tip1", "tip2", ...] }
  ```

### 5. Extract Tips
- **Type:** Function
- **Function:** Parses the AI response to extract the tips array.
- Converts each tip into individual output items for potential further use.

### 6. Upsert Habit Log to DB
- **Type:** MongoDB
- **Function:** Inserts or updates individual habit logs into the `habit_logs` collection in the `sustainability_community` database.
- **Data Upserted:** Habit name, completion status, date, and user ID.
- **Query:** Upsert based on the unique combination of date, user ID, and habit name.

### 7. Aggregate Community Impact Data
- **Type:** MongoDB
- **Function:** Aggregates habit log data across all users to calculate community-wide statistics:
  - Total completions per habit.
  - Total attempts/logs per habit.
  - Completion rate percentage per habit.
- **Output:** Aggregated stats for each habit.

### 8. Prepare Visualization Data
- **Type:** Function
- **Function:** Formats aggregated community data into a chart-friendly structure suitable for a bar chart visualization.
- Uses habit names as labels and community completion rates as dataset values.

### 9. Update Dashboard Visualization
- **Type:** Dashboard Node
- **Function:** Updates the community impact dashboard with the latest habit completion rates visualization.
- **Target Path:** `dashboard/communityImpactChart`

---

## Credentials & Integrations

- **Habitica API**
  - Uses OAuth2 authentication.
  - Requires `x-api-user` (User ID) and `x-api-key` (API Key) header values.

- **MongoDB**
  - Connected to a `sustainability_community` database.
  - Uses a `habit_logs` collection to store and aggregate habit data.

- **OpenAI GPT-4**
  - Used to generate personalized sustainability tips.
  - Prompt includes the user's tracked habits from Habitica.

---

## Data Flow Summary

1. User habit data fetched from Habitica.
2. Relevant habits filtered and processed.
3. AI generates personalized tips based on today's habits.
4. Individual habit logs upserted into community DB.
5. Community aggregated habit performance metrics calculated.
6. Data formatted and pushed to a real-time dashboard for community insight.

---

## Usage Notes

- Ensure credentials for Habitica API, MongoDB, and OpenAI are configured correctly.
- Habit logs are uniquely identified by date, user ID, and habit name to prevent duplicates.
- The dashboard visualization updates dynamically to reflect the latest community data.
- The OpenAI prompt is designed to respond strictly with a JSON object containing tips.

---

## Summary

This workflow empowers users to track and improve sustainable habits through AI-driven insights while fostering a community-wide impact visualization that encourages collective progress toward sustainability goals.