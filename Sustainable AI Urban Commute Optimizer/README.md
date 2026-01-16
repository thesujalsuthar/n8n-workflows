# AI-Powered Sustainable Urban Mobility Planner

## Overview

This workflow is designed to optimize urban commuting routes by leveraging AI to analyze traffic data, public transit schedules, and user preferences. It focuses on sustainability by prioritizing minimal environmental impact in route suggestions. The final personalized routes are delivered to users via email and messaging platforms.

---

## Workflow Nodes and Description

### 1. Check Existing Workflows
- **Type:** Workflow
- **Operation:** Retrieves all existing workflows.
- **Purpose:** To verify if the workflow named "AI-Powered Sustainable Urban Mobility Planner" already exists before creating or running it.

### 2. Validate Workflow Uniqueness
- **Type:** Function
- **Functionality:** Checks if a workflow with the same name already exists in the system.
- **Behavior:** Throws an error and stops execution if the workflow name is found to be duplicated.

### 3. Fetch Traffic Data
- **Type:** HTTP Request
- **Endpoint:** `https://api.citytrafficdata.example.com/v1/traffic`
- **Purpose:** Retrieves real-time traffic data from the city’s traffic data API.

### 4. Fetch Transit Schedules
- **Type:** HTTP Request
- **Endpoint:** `https://api.publictransit.example.com/v1/schedules`
- **Purpose:** Retrieves public transit schedules from the transit system API.

### 5. Combine Inputs
- **Type:** Function
- **Input Data:** Traffic data, transit schedules, and user preferences.
- **Purpose:** Aggregates these three inputs into a single JSON object for further processing.

### 6. Prepare AI Request
- **Type:** Function
- **Functionality:** Formats the combined data into a request structure suitable for the AI endpoint.
- **Additional Info:** Includes instructions for the AI to focus on minimal environmental impact and deliver personalized route suggestions.

### 7. AI Route Optimization
- **Type:** HTTP Request
- **Endpoint:** `https://api.openai.example.com/v1/chat/completions`
- **Method:** POST
- **Headers:**
  - Authorization: Uses an API key stored in credentials (`{{ $credentials.openAIApiKey.apiKey }}`)
  - Content-Type: application/json
- **Payload:**
  - Model: GPT-4
  - Messages: Sends system instructions and user data for AI-based route optimization focusing on sustainability.
- **Purpose:** Performs AI-driven analysis to generate optimized sustainable commuting routes.

### 8. Parse AI Response
- **Type:** Function
- **Functionality:** Extracts and parses the AI's route suggestions from the response into JSON format.

### 9. Send Email
- **Type:** Email Send
- **Recipient:** User email extracted from user preferences.
- **Subject:** "Your Sustainable Commuting Route Suggestions"
- **Content:** Personalized routes with descriptions and estimated times are included in the email body.

### 10. Send Message
- **Type:** HTTP Request
- **Endpoint:** `https://api.messagingplatform.example.com/sendMessage`
- **Method:** POST
- **Payload:** Sends a message containing sustainable route suggestions to the user on a messaging platform using their messaging user ID from preferences.
- **Authentication:** None (assumed handled externally or not required)

### 11. Deliver Routes
- **Type:** Parallel Processing / Split In Batches
- **Purpose:** Ensures simultaneous delivery of route suggestions via both email and messaging.

---

## Workflow Connections

- The workflow starts by checking existing workflows and validating that no duplication occurs.
- It fetches traffic data and transit schedules in parallel.
- Data from the traffic and transit nodes along with user preferences is combined.
- The combined data is prepared and sent to the AI for route optimization.
- The AI-generated routes are parsed and then delivered via email and messaging concurrently.

---

## User Preferences Format

User preferences are expected to include fields such as:
- `email`: The user's email address for route delivery.
- `messagingUserId`: The user ID on the messaging platform for message delivery.
- Additional preference details may be included for custom AI optimization.

---

## Prerequisites

- API keys and credentials configured for:
  - OpenAI API (`openAIApiKey`)
  - Email service (configured inside the Email Send node)
- Access to relevant traffic and transit APIs.
- Messaging platform endpoint and any necessary authentication if required.

---

## Usage

1. Configure API keys in the credentials for OpenAI and email services.
2. Ensure user preferences are properly passed to the workflow with valid contact information.
3. Execute the workflow to obtain personalized sustainable commuting routes.
4. Routes get sent automatically via email and messaging to the user.

---

## Notes

- The AI is prompted specifically to optimize routes with minimal environmental impact.
- Traffic and transit data are fetched in real-time to ensure up-to-date route suggestions.
- The system can be adapted to other cities or data sources by updating API endpoints accordingly.

---

# License

This workflow and its components are provided as-is without any warranties. Usage and customization are at the user's discretion.