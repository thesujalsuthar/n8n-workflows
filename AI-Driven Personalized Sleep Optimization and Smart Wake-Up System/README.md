# AI-Powered Personalized Sleep Optimization and Smart Alarm System

This workflow leverages Fitbit sleep data and smart home devices to provide personalized sleep improvement recommendations and schedule a smart alarm optimized to wake you during a light sleep phase, enhancing your waking experience.

---

## Workflow Overview

1. **Fetch Sleep Data**  
   Retrieves sleep activity data from your Fitbit account for the past 7 days.

2. **Analyze Sleep Patterns**  
   Processes the fetched data to calculate total sleep duration, duration in each sleep stage, and sleep efficiency.

3. **Generate Sleep Recommendations**  
   Creates personalized suggestions to improve sleep quality based on analyzed sleep metrics.

4. **Determine Optimal Wake-up Time**  
   Calculates an optimal wake-up time during a light sleep phase within the upcoming hour.

5. **Schedule Smart Light and Alarm**  
   Schedules your smart light to turn on and your smart speaker to play an alarm sound at the calculated wake-up time.

6. **Send Recommendations Notification**  
   Sends a push notification with your personalized sleep recommendations.

---

## Node Details

### 1. Fetch Sleep Data

- **Type:** Fitbit API Node  
- **Function:** Retrieves sleep activity data from Fitbit for the last 7 days using your access token.  
- **Parameters:**  
  - Date Range: From 7 days ago to today.

### 2. Analyze Sleep Patterns

- **Type:** Function Node  
- **Function:**  
  - Parses sleep segments from data input.  
  - Calculates total sleep time in minutes.  
  - Aggregates duration spent in each sleep stage (`deep`, `light`, `rem`, etc.).  
  - Computes simplified sleep efficiency assuming 8 hours time in bed.

### 3. Generate Sleep Recommendations

- **Type:** Function Node  
- **Function:** Generates personalized advice based on the following criteria:  
  - Total sleep less than 7 hours → Recommend increasing total sleep duration.  
  - Deep sleep under 60 minutes → Suggest reducing caffeine and stress.  
  - Sleep efficiency below 85% → Advice improving bedtime consistency and minimizing disruptions.  
  - If all metrics are good → Positive reinforcement message.

### 4. Determine Optimal Wake-up Time

- **Type:** Code Node  
- **Function:**  
  - Examines upcoming sleep segments within the next hour to find a light sleep period.  
  - Picks the start time of the next light sleep period as the optimal wake-up time.  
  - Defaults to 1 hour from current time if no suitable light sleep segment is found.  
  - Returns ISO formatted wake-up time.

### 5. Schedule Smart Light On

- **Type:** Smart Home Node  
- **Function:** Schedules your smart light to turn on at the optimal wake-up time with full brightness.  
- **Credentials:** Requires smart home API credentials.

### 6. Schedule Smart Alarm

- **Type:** Smart Home Node  
- **Function:** Schedules your smart speaker to play a predefined alarm sound at the optimal wake-up time.  
- **Credentials:** Requires smart home API credentials.

### 7. Send Recommendations Notification

- **Type:** Push Notification Node  
- **Function:** Sends a push notification containing the personalized sleep recommendations generated earlier.  
- **Credentials:** Requires push notification service credentials.

---

## Connections Flow

- **Fetch Sleep Data** output is fed into:
  - **Analyze Sleep Patterns** for detailed metrics.
  - **Determine Optimal Wake-up Time** to calculate alarm timing.

- **Analyze Sleep Patterns** output goes to:
  - **Generate Sleep Recommendations** to build advice.

- **Generate Sleep Recommendations** output goes to:
  - **Send Recommendations Notification** to push suggestions to user devices.

- **Determine Optimal Wake-up Time** output goes to:
  - **Schedule Smart Light On** to plan light activation.
  - **Schedule Smart Alarm** to plan alarm sound.

---

## Credentials Setup

Before activating the workflow, ensure you have the following credentials configured in n8n:

- **Fitbit API Credentials:** Access token with permission to read sleep data.
- **Smart Home API Credentials:** Access to control smart lights and smart speakers.
- **Push Notification API Credentials:** For sending push notifications to your devices.

---

## Usage Notes

- This workflow assumes consistent Fitbit sleep tracking data is available.  
- Smart home devices must be compatible and properly connected with the provided credentials.  
- The alarm timing is optimized to wake you during a light sleep phase within the next hour for a more refreshed awakening.  
- Sleep efficiency calculation is a simplified estimation and may be customized for more detailed analytics.

---

## Troubleshooting

- **No Sleep Data Available:** Ensure Fitbit device is worn consistently and syncing properly.  
- **Smart Devices Not Responding:** Verify API credentials and device connectivity.  
- **Notifications Not Received:** Confirm push notification service setup and permissions.

---

This workflow provides a seamless integration of data analysis and smart home automation, aiming to improve sleep quality and optimize your morning routine with AI assistance.