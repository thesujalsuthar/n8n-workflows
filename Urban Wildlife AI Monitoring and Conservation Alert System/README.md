# AI-Driven Smart Urban Wildlife Observation and Conservation Assistant

## Overview
This workflow integrates AI-powered wildlife image recognition with real-time environmental data to monitor urban wildlife and promote conservation efforts. When a user uploads a wildlife image, the workflow identifies the species, gathers relevant air quality information for the specified city, and then distributes the combined data through community alerts on Telegram and posts to a dedicated Slack channel.

---

## Workflow Components

### 1. Upload Wildlife Image
- **Type**: Manual Trigger
- **Function**: Starts the workflow by allowing users to upload an image of urban wildlife.
- **Binary Property Name**: `wildlifeImage`
- **Position**: (50, 100)

### 2. Fetch Environmental Data
- **Type**: HTTP Request
- **Function**: Retrieves the latest air quality data for a specific city using the OpenAQ public API.
- **API Endpoint**: `https://api.openaq.org/v2/latest?city=YOUR_CITY&parameter=pm25,pm10,co,no2,o3`
- **Parameters**:
  - Replace `YOUR_CITY` with the desired city name.
  - Pollutants retrieved: PM2.5, PM10, CO, NO2, O3.
- **Response Format**: JSON
- **Position**: (250, 300)

### 3. Wildlife Image Recognition
- **Type**: HTTP Request
- **Function**: Sends the uploaded wildlife image to an external AI API for species identification.
- **API Endpoint**: `https://api.wildlifeimagerecognition.com/v1/identify`
- **Method**: POST
- **Headers**:
  - `Authorization`: Bearer token (`YOUR_API_KEY`)
  - `Content-Type`: application/json
- **Body**:
  - Passes the wildlife image data as JSON in the format: `{"image_url": "<image_data>"}`
- **Response Format**: JSON
- **Position**: (450, 100)

### 4. Aggregate Data
- **Type**: Function
- **Function**: Combines the species identification results with the environmental data.
- **Details**:
  - Extracts species name and confidence level from image recognition results.
  - Calculates and structures relevant pollutant values (PM2.5, PM10, CO, NO2, O3).
- **Output**:
  ```json
  {
    "species": "Species Name or 'Unknown'",
    "confidence": "Confidence Score",
    "airQualityIndex": {
      "pm25": value,
      "pm10": value,
      "co": value,
      "no2": value,
      "o3": value
    }
  }
  ```
- **Position**: (650, 200)

### 5. Send Community Alert (Telegram)
- **Type**: Telegram Node
- **Function**: Sends an alert message to a specified Telegram chat with wildlife and environmental information.
- **Parameters**:
  - Chat ID: `YOUR_TELEGRAM_CHAT_ID`
  - Message Text:
    ```
    New urban wildlife sighting detected:
    Species: <species>
    Confidence: <confidence>
    Environmental conditions (AQI approx.): PM2.5: <pm25>, PM10: <pm10>, CO: <co>, NO2: <no2>, O3: <o3>

    Let's promote urban conservation together!
    #UrbanWildlife #CitizenScience
    ```
  - Notification: Enabled (notifications are not disabled)
- **Credentials**: Requires Telegram API credentials (`YOUR_TELEGRAM_CREDENTIAL_ID`)
- **Position**: (850, 300)

### 6. Post to Slack Channel
- **Type**: Slack Node
- **Function**: Posts a detailed message to a Slack channel dedicated to urban wildlife observations.
- **Parameters**:
  - Channel: `#urban-wildlife-observations`
  - Username: `UrbanWildlifeBot`
  - Message Text:
    ```
    Species Detected: <species>
    Confidence: <confidence>
    Air Quality Details: PM2.5: <pm25>, PM10: <pm10>, CO: <co>, NO2: <no2>, O3: <o3>
    Timestamp: <current ISO timestamp>

    Encouraging citizen science and urban conservation.
    ```
- **Credentials**: Requires Slack API credentials (`YOUR_SLACK_CREDENTIAL_ID`)
- **Position**: (850, 100)

---

## Workflow Execution Flow

1. **Start**: The process begins when a user manually uploads a wildlife image in the **Upload Wildlife Image** node.
2. **Parallel Data Fetch**: Upon trigger,
   - The image data flows into **Wildlife Image Recognition** for species identification.
   - Simultaneously, **Fetch Environmental Data** requests current air quality data for the chosen city.
3. **Data Aggregation**: Outputs from both the recognition and environmental data nodes converge at the **Aggregate Data** node where key information is extracted and compiled.
4. **Notification & Posting**: The aggregated results are then:
   - Sent as a community alert message to a Telegram chat.
   - Posted as a formatted message to the Slack channel for wider public awareness and collaborative conservation efforts.

---

## Setup & Configuration

### Prerequisites
- An active n8n instance to run this workflow.
- Access to the **OpenAQ API** (no authentication required).
- API key for the **Wildlife Image Recognition** service.
- Telegram bot setup with an API key and target chat ID.
- Slack app credentials with permission to post messages to the intended Slack channel.

### Important Configuration Steps
- **OpenAQ URL**: Replace `YOUR_CITY` in the `Fetch Environmental Data` node URL with the target city name.
- **Wildlife Image Recognition API Key**: Insert your key in the `Authorization` header value of the `Wildlife Image Recognition` node.
- **Telegram**:
  - Replace `YOUR_TELEGRAM_CHAT_ID` with your target chat ID.
  - Set your Telegram API credentials in the node credentials using your Telegram bot token.
- **Slack**:
  - Specify your Slack channel (default is `#urban-wildlife-observations`).
  - Insert your Slack credential ID in the Slack node credentials.

---

## Usage

1. Begin by triggering the workflow manually through the **Upload Wildlife Image** node.
2. Upload an image file of urban wildlife.
3. The workflow automatically processes the image, fetches environmental data, aggregates the information, and posts it to community channels.
4. Monitor the Telegram chat and Slack channel for real-time wildlife observation updates and environmental alerts.

---

## Benefits

- Enables real-time, AI-based identification of urban wildlife.
- Integrates environmental context using live air quality data.
- Boosts urban conservation awareness through instant community alerts.
- Facilitates citizen science by engaging local communities via popular messaging platforms.

---

## Support & Contribution

For issues or enhancements, please open an issue or submit a pull request within the repository hosting this workflow. Ensure to keep credentials and sensitive information secure and never commit them publicly.

---

*Together, let's support urban biodiversity and promote a healthier, more informed community through technology and collaboration.*