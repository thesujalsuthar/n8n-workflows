# Sustainability Habit Tips Workflow

This n8n workflow automates the generation of personalized sustainability habit tips for users based on their daily environmental data, while also integrating community impact insights and social media updates related to sustainability.

---

## Workflow Overview

- **Trigger:** Daily at 7:00 AM.
- **Input:** User's daily sustainability data (energy usage, waste reduced, carbon footprint).
- **Process:**
  - Retrieve community sustainability impact data.
  - Fetch relevant social media posts on local sustainability and green initiatives.
  - Generate personalized tips using OpenAI GPT-4 based on user data.
  - Merge all data into a single structured output.
- **Output:** Save the combined data as a JSON dashboard file.

---

## Nodes Description

### 1. Daily Trigger
- **Type:** Schedule Trigger
- **Function:** Initiates the workflow every day at 7:00 AM.
- **Details:** Configured to trigger once per day at 07:00 hours.

### 2. User Daily Data Input
- **Type:** Set
- **Function:** Sets the user's daily sustainability metrics for the workflow.
- **Parameters:**
  - `energyUsage` (kWh): e.g., 5.6
  - `wasteReduced` (kg): e.g., 1.2
  - `carbonFootprint` (kg CO2): e.g., 7.8

### 3. Generate AI Tips
- **Type:** OpenAI Chat
- **Function:** Uses GPT-4 to generate personalized sustainability tips based on user's daily data.
- **Authentication:** Personal Access Token pulled from saved credentials (`openaiApi.apiKey`).
- **Prompt:**
  - System role instructs the AI as an assistant generating sustainability tips.
  - User role provides input with user's energy usage, waste reduction, and carbon footprint.
- **Output:** Three tailored sustainability habit tips.

### 4. Fetch Community Impact Data
- **Type:** HTTP Request (GET)
- **Function:** Retrieves recent community sustainability impact data from a local government API.
- **Endpoint:** `https://api.localgov/sustainability/community-impact`
- **Response:** JSON format containing community impact statistics.

### 5. Fetch Social Feeds
- **Type:** HTTP Request (GET)
- **Function:** Fetches recent social media posts related to sustainability.
- **Endpoint:** `https://api.socialmedia.com/search`
- **Query Parameters:**
  - `q`: Search term - "local sustainability OR community green initiatives"
  - `limit`: Number of posts - 10
- **Response:** JSON with social feed data.

### 6. Merge Real-time Data
- **Type:** Merge
- **Function:** Combines community impact data and social media feeds into one data stream for further processing.
- **Merge Mode:** Pass Through (preserves all incoming data).

### 7. Combine All Data
- **Type:** Function
- **Function:** Aggregates user daily data, AI-generated tips, community impact data, and social feeds into a single JSON object.
- **Output Structure:**
  ```json
  {
    "dailyUserData": { ... },
    "personalizedTips": "...",
    "communityImpact": { ... },
    "socialFeeds": [ ... ]
  }
  ```

### 8. Save Dashboard Data
- **Type:** Write Binary File
- **Function:** Saves the combined JSON data to a dashboard file.
- **File Path:** `public/sustainability-tracker/dashboard.json`

---

## Execution Flow

1. **Trigger at 7:00 AM:**
    - Starts `User Daily Data Input` with predefined sustainability metrics.
    - Simultaneously fetches community impact data and social media posts.

2. **User Data Processed:**
    - Sends user data to OpenAI for personalized tips generation.

3. **Real-time Data Merging:**
    - Combines community and social feeds data.

4. **Data Aggregation:**
    - Joins AI tips, user data, and merged real-time data into a single object.

5. **Saving Output:**
    - Writes the aggregated data to the dashboard JSON file.

---

## Requirements

- **n8n instance** with credentials for OpenAI API.
- Valid **OpenAI API Key** stored in n8n credentials as `openaiApi`.
- Access to the following APIs:
  - Community Impact API: `https://api.localgov/sustainability/community-impact`
  - Social Media API: `https://api.socialmedia.com/search`
- Permissions to write files in the `public/sustainability-tracker/` directory.

---

## Customization

- **Trigger Time:** Modify the `Daily Trigger` node's hour/minute for different schedule.
- **User Data Input:** Adjust the values in `User Daily Data Input` node to reflect real user inputs dynamically.
- **AI Model:** Change the model parameter in `Generate AI Tips` (e.g., use `gpt-4` or other available models).
- **APIs:** Update the API endpoints and query parameters as needed to fit your data sources.
- **Output Path:** Change the file path in `Save Dashboard Data` node to your preferred location.

---

## Summary

This workflow provides a fully automated, daily updated sustainability dashboard by combining personal environmental impact data, AI-generated habit tips, and community/social insights, assisting users in improving their eco-friendly practices effectively.