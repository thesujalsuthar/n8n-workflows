# Quantum Computing Research Collaboration Workflow

## Overview

This workflow manages the submission, storage, and notification of new quantum computing research projects through a webhook. It integrates with NoCoDB for project data storage and Slack for team notifications. Additionally, it provides an endpoint to retrieve all stored projects.

---

## Workflow Nodes Description

### 1. Receive Project Update

- **Type:** Webhook  
- **Method:** POST  
- **Webhook Path:** `/quantum-research-webhook`  
- **Purpose:**  
  Listens for incoming POST requests that contain new quantum computing project data. This webhook acts as the entry point to the workflow.

---

### 2. Add Project to DB

- **Type:** NoCoDB  
- **Operation:** Create an item  
- **Collection ID:** `quantum_research_projects`  
- **Purpose:**  
  Stores the JSON payload received from the webhook into the `quantum_research_projects` collection in NoCoDB.

- **Credentials Required:**  
  NoCoDB API credentials (configured as `NoCoDB API`).

---

### 3. Format Notification Message

- **Type:** Function Item  
- **Purpose:**  
  Constructs a formatted notification message using the project data fields:
  - Title  
  - Lead Researcher  
  - Summary  

- **Message Template:**  
  ```
  New Quantum Computing Research Project Added:

  Title: {{$json["title"]}}
  Lead Researcher: {{$json["leadResearcher"]}}
  Summary: {{$json["summary"]}}

  Check the project dashboard for more details.
  ```

---

### 4. Send Slack Notification

- **Type:** Slack  
- **Channel:** `#quantum-updates`  
- **Purpose:**  
  Sends the formatted notification message to the designated Slack channel to inform the team about the new project.

- **Credentials Required:**  
  Slack API credentials (configured as `Slack Workspace`).

---

### 5. Fetch All Projects

- **Type:** NoCoDB  
- **Operation:** Get all items  
- **Collection ID:** `quantum_research_projects`  
- **Purpose:**  
  Retrieves all stored quantum computing research projects from the NoCoDB collection.

- **Credentials Required:**  
  NoCoDB API credentials (configured as `NoCoDB API`).

---

### 6. Format Projects for Output

- **Type:** Function  
- **Purpose:**  
  Processes the list of project items fetched to properly format them as an array of JSON objects for output.

---

### 7. Respond with Projects

- **Type:** Respond to Webhook  
- **HTTP Status Code:** 200 OK  
- **Purpose:**  
  Sends the formatted list of all projects back to the requester, completing the retrieval process.

---

## Workflow Connections

- The workflow starts when the webhook (`Receive Project Update`) receives data.
- The project data is added to the NoCoDB database (`Add Project to DB`).
- After saving the project, a notification message is formatted (`Format Notification Message`).
- The notification is then sent to the Slack channel (`Send Slack Notification`).
- Separately, the flow to fetch all projects begins at `Fetch All Projects`.
- The retrieved project data is formatted (`Format Projects for Output`).
- Finally, the formatted list is sent as the response via the webhook (`Respond with Projects`).

---

## Setup Instructions

1. **NoCoDB Setup:**  
   - Create a collection named `quantum_research_projects` in your NoCoDB instance to store project entries.  
   - Configure API credentials in n8n as `NoCoDB API`.

2. **Slack Setup:**  
   - Create or use an existing Slack workspace and channel `#quantum-updates`.  
   - Set up Slack credentials in n8n as `Slack Workspace` with appropriate permissions to post messages.

3. **Webhook Usage:**  
   - Use the webhook endpoint `/quantum-research-webhook` to POST new project submissions in JSON format.  
   - To fetch all projects, invoke the respective fetch endpoint defined by your workflow exposure (adjust as per deployment).

---

## Data Schema Example for Project Submissions

```json
{
  "title": "Quantum Entanglement Algorithms",
  "leadResearcher": "Dr. Alice Smith",
  "summary": "Research focused on developing new algorithms leveraging quantum entanglement for optimization."
}
```

---

## Notes

- Ensure that n8n credentials for NoCoDB and Slack are correctly configured with valid API keys and permissions.
- This workflow runs actively, listening to incoming project updates and handling database and notification tasks automatically.
- You can extend or customize the Slack message format or NoCoDB schema as needed for your collaboration requirements.

---

Thank you for using the Quantum Computing Research Collaboration workflow!