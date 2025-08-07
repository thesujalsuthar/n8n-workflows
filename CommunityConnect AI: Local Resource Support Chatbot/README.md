# AI-Powered Local Community Support Chatbot and Resource Matcher

## Overview  
This workflow implements an AI-powered chatbot designed to support local community members by understanding their needs related to community resources (food banks, shelters, health clinics, social services, etc.) and providing matched relevant resources based on their location. It leverages OpenAI GPT-4 for natural language processing and multiple APIs to fetch local resource information, delivering helpful, user-friendly responses.

---

## Workflow Breakdown

### 1. Trigger (Webhook)
- **Node:** `Trigger`  
- **Purpose:** Listens for incoming chat messages on the `/chat` path with the `message` event.  
- **Function:** Initiates the workflow whenever a user sends a message.

---

### 2. Initialize Session and Message  
- **Node:** `Initialize Session and Message` (Function)  
- **Function:** Extracts the `sessionId` and user message (`message`) from incoming webhook data for further processing.

---

### 3. NLP - Extract Needs and Location  
- **Node:** `NLP - Extract Needs and Location` (OpenAI GPT-4)  
- **Function:**  
  - Analyzes user message to identify:
    - User needs (categories like food assistance, shelter, health care, social services)  
    - Location information (city, zip code, or other location descriptors)  
  - Outputs a JSON string strictly formatted as:
    ```json
    {
      "needs": ["need1", "need2"],
      "location": "location string"
    }
    ```

---

### 4. Parse AI Needs and Location  
- **Node:** `Parse AI Needs and Location` (Function)  
- **Function:** Parses the JSON string output from the AI node into usable JSON format containing `needs` and `location` arrays.

---

### 5. Prepare Resource API Calls  
- **Node:** `Prepare Resource API Calls` (Function)  
- **Function:**  
  - Creates a list of API endpoint calls relevant to the extracted user needs and location.  
  - Example endpoints:
    - Food Banks: `https://api.localresources.org/foodbanks?location=...`  
    - Shelters: `https://api.localresources.org/shelters?location=...`  
    - Health Clinics: `https://api.localresources.org/healthclinics?location=...`  
    - Social Services: `https://api.localresources.org/socialservices?location=...`  
  - Each call is formatted with a type and URL.

---

### 6. Fetch Local Resources  
- **Node:** `Fetch Local Resources` (HTTP Request)  
- **Function:** Sends HTTP requests to the URLs prepared for each resource type to retrieve local resource data in JSON format.  
- **Note:** Authentication can be set up here with HTTP Basic Auth credentials if required.

---

### 7. Aggregate Resources  
- **Node:** `Aggregate Resources` (Function)  
- **Function:** Combines responses from all resource API requests into a single structured array grouping resources by type.

---

### 8. Create User Response Message  
- **Node:** `Create User Response Message` (OpenAI GPT-4)  
- **Function:**  
  - Takes aggregated resources, user needs, and location as input.  
  - Generates a clear, concise, and friendly summary message listing recommended local resources categorized by type.  
  - Incorporates resource descriptions and contact information where available.  
  - Outputs the final response text for the user.

---

### 9. Extract Final Response Text  
- **Node:** `Extract Final Response Text` (Function)  
- **Function:** Extracts the generated user response message text from the AI node's output.

---

### 10. Send Chatbot Reply  
- **Node:** `Send Chatbot Reply` (Custom API)  
- **Function:** Sends the final compiled chatbot response back to the user, addressing the session identified by `sessionId`.

---

## Summary  
This workflow orchestrates a seamless experience where users can chat naturally about their community-related needs and receive AI-curated help by locating local resources tailored to their specified location and requirements. The workflow utilizes advanced NLP for intent and location extraction, dynamic API queries to real community data sources, and AI-generated personalized responses for improved engagement and impact.