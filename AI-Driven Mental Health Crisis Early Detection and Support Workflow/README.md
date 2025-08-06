# AI-Powered Mental Health Crisis Early Warning and Support System

## Overview

This workflow is designed to monitor early signs of mental health crises by aggregating data from multiple sources, analyzing risk, and providing timely support and alerts. It integrates social media sentiment analysis, user self-reports, and wearable device vitals to assess mental health risk and triggers personalized notifications and resources if a high risk is detected.

---

## Workflow Components

### 1. Social Media Sentiment Analysis
- **Type:** HTTP Request  
- **Purpose:** Fetches sentiment data related to keywords such as "mental health," "crisis," "stress," and "anxiety" from social media over the past hour.  
- **Endpoint:** `https://api.socialmedia.com/v1/sentiment`  
- **Request Method:** GET  
- **Parameters:**  
  - `keywords`: `"mental health, crisis, stress, anxiety"`  
  - `time_range`: `"last_1_hour"`

### 2. User Self-Reports
- **Type:** Google Sheets Read Operation  
- **Purpose:** Reads self-reported stress and anxiety levels from users stored in a Google Sheet (`user_self_reports_sheet_id`).  
- **Sheet Range:** `A2:D100`  
- **Data:** Includes stress/anxiety level inputs for the last collected data points.

### 3. Wearable Device Vitals
- **Type:** Wearable API  
- **Purpose:** Retrieves vital signs data (e.g., heart rate, heart rate variability (HRV)) from wearable devices of users over the last hour.  
- **Device IDs:** `"wearable_device_user_1", "wearable_device_user_2"`  
- **Authentication:** OAuth2  

### 4. Risk Assessment Function
- **Type:** Custom Function Node  
- **Purpose:** Aggregates and analyzes all input data to calculate a crisis risk score with the following logic:
  - Social media sentiment weighted 40%
  - Average self-reported stress level weighted 40%
  - Abnormal vitals (heart rate > 100 BPM or HRV < 20) weighted 20%
- **Outputs:**  
  - `crisisRiskScore` (score from 0 to 1)  
  - `highRisk` (boolean flag if risk score > 0.7)

### 5. Check High Risk
- **Type:** Conditional If Node  
- **Purpose:** Checks if the `highRisk` flag is true to trigger urgent intervention flows.

### 6. Send Support Message
- **Type:** Email Send  
- **Purpose:** Sends an immediate personalized email to the user detected at high risk, containing supportive messages and links to coping resources.  
- **Email Content:**  
  - Subject: "Immediate Mental Health Support"  
  - Support resources links and contact info for professional help  

### 7. Alert Caregiver
- **Type:** Email Send  
- **Purpose:** Sends an alert email to the caregiver notifying them that the user is showing early signs of mental health distress.  
- **Recipient:** Caregiver email (`caregiver@example.com`)  
- **Email Content:** Notification message requesting caregiver check-in.

### 8. Suggest Professional Resources
- **Type:** External API Search  
- **Purpose:** Searches for up to 3 nearby professional mental health resources.  
- **Query:** `"mental health professional near user location"`

### 9. Format Professional Resources
- **Type:** Function  
- **Purpose:** Formats the retrieved professional resources for inclusion in an outgoing message.

### 10. Send Professional Resources
- **Type:** Email Send  
- **Purpose:** Sends an email to the user with a curated list of recommended professional mental health resources nearby.  
- **Email Content:**  
  - Subject: "Professional Mental Health Resources"  
  - List of professionals with contact details formatted as bullet points

---

## Workflow Execution Flow

1. Data is fetched simultaneously from social media sentiment analysis, user self-reports, and wearable device vital signs.  
2. These inputs are passed to the Risk Assessment Function to calculate the overall crisis risk score.  
3. If the crisis risk score exceeds the threshold (0.7), the workflow:  
   - Sends personalized support messages to the user.  
   - Notifies the user's caregiver.  
   - Searches and sends professional mental health resource recommendations to the user.  

---

## Notes

- The system relies on real-time or near-real-time data inputs.  
- The risk calculation weights can be adjusted based on model training or customization needs.  
- Email addresses (`user_email` and caregiver) should be properly mapped and secured in the system handling personal data.  
- The wearable device data requires OAuth2 authentication for secure access.  
- Support resources and professional contacts should be maintained and updated regularly for accuracy.  

---

## License

This workflow is for educational and integration purposes. Ensure compliance with data privacy, medical, and ethical guidelines when deploying in production environments.