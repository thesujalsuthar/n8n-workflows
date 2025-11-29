# Smart Urban Garden Automation Workflow

This workflow automates the monitoring and care of an urban garden by integrating sensor data, weather forecasts, AI-driven plant health analysis, and messaging alerts. It enables smart watering decisions, pest and disease detection, treatment scheduling, and personalized gardening tips delivered via Telegram.

---

## Workflow Overview

### 1. Soil Moisture Monitoring
- **Node:** Soil Moisture Sensor  
- **Function:** Reads soil moisture data every 5 minutes from device `garden-soil-moisture-sensor-01`.  
- **Output:** Moisture level data used for watering decisions and gardening tips.

### 2. Garden Camera Imaging
- **Node:** Garden Camera  
- **Function:** Captures garden images every 10 minutes from device `garden-camera-01`.  
- **Output:** Image URL sent for AI analysis.

### 3. Weather Forecast Retrieval
- **Node:** Weather Forecast API  
- **Function:** Fetches 5-day weather forecast for "Urban City" every run using OpenWeatherMap API with metric units.  
- **Output:** Rainfall forecast data used to improve watering decisions.

### 4. Watering Decision Logic
- **Node:** Watering Decision Logic (Function)  
- **Logic:**  
  - Reads soil moisture from Soil Moisture Sensor  
  - Calculates total rainfall predicted for the next 12 hours (sum of rainfall from the next 4 forecast intervals)  
  - Decision rule: Watering is triggered if soil moisture is below 30% AND predicted rain is less than 2mm.  
- **Output:** Boolean flag `shouldWater`, soil moisture, and rain sum.

### 5. Activate Watering System
- **Node:** Activate Watering System  
- **Function:** Turns on watering system `garden-watering-system-01` for 5 minutes (300 seconds) if watering is needed.

### 6. Extract Camera Image URL
- **Node:** Extract Camera Image URL (Function)  
- **Function:** Extracts the image URL from the camera node output for AI analysis.

### 7. Plant Health & Pest Detection AI
- **Node:** Plant Health & Pest Detection AI  
- **Function:** Sends the garden image to an AI model (`general-detection`) to detect pests, diseases, and nutrient deficiencies.

### 8. Analyze AI Detection Results
- **Node:** Analyze AI Detection Results (Function)  
- **Logic:**  
  - Filters detections into pests and plant health issues (disease/nutrient deficiency)  
  - Creates alert messages listing detected pests and plant issues.

### 9. Prepare Pest/Health Alert Message
- **Node:** Prepare Alert Message (Function)  
- **Function:** Compiles and formats alert messages for detected issues.  
- **Output:** Formatted alert string for messaging.

### 10. Send Pest/Health Alerts
- **Node:** Send Pest/Health Alerts  
- **Function:** Sends alert messages via Telegram to `garden-owner-chat-id` using Telegram API.

### 11. Generate Treatment Schedule
- **Node:** Generate Treatment Schedule (Function)  
- **Logic:**  
  - If pests detected, schedules eco-friendly pesticide treatment 1 hour from now  
  - If plant health issues detected, schedules natural fertilizer treatment 2 hours from now  
- **Output:** Treatment schedule array.

### 12. Send Treatment Schedule Alert
- **Node:** Send Treatment Schedule Alert  
- **Function:** Sends the treatment schedule details as a Telegram message to the garden owner.

### 13. Generate Personalized Gardening Tips
- **Node:** Generate Personalized Gardening Tips (Function)  
- **Logic:**  
  - Provides tips based on soil moisture levels: low, optimal, or high  
  - Advises on pest presence  
  - Adds seasonal general gardening tips based on current month  
- **Output:** Formatted gardening tips string.

### 14. Send Gardening Tips
- **Node:** Send Gardening Tips  
- **Function:** Sends the personalized gardening tips via Telegram.

---

## Authentication & Credentials

- **OpenWeatherMap API Credentials:** Required for the Weather Forecast API node (`openWeatherMapApi`).  
- **Telegram API Credentials:** Required for sending messages (`telegramApi`).  
- Ensure credentials are properly configured in n8n's credentials manager.

---

## Node Connections Summary

- Soil Moisture Sensor → Watering Decision Logic & Generate Personalized Gardening Tips  
- Weather Forecast API → Watering Decision Logic  
- Watering Decision Logic → Activate Watering System (if watering needed)  
- Garden Camera → Extract Camera Image URL → Plant Health & Pest Detection AI → Analyze AI Detection Results  
- Analyze AI Detection Results → Prepare Alert Message → Send Pest/Health Alerts  
- Analyze AI Detection Results → Generate Treatment Schedule → Send Treatment Schedule Alert  
- Analyze AI Detection Results → Generate Personalized Gardening Tips → Send Gardening Tips

---

## Usage Notes

- Adjust sensor device IDs and Telegram chat IDs according to your setup.  
- Customize watering thresholds, AI detection models, and treatment schedules as needed.  
- The workflow runs periodically based on sensor data frequency and API polling intervals.  
- Ensure network connectivity for API calls and device control commands.

---

## Summary

This workflow enables a fully automated, intelligent urban garden management system that monitors soil and plant health, accounts for weather forecasts, controls irrigation smartly, detects pests and diseases with AI, schedules treatments, and communicates all relevant alerts and tips to the gardener through Telegram messages.