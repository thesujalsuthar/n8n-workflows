# AI-Powered Personalized Environmental Sustainability Tips & Habit Tracker

This workflow provides personalized environmental sustainability tips and tracks user progress on adopting sustainable habits through an AI-driven approach.

---

## Overview

The workflow accepts user data related to their lifestyle and environment, enriches it with environmental data, and uses AI (GPT-4) to generate personalized, actionable sustainability tips. It then tracks the user's habit progress and returns the tailored tips as a response.

---

## Workflow Nodes & Functionality

### 1. Webhook (`submit-user-data`)

- **Type:** HTTP POST Webhook
- **Purpose:** Entry point to receive user input data.
- **User Data Example:**
  ```json
  {
    "energyUsage": 350,
    "transportMethod": "car",
    "dietPreference": "omnivore",
    "recyclingHabit": true,
    "region": "CA"
  }
  ```
- **Details:** Accepts POST requests at `/submit-user-data` endpoint and triggers the workflow.

---

### 2. Prepare Data (Function Node)

- **Purpose:** Prepares a combined dataset of the user’s submitted data and simulated environmental data.
- **Environmental Data included:**
  - Air Quality Index (e.g., 75)
  - Regional CO2 Emissions (e.g., 12.4 metric tons)
- **Function Logic:**
  - Extracts user data from webhook input.
  - Adds mock environmental data (can be replaced with external API calls).
- **Output:** Combined JSON with userData and environmentalData.

---

### 3. Generate Sustainability Tips (OpenAI Node)

- **Model:** GPT-4
- **Prompt:**
  - Uses the combined user and environmental data to instruct the AI to generate **3 practical, personalized, and actionable sustainability tips**.
  - Tips focus on reducing the user's environmental impact.
- **Output Format:** JSON object with field `tips` as an array of strings.

---

### 4. Track Habit Progress (Function Node)

- **Purpose:** Tracks the user's habit progress with tips received.
- **Logic:**
  - Parses the generated tips from the AI response.
  - Creates or updates an in-memory habit progress data structure including:
    - Last input data
    - Tips received
    - Empty progress log (placeholder for habit tracking over time)
- **Note:** In production, habit progress should be saved in a database/external storage.

---

### 5. Send Tips Response (Start Node)

- **Purpose:** Sends back a formatted response to the user.
- **Message Content:**
  ```
  Hello! Here are your personalized environmental sustainability tips based on your input:

  1. [Tip 1]
  2. [Tip 2]
  3. [Tip 3]

  Track your progress regularly to build sustainable habits and make an impactful change!
  ```
- Tips are dynamically inserted based on the AI-generated output.

---

## How to Use

1. Send a **POST** request with user data JSON to the webhook endpoint:  
   `/submit-user-data`

2. The workflow processes the input, augments it with environmental data, generates personalized tips, tracks habit progress, and responds with tailored actionable suggestions.

3. Use the response tips to guide and encourage users toward sustainable behavior changes.

---

## Example Input

```json
{
  "energyUsage": 350,
  "transportMethod": "car",
  "dietPreference": "omnivore",
  "recyclingHabit": true,
  "region": "CA"
}
```

## Example Output

```json
{
  "text": "Hello! Here are your personalized environmental sustainability tips based on your input:\n\n1. Consider switching to public transport or carpooling to reduce carbon emissions.\n2. Try incorporating more plant-based meals into your diet to lower your environmental footprint.\n3. Increase your energy efficiency by using LED bulbs and unplugging devices when not in use.\n\nTrack your progress regularly to build sustainable habits and make an impactful change!"
}
```

---

## Notes

- Environmental data is currently simulated and should be integrated with real APIs for production.
- Habit tracking is stored in-memory; persistent storage is recommended for long-term tracking.
- The prompt to GPT-4 can be customized to refine tip generation.
- Ensure OpenAI API credentials and webhook security configurations are properly set in your environment.

---

## License

This workflow is provided as-is for educational and prototyping purposes. Customize and expand as needed for production use.