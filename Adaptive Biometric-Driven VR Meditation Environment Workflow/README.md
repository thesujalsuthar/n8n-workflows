# Biometric Data to VR Meditation Environment Workflow

## Overview

This workflow integrates biometric sensor data with AI-driven mood analysis to dynamically customize a VR meditation environment based on the user's current emotional state. It consists of sequential steps that fetch biometric data, analyze mood, determine VR environment settings, update the VR environment, and confirm the update with a success message.

---

## Workflow Nodes Description

### 1. Get Biometric Data

- **Type:** HTTP Request
- **Purpose:** Retrieves real-time biometric data from the biometric sensor API.
- **API Endpoint:** `https://api.biometric-sensor.com/v1/data`
- **Authentication:** Header Bearer token using `biometricApi.apiKey` credential.
- **Output:** Raw biometric data JSON including metrics like heart rate, skin conductance, and breathing rate.

---

### 2. Extract Biometric Metrics

- **Type:** Function
- **Purpose:** Extracts relevant biometric metrics required for mood analysis.
- **Logic:** Parses the first item's JSON and extracts:
  - `heartRate`
  - `skinConductance`
  - `breathingRate`
- **Output:** JSON object containing only these three biometric metrics.

---

### 3. Analyze Mood via AI

- **Type:** HTTP Request
- **Purpose:** Sends extracted biometric metrics to an AI mood analysis service.
- **API Endpoint:** `https://api.mood-ai.com/v1/analyze`
- **Method:** POST
- **Authentication:** Header Bearer token using `moodApi.apiKey` credential.
- **Request Body:**
  ```json
  {
    "heartRate": "{{ $json.heartRate }}",
    "skinConductance": "{{ $json.skinConductance }}",
    "breathingRate": "{{ $json.breathingRate }}"
  }
  ```
- **Headers:** Includes `Content-Type: application/json`.
- **Output:** Mood analysis response JSON indicating the detected mood (e.g., stressed, anxious, calm).

---

### 4. Determine VR Environment Settings

- **Type:** Function
- **Purpose:** Maps the detected mood to VR environment parameters for customization.
- **Logic:**
  - If mood is **stressed**, sets soothing ambiance with ocean waves, muted lighting, and high relaxation.
  - If mood is **anxious**, sets calming forest birds soundscape with medium lighting and relaxation.
  - If mood is **calm**, sets serene wind chimes, brighter lighting, and low relaxation level.
  - Defaults to neutral minimal setting for unknown moods.
- **Output:** JSON object containing:
  - `ambientColor` (hex color code)
  - `soundscape` (sound environment)
  - `lightingIntensity` (float value)
  - `relaxationLevel` (string)

---

### 5. Update VR Environment

- **Type:** HTTP Request
- **Purpose:** Sends the VR environment customization parameters to the VR platform API.
- **API Endpoint:** `https://api.vr-meditation.com/v1/customizeEnvironment`
- **Method:** POST
- **Authentication:** Header Bearer token using `vrPlatformApi.apiKey` credential.
- **Headers:** Includes `Content-Type: application/json`.
- **Request Body:** Passes the environment settings JSON from the previous step.
- **Output:** Response from the VR platform confirming environment update.

---

### 6. Success Message

- **Type:** Function
- **Purpose:** Returns a success confirmation message.
- **Output:** JSON with message:  
  ```json
  {
    "message": "VR Meditation Environment updated successfully."
  }
  ```

---

## Workflow Execution Flow

1. **Get Biometric Data** fetches raw biometric sensor data.
2. Data flows into **Extract Biometric Metrics** for selecting key metrics.
3. Metrics are posted to **Analyze Mood via AI** for emotional state detection.
4. The mood result determines VR settings in **Determine VR Environment Settings**.
5. These settings are sent to **Update VR Environment** to customize the VR space.
6. Finally, **Success Message** confirms successful update completion.

---

## Credentials Required

- `biometricApi.apiKey` — API key for biometric sensor service.
- `moodApi.apiKey` — API key for mood AI analysis service.
- `vrPlatformApi.apiKey` — API key for VR meditation platform.

---

## Summary

This workflow seamlessly personalizes a VR meditation environment by analyzing live biometric data to infer mood and adjust sensory VR settings, improving meditation efficacy through adaptive ambiance.