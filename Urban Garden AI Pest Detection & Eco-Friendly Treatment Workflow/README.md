# AI-Powered Smart Urban Garden Pest Detection and Eco-Friendly Treatment Recommendation System

This workflow leverages AI and IoT sensor data to detect pests in urban gardens and recommend eco-friendly treatments, delivering timely notifications to the user.

---

## Workflow Overview

The system continuously monitors the garden through scheduled triggers, fetching images and sensor data. AI models analyze both visual and sensor inputs to identify potential pest infestations. Identifications are combined, and eco-friendly treatments are mapped to detected pests. Finally, the system sends a notification summarizing the findings and recommendations.

---

## Node Breakdown

### 1. Schedule Trigger
- **Type:** Schedule Trigger
- **Description:** Initiates the workflow at configured intervals to automate periodic garden monitoring.
- **Position:** (150, 300)

### 2. Fetch Garden Images
- **Type:** HTTP Request
- **Purpose:** Retrieves the latest garden images for visual pest inspection.
- **Data:** Requests images as binary data with property name `image`.
- **Position:** (350, 180)

### 3. Read Sensor Data
- **Type:** Custom API
- **Purpose:** Pulls real-time sensor data (e.g., humidity, temperature, pest-related signals) from the garden sensors.
- **Operation:** `getAll` on `sensor` resource.
- **Position:** (350, 420)

### 4. AI Pest Identification
- **Type:** AI Node
- **Purpose:** Processes fetched garden images through a custom AI model (`custom-pest-detection-model`) to identify visible pests.
- **Input:** Image data from "Fetch Garden Images".
- **Position:** (550, 180)

### 5. AI Pest Detection from Sensors
- **Type:** AI Node
- **Purpose:** Analyzes sensor data using a custom AI model (`custom-pest-identification-model`) to detect pest presence based on environmental signals.
- **Input:** Sensor data from "Read Sensor Data".
- **Position:** (550, 420)

### 6. Combine Identifications
- **Type:** Function
- **Purpose:** Aggregates pest detections from both AI analyses into a single list.
- **Code Summary:** Merges `pestIdentification` results from image and sensor AI nodes.
- **Position:** (750, 300)

### 7. Evaluate Eco-Friendly Treatments
- **Type:** HTTP Request
- **Purpose:** (Placeholder Step) Meant to fetch additional eco-friendly treatment data for identified pests if an external service is used.
- **Input:** Pest names.
- **Position:** (950, 300)

### 8. Map Treatments Locally
- **Type:** Function
- **Purpose:** Maps identified pests to predefined eco-friendly treatments locally without external API calls.
- **Treatment Database Examples:**
  - Aphids: Neem oil spray, Introduce ladybugs
  - Whiteflies: Sticky traps, Companion planting with marigolds
  - Caterpillars: Bacillus thuringiensis (Bt) application, Handpicking
  - Mites: Insecticidal soap, Increase humidity
  - Scale insects: Horticultural oil, Prune affected branches
- **Position:** (1150, 300)

### 9. Notify User
- **Type:** Pushbullet Notification
- **Purpose:** Sends a high-priority notification summarizing detected pests and recommended eco-friendly treatments.
- **Message Format:** Lists each pest with its associated treatments.
- **Credentials Required:** Pushbullet API token.
- **Position:** (1350, 300)

---

## Execution Flow

1. **Schedule Trigger** activates the workflow on predefined times.
2. It concurrently triggers:
   - **Fetch Garden Images** → feeds to → **AI Pest Identification**
   - **Read Sensor Data** → feeds to → **AI Pest Detection from Sensors**
3. Both AI nodes pass their outputs to **Combine Identifications** to aggregate pest detection results.
4. Combined pest list is sent through:
   - **Evaluate Eco-Friendly Treatments** (optional/extensible HTTP request for treatment data)
   - **Map Treatments Locally** which cross-references pests with eco-friendly treatment options.
5. Finally, **Notify User** dispatches the recommendations via Pushbullet.

---

## Prerequisites & Configuration

- **AI Models:** Custom-trained models must be deployed and accessible via the AI node configurations:
  - `custom-pest-detection-model`
  - `custom-pest-identification-model`
- **Sensor API:** Custom API credential and endpoint configuration to fetch sensor readings.
- **Pushbullet API:** A valid API token is required for the notification node.
- **HTTP Request Nodes:** Configure endpoints for image fetching and optionally for treatment evaluation.

---

## Notes

- Eco-friendly treatments mapping can be extended in the `Map Treatments Locally` function.
- The **Evaluate Eco-Friendly Treatments** node is currently a placeholder and can be integrated with external treatment recommendation APIs if needed.
- Schedule intervals should be carefully chosen based on garden monitoring needs.

---

This automated approach helps urban gardeners maintain healthy plants by quickly identifying pests and applying safe, effective treatments without manual inspection.