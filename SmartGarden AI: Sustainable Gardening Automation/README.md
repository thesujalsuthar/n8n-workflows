# AI-Powered Smart Sustainable Gardening Workflow

This workflow leverages AI and weather data to monitor plant health, detect pests, make watering decisions, and send actionable notifications for sustainable garden management.

---

## Overview

The workflow is scheduled to run **twice daily** at 6:00 AM and 6:00 PM. It integrates AI-based plant health and pest detection, weather forecasting, logic to decide watering needs, and notifications through Slack. If watering is necessary, it automatically triggers an irrigation system.

---

## Workflow Nodes and Functions

### 1. Schedule Trigger
- **Type:** Schedule Trigger
- **Function:** Automatically starts the workflow every day at 6:00 AM and 6:00 PM.

### 2. Prepare Input Data
- **Type:** Function
- **Function:** Provides fixed input data including:
  - Latest garden camera image URL (`https://gardencam.example.com/latest.jpg`).
  - Garden location coordinates (Latitude: 40.7128, Longitude: -74.0060).

### 3. AI Plant Health Monitor
- **Type:** HTTP Request
- **Function:** Sends the current plant image to an AI API (`https://api.exampleai.com/v1/plant-health/detect`) to assess plant health.
- **Authentication:** Bearer token via `$credentials.aiApi.key`.
- **Output:** JSON response containing `health_status` (e.g., "poor", "good").

### 4. Get Weather Forecast
- **Type:** HTTP Request
- **Function:** Calls OpenWeatherMap One Call API for current weather including rain and temperature based on provided latitude and longitude.
- **Authentication:** API key via `$credentials.openWeatherMapApi.key`.
- **Parameters:**
  - Latitude
  - Longitude
  - Units set to metric
  - Data excluding minutely, hourly, daily, and alerts for efficiency.

### 5. AI Pest Detection
- **Type:** HTTP Request
- **Function:** Sends the plant image to an AI API (`https://api.exampleai.com/v1/pest-detection/detect`) to identify pests.
- **Authentication:** Bearer token via `$credentials.aiApi.key`.
- **Output:** JSON response listing detected pests (e.g., aphids, spider_mites).

### 6. Watering Decision Logic
- **Type:** Function
- **Logic:**
  - Extracts plant health and current weather data.
  - Assesses:
    - If plant health is "poor", or
    - Rainfall is below 1 mm and temperature is above 15°C.
  - Decides if watering is needed.
- **Output:** Object with `shouldWater` (true/false) and a `reason`.

### 7. Treatment Recommendations
- **Type:** Function
- **Function:** Matches detected pests to eco-friendly treatment recommendations:
  - Aphids → neem oil spray or ladybugs.
  - Spider mites → predatory mites or insecticidal soap.
  - Caterpillars → handpicking or Bt treatment.
  - Whiteflies → yellow sticky traps and insecticidal soap.
  - Scale → horticultural oils or natural predators.
- **Output:** List of pest-treatment pairs.

### 8. Water Plants (Conditional)
- **Type:** If Node (Conditional)
- **Function:** Checks `shouldWater` decision to either:
  - Trigger watering if true.
  - Skip watering if false.

### 9. Trigger Watering
- **Type:** HTTP Request
- **Function:** Sends a POST request to a local irrigation system (`https://api.yourirrigationsystem.local/water`) to water plants for 5 minutes.

### 10. Send Slack Notification
- **Type:** Slack Node
- **Function:** Sends a detailed message to a `gardening-alerts` Slack channel with:
  - AI Plant Health Status.
  - Watering decision and reason.
  - Pest treatment recommendations.
- **Authentication:** Slack bot token via `$credentials.slackBotApi.token`.

---

## Workflow Execution Flow

1. **Trigger:** The workflow starts at the scheduled times.
2. **Prepare Data:** Provides image URL and location.
3. **AI Analysis:** Parallel requests to AI Plant Health and Pest Detection APIs.
4. **Get Weather:** Obtains current rain and temperature data.
5. **Decision Making:** Evaluates plant health and weather to decide on watering.
6. **Pest Treatment:** Generates eco-friendly pest control advice.
7. **Conditional Watering:** If watering is needed, triggers irrigation.
8. **Notification:** Sends full status and recommendations to Slack.

---

## Required Credentials

- `aiApi` with API key for plant health and pest detection AI.
- `openWeatherMapApi` with API key for weather data.
- `slackBotApi` with Slack Bot OAuth token for sending messages.

---

## Configuration Notes

- Update the `imageUrl` in the **Prepare Input Data** node to match your garden camera feed.
- Set your garden’s geographical coordinates accurately for weather data.
- Configure your irrigation system API URL and parameters in the **Trigger Watering** node.
- Slack channel name can be updated in the **Send Slack Notification** node if needed.

---

## Summary

This automated workflow combines AI-powered analysis and real-time weather data to optimize sustainable gardening efforts by:

- Monitoring plant health.
- Detecting pests with actionable eco-friendly treatments.
- Making informed watering decisions.
- Providing timely alerts and automated irrigation control.

---

Run the workflow to keep your garden thriving while minimizing water waste and chemical use!