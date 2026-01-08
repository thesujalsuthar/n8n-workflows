# Adaptive AI-Powered VR Meditation Environment Designer

This workflow is designed to create a personalized VR meditation session tailored to an individual's current biometric data. Using AI-powered analysis and configuration generation, it adapts the VR environment in real-time to effectively reduce stress.

---

## Workflow Overview

1. **Receive Biometric Data**
2. **Evaluate Stress Level**
3. **Generate Adaptive VR Environment Config**
4. **Parse VR Environment Config**
5. **Prepare VR Session Payload**
6. **Start VR Meditation Session**
7. **Success Confirmation**

---

## Detailed Node Descriptions

### 1. Receive Biometric Data

- **Type:** HTTP Request (POST)
- **Purpose:** Collects biometric information such as heart rate, skin conductance, and brain wave data via an HTTP POST request at the `/biometric-data` endpoint.
- **Input:** JSON object containing biometric metrics.
- **Output:** Raw biometric data JSON passed to the next step.

---

### 2. Evaluate Stress Level

- **Type:** Function
- **Purpose:** Calculates a stress score based on the biometric inputs using a heuristic approach.
- **Key Processes:**
  - Extracts heart rate, skin conductance, and brain wave beta values.
  - Computes stress score by weighting these metrics:
    - Heart rate above 75 bpm contributes proportionally.
    - Skin conductance above 0.6 contributes scaled linearly.
    - Brain wave beta values add to the score.
  - Stress score is clamped between 0 and 100.
- **Output:** JSON including heart rate, skin conductance, brain wave data, and computed stress score.

---

### 3. Generate Adaptive VR Environment Config

- **Type:** OpenAI GPT-4 Integration
- **Purpose:** Sends biometric data and stress score to GPT-4 to generate a VR meditation environment configuration.
- **System Message:** Positions the AI as an expert assistant for designing adaptive VR meditation environments based on biometric input and stress levels.
- **User Message:** Provides biometric numbers and requests a customized environment configuration including:
  - Visuals
  - Ambient sounds
  - Guided meditation style
  - Session length
- **Output:** Raw AI-generated JSON configuration content.

---

### 4. Parse VR Environment Config

- **Type:** Function
- **Purpose:** Parses the AI-generated text into a JSON object.
- **Fallback:** If the AI output is not valid JSON, encapsulate as text under the `configuration` key.
- **Output:** Parsed VR environment configuration JSON.

---

### 5. Prepare VR Session Payload

- **Type:** Function
- **Purpose:** Creates the payload format expected by the VR system for starting the session.
- **Includes:**
  - Visuals (default: Calm nature scenes with flowing water and soft lighting)
  - Ambient sounds (default: Gentle forest sounds with birdcalls)
  - Guided meditation style (default: Breath awareness with progressive muscle relaxation)
  - Session length in minutes (default: 15)
- **Output:** JSON object with the `vrSessionPayload` key containing the prepared session details.

---

### 6. Start VR Meditation Session

- **Type:** HTTP Request (POST)
- **Purpose:** Sends the session payload to the VR service endpoint `https://vrmeditation.example.com/session/start` to initiate the VR meditation environment.
- **Request Body:** JSON-formatted session payload.
- **Output:** Response from VR system indicating session start status.

---

### 7. Success Confirmation

- **Type:** Function
- **Purpose:** Produces a confirmation message indicating that the adaptive VR meditation session has started successfully.
- **Output:** JSON message confirming session initiation along with details.

---

## How to Use

1. **Send Biometric Data:** Post a JSON object with biometric metrics to the `/biometric-data` HTTP endpoint. Example:

   ```json
   {
     "heartRate": 82,
     "skinConductance": 0.7,
     "brainWave": { "beta": 0.3, "alpha": 0.2 }
   }
   ```

2. **Workflow Execution:** The workflow will process data, generate a tailored VR meditation environment configuration, and start the session automatically.

3. **Receive Confirmation:** The final node returns a success message with details confirming that the VR meditation session has begun.

---

## Notes

- Default values are used for missing biometric data to ensure the workflow continuity.
- The adaptive VR configuration leverages GPT-4's capabilities for creative and context-aware environment design.
- This system is designed to help reduce stress effectively by dynamically tailoring the meditation experience according to real-time biometric feedback.

---

## Dependencies

- AI Integration with OpenAI GPT-4
- HTTP endpoint for receiving biometric data
- VR system API at `https://vrmeditation.example.com/session/start`

---

## License

This workflow is provided as-is for educational and development purposes. Customize and extend to suit your specific VR meditation platform and biometric devices.