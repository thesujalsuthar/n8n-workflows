# AI-Driven Personalized Sustainability Habit Tracker and Community Impact Dashboard

This workflow is designed to help users log their sustainability habits, receive AI-generated personalized tips for improvement, and visualize community-wide sustainability impact in real-time.

---

## Workflow Overview

1. **User Habit Submission**  
   Users submit their sustainability habits via an HTTP POST request.

2. **Validate Input**  
   Submitted data is validated to ensure all required fields (`userId`, `habit`, and `date`) are present.

3. **Save Habit Data**  
   Validated habit data is stored in the `user_habits` table of the PostgreSQL database.

4. **Fetch User Recent Habits**  
   Retrieves the user's logged habits from the last 30 days for personalized analysis.

5. **AI Generate Tips**  
   Uses OpenAI's GPT-4 model to analyze the user's recent habits and generate 3 personalized sustainability improvement tips.

6. **HTTP Response**  
   Sends a response back to the user confirming the saved habit and delivering the personalized tips.

7. **Fetch Community Data**  
   Retrieves aggregated habit counts for all users to assess community sustainability activities.

8. **Calculate Community Impact**  
   Aggregates community data into a total community impact score based on predefined impact values per habit.

9. **Broadcast Impact Real-Time**  
   Uses Socket.IO to broadcast the total community impact score and aggregated habits data to connected clients in real-time.

---

## Detailed Node Descriptions

### 1. User Habit Submission (HTTP Trigger)
- **Type:** HTTP Trigger (POST)
- **Path:** `/log-habit`
- **Purpose:** Serves as the entry point where users submit their sustainability habits.

### 2. Validate Input (Function)
- **Purpose:** Ensures that the submitted JSON contains `userId`, `habit`, and `date`.
- **Error Handling:** Throws an error if any required fields are missing.

### 3. Save Habit Data (PostgreSQL)
- **Operation:** Insert
- **Table:** `user_habits`
- **Columns:** `userId`, `habit`, `date`, `details`
- **Function:** Stores the user habit data persistently.

### 4. Fetch User Recent Habits (PostgreSQL)
- **Operation:** Execute Query
- **Query:** Retrieves habit counts by type for the user within the last 30 days.
- **Purpose:** Supplies data for personalized AI analysis.

### 5. AI Generate Tips (OpenAI Node)
- **Model:** GPT-4
- **Prompt:** Analyzes user habits and provides 3 personalized tips to improve sustainability.
- **Settings:** Temperature 0.7, max tokens 300

### 6. HTTP Response (HTTP Response)
- **Content:** Returns a JSON response confirming the saved habit and including AI-generated personalized tips.

### 7. Fetch Community Data (PostgreSQL)
- **Operation:** Execute Query
- **Query:** Retrieves total counts of each habit type logged by all users.
- **Purpose:** For community impact analysis.

### 8. Calculate Community Impact (Function)
- **Purpose:** Calculates a community impact score by weighting habit counts with predefined impact values, e.g., recycling = 1, reducing plastic = 2, etc.
- **Output:** Returns total community impact score along with aggregated community habits.

### 9. Broadcast Impact Real-Time (Socket.IO)
- **Channel:** `community-impact`
- **Purpose:** Broadcasts community impact data to all connected clients in real-time.

---

## Data Model

### `user_habits` Table
| Column  | Type    | Description                     |
|---------|---------|---------------------------------|
| userId  | string  | Unique identifier for the user  |
| habit   | string  | Sustainability habit description|
| date    | date    | Date the habit was logged       |
| details | string  | Optional additional information |

---

## API Endpoint

### POST `/log-habit`

**Request Body Example:**
```json
{
  "userId": "user123",
  "habit": "recycling",
  "date": "2024-05-20",
  "details": "Recycled plastic bottles today"
}
```

**Response Example:**
```json
{
  "success": true,
  "savedHabit": {
    "userId": "user123",
    "habit": "recycling",
    "date": "2024-05-20",
    "details": "Recycled plastic bottles today"
  },
  "personalizedTips": "1. Increase recycling frequency during weekends...\n2. Separate recyclables more efficiently...\n3. Explore composting options..."
}
```

---

## Requirements & Credentials

- **PostgreSQL Database:** Must have a schema `public` with a table `user_habits`.
- **OpenAI API Key:** Required to access GPT-4 for generating personalized tips.
- **Socket.IO Server:** Configured for real-time broadcasting of community impact data.

---

## Summary

This workflow provides an integrated solution for tracking sustainability habits, delivering AI-powered personalized advice, and promoting community awareness through real-time impact visualization.