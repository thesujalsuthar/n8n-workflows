# SmartUrban Garden AI Pest Detection & Eco-Friendly Treatment Scheduler

This workflow automates the process of detecting garden pests from uploaded images, mapping detected pests to eco-friendly treatment schedules, evaluating optimal treatment timing based on current weather conditions, and sending timely email notifications to gardeners.

---

## Workflow Overview

The workflow consists of the following main steps:

1. **Receive Image Upload**  
   Accepts a POST request containing a garden image in Base64 format.

2. **Extract Base64 Image**  
   Extracts the Base64 encoded image data from the POST request body.

3. **AI Pest Detection API**  
   Sends the Base64 image to an AI-powered pest detection API which identifies pest types present in the garden image.

4. **Map Pest to Treatment**  
   Maps each detected pest to a recommended eco-friendly treatment plan including treatment type, frequency, and usage notes.

5. **Fetch Weather Data**  
   Retrieves current weather data for a specified location using the OpenWeather API to ensure treatments are applied under suitable environmental conditions.

6. **Evaluate Treatment Timing**  
   Determines if it is safe to proceed with treatments based on the weather conditions (e.g., avoids treatments during rain or storms) and generates treatment advice.

7. **Compose Notification Message**  
   Creates a detailed notification message containing pest-specific treatments and weather suitability advice.

8. **Send Email Notification**  
   Sends the composed notification message to the gardener’s email to remind them of treatment schedules and best practices.

---

## Detailed Node Description

### 1. Receive Image Upload  
- **Type:** HTTP Trigger (POST)  
- **Endpoint:** `/upload-image`  
- **Function:** Waits for incoming HTTP POST requests with image data.

### 2. Extract Base64 Image  
- **Type:** Function  
- **Function:** Extracts the `imageBase64` field from the request body and throws an error if missing.

### 3. AI Pest Detection API  
- **Type:** HTTP Request  
- **Purpose:** Sends the extracted Base64 image to the external AI pest detection API.  
- **API URL:** `https://api.example-ai-image-recognition.com/v1/pest-detection`  
- **Authentication:** Bearer token via header (`YOUR_API_KEY`)  
- **Content-Type:** `application/json`  
- **Request Body:** JSON with field `image_base64`.

### 4. Map Pest to Treatment  
- **Type:** Function  
- **Function:**  
  - Receives detected pest types from the AI API response.  
  - Maps each pest type to a predefined eco-friendly treatment plan, which includes:  
    - Treatment name  
    - Frequency of application (days)  
    - Special notes (e.g., avoid midday sun, apply early morning)  
- **Sample Treatments:**  
  - Aphids → Neem oil spray, every 7 days  
  - Spider mites → Insecticidal soap, every 5 days  
  - Whiteflies → Yellow sticky traps, every 10 days  
  - Caterpillars → Bt toxin spray, every 14 days

### 5. Fetch Weather Data  
- **Type:** HTTP Request  
- **API:** OpenWeather Current Weather Data  
- **Parameters:**  
  - `q`: Location (replace `YourCity,YourCountry`)  
  - `appid`: Your OpenWeather API key (`YOUR_OPENWEATHER_API_KEY`)  
  - `units`: Metric system (°C)  
- **Purpose:** To access current weather conditions that may affect treatment timing.

### 6. Evaluate Treatment Timing  
- **Type:** Function  
- **Logic:**  
  - Parses weather data to check for rain or storm conditions.  
  - Determines if it is safe to carry out pest treatments today.  
  - Generates actionable advice:  
    - Safe to treat if no adverse weather detected.  
    - Otherwise, recommend avoiding treatments.

### 7. Compose Notification Message  
- **Type:** Function  
- **Function:**  
  - Formats the treatment schedule and weather advice into a clear message.  
  - Includes pest names, treatment details, frequency, notes, and weather suitability advice.

### 8. Send Email Notification  
- **Type:** Email Send  
- **Parameters:**  
  - **From:** `garden-notifications@smarturban.com`  
  - **To:** Uses gardener’s email if provided in JSON (`gardenerEmail`), defaults to `gardener@example.com`.  
  - **Subject:** `SmartUrban Garden Treatment Reminder`  
  - **Body:** Contains the composed notification message.  
- **Authentication:** Header authentication (SMTP or API key as per setup).

---

## Configuration & Setup

- Replace all placeholder API keys:  
  - `YOUR_API_KEY` for AI Pest Detection API  
  - `YOUR_OPENWEATHER_API_KEY` for weather data  
- Update the city and country in the weather API node (`YourCity,YourCountry`) to match your garden location.
- Configure email credentials and sender settings based on your SMTP/email provider.
- Activate the workflow after configuration for production use.

---

## How to Use

1. Send a POST HTTP request to the `/upload-image` endpoint containing a JSON body with a Base64 encoded garden image under the field `imageBase64`.
2. The workflow will process the image, detect pests, generate eco-friendly treatment recommendations, check local weather, and determine optimal treatment times.
3. A detailed email notification will be sent to the gardener summarizing the pest situation and treatment advice.

---

## Example Input (POST to `/upload-image`)

```json
{
  "imageBase64": "data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAYABgAAD..."
}
```

---

## Expected Output

An email summarizing:

- Detected pests and recommended eco-friendly treatments with application frequency and notes.
- Whether current weather conditions support treatment.
- Clear instructions on when and how to apply treatments safely.

---

## Notes

- Ensure all API keys are securely stored and not hard-coded in production.
- This workflow is designed for smart urban gardens looking to use AI and eco-friendly pest control methods efficiently.
- Modify treatment mappings as needed to reflect local pest species and eco-friendly options.

---

*SmartUrban Garden AI Pest Detection & Eco-Friendly Treatment Scheduler* empowers gardeners with AI insights and environmental awareness to maintain healthy, sustainable urban gardens.