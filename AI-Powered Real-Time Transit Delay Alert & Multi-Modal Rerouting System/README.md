# Real-Time AI Transit Delay Notification and Multi-Modal Rerouting Assistant

This workflow provides real-time transit delay notifications combined with AI-driven multi-modal rerouting suggestions tailored to individual user preferences. It fetches live transit delay data, analyzes the impact on user routes, generates alternate travel options using various transportation modes, composes a detailed notification message, and sends this via email.

---

## Workflow Overview

1. **Set User Preferences**  
   Defines user-specific transit preferences such as preferred modes of transport (bus, train, bike, walk) and maximum distances for walking and biking.

2. **Fetch Real-Time Transit Delay Data**  
   Retrieves live transit delay information from a public API endpoint:  
   `https://api.publictransitdata.com/v1/delays`  
   The data is expected in JSON format.

3. **Process Delay Data**  
   Processes the real-time data to:  
   - Identify routes currently affected by delays (`isDelayed === true`).  
   - Combine the affected routes with the user's preferences for further analysis.

4. **Generate Multi-Modal Rerouting Suggestions**  
   Using the affected routes and user preferences, this function simulates multi-modal route alternatives, including walking, biking, bus, and train options with estimated travel times and descriptions. This is a mocked logic serving as a placeholder for integration with a real AI or routing API.

5. **Compose Notification Message**  
   Crafts a user-friendly notification based on the presence or absence of delays, embedding the rerouting suggestions in a readable format.

6. **Send Notification Email**  
   Sends an email to the user (default email: `user@example.com`, customizable via incoming data) containing the alert message and the multi-modal rerouting recommendations.

---

## Node Details

### 1. Set User Preferences

- **Type:** Function  
- **Purpose:** Sets preferences such as:
  - `preferredModes`: Allowed transportation modes (`bus`, `train`, `bike`, `walk`)  
  - `maxWalkDistanceKm`: Maximum walking distance (1 km)  
  - `maxBikeDistanceKm`: Maximum biking distance (5 km)

### 2. Fetch Real-Time Transit Delay Data

- **Type:** HTTP Request  
- **Configuration:**
  - URL: `https://api.publictransitdata.com/v1/delays`  
  - Authentication: None  
  - Response Format: JSON

### 3. Process Delay Data

- **Type:** Function  
- **Logic:**  
  - Filters delay entries where `isDelayed` is true  
  - Prepares object containing `affectedRoutes` and `userPrefs` for further use

### 4. Generate Multi-Modal Rerouting Suggestions

- **Type:** Function  
- **Logic:**  
  - If delays exist, generates mock rerouting options:
    - Walking (e.g., walk 1km to next bus station)  
    - Biking (if within preferred distance)  
    - Taking an alternate bus route  
  - If no delays, suggests that the train route is on time

### 5. Compose Notification Message

- **Type:** Function  
- **Logic:**  
  - Forms alert text depending on delay presence  
  - Concatenates rerouting suggestions into a readable list

### 6. Send Notification Email

- **Type:** Email Send  
- **Settings:**
  - **To:** User's email from input or defaults to `user@example.com`  
  - **Subject:** "Real-Time Transit Delay Alert & Rerouting Suggestions"  
  - **Message:** Layout merging the notification text and each reroute suggestion including mode, estimated time, and details

---

## Workflow Connections

- `Set User Preferences` → `Fetch Real-Time Transit Delay Data`  
- `Fetch Real-Time Transit Delay Data` → `Process Delay Data`  
- `Process Delay Data` → `Generate Multi-Modal Rerouting Suggestions`  
- `Generate Multi-Modal Rerouting Suggestions` → `Compose Notification Message`  
- `Compose Notification Message` → `Send Notification Email`

---

## Customization and Integration

- **User Preferences:** Modify `Set User Preferences` node to capture or process different user requirements.  
- **Transit Data API:** Replace the URL in `Fetch Real-Time Transit Delay Data` with your own transit delay data source if available.  
- **Rerouting Suggestions:** Integrate an AI or routing API in the rerouting suggestions node for dynamic and accurate route alternatives.  
- **Email Node:** Configure SMTP credentials and customize email templates for branding and formatting.

---

## Usage Notes

- This workflow assumes JSON structured delay data with an `isDelayed` field per route object.  
- Current routing suggestions are mocked and should be replaced with live API calls for production use.  
- Ensure the email sending node is properly configured with valid SMTP credentials.

---

**End of documentation**