# AI-Driven Personal Carbon Footprint and Sustainable Behavior Tracker

## Overview

This workflow enables tracking and reducing your personal carbon footprint by collecting daily user activity data, estimating associated carbon emissions using the Carbon Interface API, and generating personalized sustainability suggestions. It stores historical data in Google Sheets for progress tracking and sends motivational SMS reminders via Twilio.

---

## Workflow Breakdown

### 1. Daily Trigger

- **Node:** `Daily Trigger` (Cron)
- **Description:** Automatically triggers the workflow every day at 08:00 AM to send motivational SMS reminders based on the latest data.
  
---

### 2. Collect User Input

- **Node:** `Request User Input` (HTTP In)
- **Description:** Accepts POST requests containing user activity data in JSON format through a webhook at `user-activity-input`.
- **Expected Input Format Example:**

  ```json
  {
    "activities": [
      {
        "type": "car_travel",
        "amount": 20,
        "unit": "km",
        "description": "commute to work"
      }
    ],
    "phoneNumber": "+1234567890"
  }
  ```

---

### 3. Format Input Data

- **Node:** `Format Input Data` (Function)
- **Description:** Normalizes and structures user activity data into the format required by the Carbon Interface API.
- **Transforms input activities to:**

  ```json
  [
    {
      "activity_type": "car_travel",
      "amount": 20,
      "unit": "km",
      "description": "commute to work"
    }
  ]
  ```

---

### 4. Calculate Carbon Footprint

- **Node:** `Calculate Carbon Footprint` (HTTP Request)
- **Description:** Sends a POST request to [Carbon Interface API](https://carboninterface.com/) with formatted activity data to estimate carbon emissions.
- **Configuration:**
  - URL: `https://api.carboninterface.com/v1/estimates`
  - Authorization: Bearer token (`YOUR_CARBONINTERFACE_API_KEY`)
  - Request Body:
    ```json
    {
      "type": "custom",
      "parameters": {
        "activities": [ /* formatted activities array */ ]
      }
    }
    ```

---

### 5. Extract Footprint Value

- **Node:** `Extract Footprint Value` (Function)
- **Description:** Parses the API response to extract the total carbon footprint in kilograms (kg).

---

### 6. Generate Suggestions

- **Node:** `Generate Suggestions` (Function)
- **Description:** Generates personalized sustainable lifestyle suggestions based on the calculated footprint.
- **Logic:**
  - If footprint > 5 kg:
    - Suggest reducing car travel.
    - Recommend switching to renewable energy.
  - If footprint between 2 and 5 kg:
    - Suggest reducing meat consumption.
    - Recommend avoiding single-use plastics.
  - If footprint ≤ 2 kg:
    - Congratulate user on sustainable habits.
    - Encourage sharing tips.

---

### 7. Retrieve Existing User Data

- **Node:** `Get Existing User Data` (Google Sheets)
- **Description:** Reads historical user data from a Google Sheet for tracking progress.
- **Setup:**
  - Google Sheet ID: `YOUR_GOOGLE_SHEET_ID`
  - Range: `UserData!A:D`
  - Authorize with `Google Sheets OAuth2` credentials.

---

### 8. Prepare Data for Saving

- **Node:** `Prepare Data for Saving` (Function)
- **Description:** Appends today's date, carbon footprint, suggestions (joined as string), and a status ('pending') to the historic dataset.

---

### 9. Save Data to Google Sheets

- **Node:** `Save Data to Google Sheets` (Google Sheets)
- **Description:** Writes the updated dataset back to the Google Sheet for records and progress tracking.
- **Setup:**
  - Google Sheet ID: `YOUR_GOOGLE_SHEET_ID`
  - Range: `UserData!A:D`
  - Value Input Mode: `USER_ENTERED`

---

### 10. Send Motivational SMS

- **Node:** `Send Motivational SMS` (Twilio)
- **Description:** Sends a motivational SMS to the user with today's carbon footprint and a sustainability tip.
- **Setup:**
  - From Number: `YOUR_TWILIO_PHONE_NUMBER`
  - To Number: Extracted from user input (`phoneNumber` or `userPhone`)
  - Message Template:
    ```
    Keep up the great work! Reduce your carbon footprint by {{ carbon_kg }} kg today. Here's a tip: {{ first suggestion }}
    ```
  - Credentials: Configured Twilio API credentials.

---

### 11. Data Merging

- **Node:** `Merge User Input and Calculated Data` (Merge)
- **Description:** Combines original user input and calculated data streams for downstream use (e.g., logging, extended processing).

---

## Setup Instructions

1. **Carbon Interface API:**
   - Sign up at [carboninterface.com](https://www.carboninterface.com/).
   - Obtain an API key.
   - Replace `YOUR_CARBONINTERFACE_API_KEY` in the `Calculate Carbon Footprint` node headers.

2. **Google Sheets:**
   - Create a Google Sheet with a tab named `UserData`.
   - Columns A-D reserved for: Date, Carbon Footprint (kg), Suggestions, Status.
   - Obtain and configure Google Sheets OAuth2 credentials in n8n.
   - Replace `YOUR_GOOGLE_SHEET_ID` in relevant Google Sheets nodes.

3. **Twilio:**
   - Sign up at [twilio.com](https://www.twilio.com/).
   - Get a Twilio phone number.
   - Set up Twilio API credentials in n8n.
   - Enter your Twilio number in `Send Motivational SMS` node.
   - Make sure to obtain user phone numbers during data input or configure a default number for testing.

4. **Webhook:**
   - Expose n8n webhook endpoint publicly or via tunneling.
   - POST daily user activity data to `/webhook/user-activity-input`.

---

## Usage

- **Daily Data Input:** Send a POST request daily with your activity data to the webhook endpoint `/webhook/user-activity-input`.
- **Daily Motivation:** At 08:00 AM every day, receive an SMS summarizing your carbon footprint and a personalized tip.
- **Progress Tracking:** Check your Google Sheet to view historical data and track improvements over time.

---

## Customization

- Modify suggestion logic in `Generate Suggestions` node to suit your preferences.
- Adjust timing in the `Daily Trigger` node for SMS reminders.
- Extend activity types and data structure in `Format Input Data` node as needed.

---

## Notes

- Ensure all credentials are configured correctly in n8n.
- Sensitive information like API keys and phone numbers must be securely stored.
- This workflow assumes basic JSON knowledge for preparing activity data for input.

---

## License

This project is open for personal and educational use. Adjust configurations and code to fit your use case.