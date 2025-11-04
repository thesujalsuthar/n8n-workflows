# AI-Powered Smart Urban Garden AI Pest Detection & Eco-Friendly Treatment Scheduler

This workflow automates the monitoring and management of pests in an urban garden using AI-driven image recognition and schedules eco-friendly pest treatments while notifying gardeners promptly.

---

## Workflow Overview

The workflow runs every 6 hours and follows these steps:

1. **Capture Garden Image**  
   Captures the latest image from the garden camera (`urban_garden_cam_01`).

2. **AI Pest Detection**  
   Sends the captured image to an AI image recognition API to detect pests.

3. **Parse & Recommend Treatment**  
   Parses the AI response to extract detected pest types, confidence levels, and locations. It recommends eco-friendly treatments based on the pest type.

4. **Schedule Treatment**  
   Sets a treatment schedule exactly 1 hour after the detection.

5. **Notify Gardeners**  
   Sends an email notification to gardeners with pest details, confidence score, recommended treatment, and scheduled treatment time.

---

## Nodes Description

### 1. Every 6 Hours Trigger  
- **Type:** Cron trigger  
- **Schedule:** Runs every 6 hours (UTC timezone)  
- **Purpose:** Initiates the workflow periodically for continuous monitoring.

### 2. Capture Garden Image  
- **Type:** HTTP Request  
- **Operation:** Captures image from camera resource  
- **Camera ID:** `urban_garden_cam_01`  
- **Description:** Fetches a real-time image snapshot of the urban garden to be used in pest detection.

### 3. AI Pest Detection  
- **Type:** HTTP Request  
- **API Endpoint:** `https://api.example-ai.com/v1/image-recognition/pests`  
- **Method:** POST  
- **Authentication:** Bearer token (replace `YOUR_AI_API_KEY` with your actual API key)  
- **Payload:** Captured image JSON body from previous node  
- **Response:** JSON formatted pest detection data  

### 4. Parse & Recommend Treatment  
- **Type:** Function  
- **Logic:**  
  - Extracts pest detection data from AI response.  
  - Maps pest types to recommended eco-friendly treatments:  
    - Aphids → Neem oil spray  
    - Whiteflies → Insecticidal soap  
    - Spider mites → Horticultural oil  
    - Caterpillars → Bt (Bacillus thuringiensis)  
    - Thrips → Sticky traps and neem oil  
    - Mealybugs → Alcohol spray  
    - Others → Manual inspection needed  
  - Returns structured data including pest type, confidence, location, treatment, and timestamp.

### 5. Schedule Treatment  
- **Type:** Wait node  
- **Delay:** 1 hour from detection time  
- **Purpose:** Schedules the treatment action with a delay to allow preparatory steps or resource mobilization.

### 6. Notify Gardeners  
- **Type:** Email Send  
- **Recipient:** `gardeners@example.com` (replace with actual recipients)  
- **Subject:** Pest Detection Alert  
- **Message:** Includes pest type, confidence percentage, recommended treatment, and scheduled treatment time for immediate attention.

---

## Setup Instructions

1. **Configure the Camera**  
   Ensure your urban garden camera is accessible and configured with the ID `urban_garden_cam_01`.

2. **Set API Key**  
   Replace `YOUR_AI_API_KEY` in the **AI Pest Detection** node with your valid AI image recognition service API key.

3. **Email Setup**  
   Configure the email credentials in the **Notify Gardeners** node and update the recipient email address as needed.

4. **Timezone Configuration**  
   The cron trigger is set to UTC by default; adjust in node settings if your operation requires a different timezone.

5. **Activate Workflow**  
   Enable the workflow to start automatic pest detection and treatment scheduling every 6 hours.

---

## Notes

- Pest detection confidence and treatment recommendations help prioritize eco-friendly pest management.  
- This workflow assumes availability of an AI pest detection API endpoint that accepts images and returns JSON detections.  
- Customize treatment recommendations in the function code as per your garden's pest management policies.

---

By using this workflow, urban gardeners can efficiently monitor pest infestations and schedule environmentally safe treatments with timely notifications, ensuring healthier plants and sustainable gardening practices.