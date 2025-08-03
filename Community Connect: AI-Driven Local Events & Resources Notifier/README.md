# AI-Powered Local Community Resource Finder and Event Notifier

This workflow leverages AI, geocoding, and event APIs to deliver personalized local community resources and event notifications to users based on their location and interests.

---

## Workflow Overview

The workflow automates the process of identifying community events and resources tailored to user preferences, sending an email notification with relevant information, and following up with daily reminder push notifications.

---

## Workflow Nodes and Description

### 1. Start - Hardcoded Input
- **Type:** Function
- **Description:** Provides a hardcoded sample input with the user's email, location, and interests.
- **Output:**
  ```json
  {
    "userEmail": "user@example.com",
    "input": {
      "location": "New York, NY",
      "interests": ["health", "education"]
    }
  }
  ```

### 2. Extract User Input
- **Type:** Function
- **Description:** Extracts and parses the user's location and interests from the input.
- **Input:** JSON object containing `location` and `interests`.
- **Output:** JSON with separated `location` and `interests` fields.

### 3. Geocode Location
- **Type:** HTTP Request
- **Description:** Converts the user’s location query into geographic coordinates (latitude and longitude) using the Positionstack API.
- **API:** [Positionstack Forward Geocoding API](https://positionstack.com/documentation)
- **Parameters:**
  - `access_key`: Your Positionstack API key.
  - `query`: The user's location string.
  - `limit`: Number of results limited to 1 for precision.

### 4. Parse Geocode Result
- **Type:** Function
- **Description:** Parses the geocoding API response, extracts the first result's latitude and longitude, and appends the user's interests.
- **Error Handling:** Throws an error if no location is found.

### 5. Search Events
- **Type:** HTTP Request
- **Description:** Searches for upcoming events near the geocoded location matching the user's interests on Eventbrite.
- **API:** [Eventbrite Events Search API](https://www.eventbrite.com/platform/api#/reference/event-search/list/search-events)
- **Parameters:**
  - `token`: Your Eventbrite API token.
  - `location.latitude` & `location.longitude`: Coordinates from geocoding.
  - `q`: User interests joined by commas.
  - `expand`: Venue details expanded to include venue address.
  - `sort_by`: Sorted by date.
  - `start_date.range_start`: Events starting from the current date/time.

### 6. Parse Eventbrite Events
- **Type:** Function
- **Description:** Extracts relevant event data including event ID, name, local start time, venue address, and URL.
- **Output:** An array of simplified event objects.

### 7. Filter Community Resources
- **Type:** Function
- **Description:** Filters a mock dataset of community resources based on the user’s interests.
- **Resources Sample:** Local Food Bank, Neighborhood Library, Community Health Clinic.
- **Filtering:** Matches resource categories against user interests (case-insensitive).

### 8. Prepare Notification Message
- **Type:** Function
- **Description:** Combines event and filtered community resource data into a formatted notification message listing upcoming events and relevant resources.
- **Output:** A comprehensive text message to be sent via email.

### 9. Send Email Notification
- **Type:** Email Send
- **Description:** Sends an email to the user with the subject "Your Local Community Events and Resources" and the prepared notification message.
- **Parameters:**
  - `fromEmail`: `no-reply@communityfinder.app`
  - `toEmail`: User's email extracted from input.
  - `subject`: Email subject line.
  - `text`: Email body content.

### 10. Daily Trigger
- **Type:** Schedule Trigger
- **Description:** Triggers the subsequent reminder process daily at 09:00 (AM).

### 11. Prepare Reminder Message
- **Type:** Function
- **Description:** Prepares a simple reminder push notification message alerting the user to check their email for upcoming community events and volunteer opportunities.

### 12. Send Reminder Notification
- **Type:** Pushbullet Notification
- **Description:** Sends a push notification to the user's device with the daily reminder message.
- **Parameters:**
  - `to`: User's email.
  - `message`: Reminder text prepared in the previous step.

---

## Configuration and Setup

### Required API Keys
- **Positionstack API Key:** Needed for the geocoding service to convert user location into latitude and longitude.
- **Eventbrite API Token:** Required to access Eventbrite's event search API.
- **Pushbullet Access:** Configured in the Pushbullet node to send push notifications.

### Environment Setup
- Replace the placeholder `"YOUR_POSITIONSTACK_API_KEY"` with your actual Positionstack API key in the **Geocode Location** node.
- Replace `"YOUR_EVENTBRITE_API_KEY"` with your Eventbrite API token in the **Search Events** node.
- Configure email sending with appropriate SMTP credentials or service in the **Send Email Notification** node.
- Set up Pushbullet access tokens or credentials for the **Send Reminder Notification** node.

---

## Workflow Execution Flow

1. The workflow starts with the **Start - Hardcoded Input** node providing user data.
2. Extracts user's location and interests.
3. Converts the location to latitude and longitude.
4. Uses those coordinates and interests to search for upcoming relevant events on Eventbrite.
5. Parses and simplifies the event data.
6. Filters local community resources based on user interests.
7. Prepares a combined notification message including events and resources.
8. Sends the notification email to the user.
9. Sets up a daily schedule trigger at 9 AM.
10. At scheduled time, sends a push notification reminding the user to check their email.

---

## Notes

- The community resources data is mocked and can be extended or replaced with a real data source.
- This workflow assumes the user’s email, location, and interests are available at the start; in production, user input can be collected via forms or other input methods.
- Timezone considerations should be handled when managing dates and notifications.

---

## Licensing

This project is provided as-is for educational and integration purposes. API keys and external services usage are subject to their respective terms and conditions.

---

Thank you for using the **AI-Powered Local Community Resource Finder and Event Notifier** workflow!