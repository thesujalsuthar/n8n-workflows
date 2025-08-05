# AI-Powered Plant Disease Detection and Treatment Recommendation System

This workflow enables automatic detection of plant diseases from images, generates treatment recommendations, stores historical data, and sends notifications to users. It also analyzes trends in plant health based on historical data.

---

## Workflow Overview

The workflow consists of the following main components:

1. **Image Upload via Webhook**
2. **Disease Detection API Call**
3. **Treatment Recommendation Generation**
4. **Storing Historical Records**
5. **Email Notification to Users**
6. **Fetching and Analyzing Historical Data**
7. **Sending Trend Notifications**

---

## Nodes Description

### 1. Webhook - Image Upload

- **Type:** Webhook (HTTP POST)
- **Purpose:** Receives plant image uploads from users.
- **Webhook Path:** `/upload-plant-image`
- **Input:** Binary image data.

### 2. Disease Detection API

- **Type:** HTTP Request (POST)
- **Purpose:** Sends the uploaded image to a third-party AI API for disease detection.
- **Endpoint:** `https://api.example-plant-disease.ai/v1/detect`
- **Authentication:** HTTP Basic Auth (configured credentials).
- **Request Format:** Form data containing image binary data.
- **Response:** JSON containing detected disease information.

### 3. Generate Treatment Recommendation

- **Type:** Function
- **Purpose:** Maps detected disease names to treatment advice and severity.
- **Treatment Map Example:**

  | Disease         | Treatment                                                    | Severity |
  |-----------------|--------------------------------------------------------------|----------|
  | Powdery Mildew  | Apply sulfur-based fungicide weekly and ensure air circulation.| moderate |
  | Leaf Spot       | Remove affected leaves and use copper fungicide.             | mild     |
  | Blight          | Remove infected plants and apply appropriate bactericide.    | severe   |
  | Healthy         | No treatment needed. Plant is healthy.                        | none     |

- If disease is unknown, advises consulting an expert.

### 4. Store Historical Data

- **Type:** Postgres Insert
- **Purpose:** Saves records of detection events into the `history` table.
- **Table Fields:**
  - `timestamp` - Detection time
  - `disease_name` - Detected disease name
  - `severity` - Disease severity
  - `treatment` - Recommended treatment
  - `image_reference` - Filename or tag of uploaded image
  - `raw_result` - JSON string of raw detection result

### 5. Send Email Notification

- **Type:** Email Send
- **Purpose:** Notifies users of detected diseases and recommended treatments.
- **Recipient:** User's email (defaults to `user@example.com` if not provided)
- **Subject:** Includes disease name, e.g., "Plant Disease Detection Alert: Blight Detected"
- **Body:** Includes detected disease, severity, and treatment recommendation.

### 6. Fetch Historical Data

- **Type:** Postgres Query (Get All)
- **Purpose:** Retrieves all historical detection records for trend analysis.
- **Table:** `history`

### 7. Analyze Trends

- **Type:** Function
- **Purpose:** Aggregates historical data by counting occurrences of each disease.
- **Output:** JSON object summarizing counts of each disease detected historically.

### 8. Send Trend Notification

- **Type:** Pushbullet Notification
- **Purpose:** Sends push notifications with latest trend analysis to user devices.
- **Recipient:** User's Pushbullet account/email (defaults to `user@example.com` if not provided)
- **Message:** Includes JSON string of disease trend counts.
- **Priority:** High

---

## Credentails Required

- **HTTP Basic Auth:** For accessing the Plant Disease Detection API.
- **Postgres:** Database credentials for accessing and manipulating plant health data.
- **SMTP:** Email server credentials for sending notifications.
- **Pushbullet API:** Access token for sending push notifications.

---

## Execution Flow

1. A user uploads a plant image via POST to `/upload-plant-image`.
2. The image is sent to the Plant Disease Detection AI API.
3. The system processes the AI response to generate a suitable treatment recommendation.
4. Detection event data and raw results are stored in the Postgres database.
5. An email notification is sent to the user with disease info and treatment advice.
6. Separately, the system fetches all historical data and analyzes trends.
7. Results of trend analysis are shared via Pushbullet notifications regularly.

---

## Database Schema (Table: `history`)

| Column          | Type         | Description                           |
|-----------------|--------------|-------------------------------------|
| `timestamp`     | BIGINT / TIMESTAMP | Time of detection                  |
| `disease_name`  | VARCHAR      | Name of detected disease             |
| `severity`      | VARCHAR      | Disease severity (e.g., mild, severe)|
| `treatment`     | TEXT         | Recommended treatment instructions   |
| `image_reference`| VARCHAR     | Reference or file name of uploaded image |
| `raw_result`    | JSON/Text    | Raw JSON result from disease detection API|

---

## Customization Tips

- Extend the treatment mapping in the "Generate Treatment Recommendation" node to support additional diseases.
- Update endpoints and credentials as per API/service providers.
- Modify email and notification message templates to fit branding or localization needs.
- Adjust the historical data analysis function for more complex trend insights.

---

## Important Notes

- Ensure all credentials are properly configured and secured.
- The disease detection API URL is a placeholder and should be replaced with a valid endpoint.
- The workflow assumes a prepared Postgres database with the specified schema.
- Image upload expects multipart/form-data POST requests with the image file under the `image` key.
- Error handling and retries are recommended to be implemented as per operational requirements.

---

This workflow automates the crucial steps of plant health monitoring, making it easier for users to receive timely disease detection and treatment guidance through AI-powered analysis and notifications.