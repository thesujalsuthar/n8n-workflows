# Crypto Regulatory Updates Workflow

This workflow automates the process of fetching, detecting, and alerting on changes in cryptocurrency regulations from an external regulatory API. It ensures your compliance team stays up-to-date with the latest regulatory developments by sending alerts via message queue and email.

---

## Workflow Overview

### Nodes Description

1. **Start**
   - Entry point of the workflow.

2. **Set Previous Data**
   - Reads the previously saved regulatory data from a local JSON file (`data/previousRegulations.json`).
   - Creates the file if it does not exist, initializing with empty data.

3. **Fetch Regulatory Updates**
   - Sends an authenticated HTTP GET request to the regulatory API endpoint:  
     `https://regulatory-api.example.com/v1/crypto-regulations/changes`
   - Retrieves the latest regulation data in JSON format.

4. **Detect Changes**
   - Compares the new regulatory data fetched against the previously saved data.
   - Identifies **new** regulations or **updated** regulations by comparing regulation IDs and content.
   - Outputs an array of changes, each annotated with a `changeType` attribute (`New Regulation` or `Updated Regulation`).

5. **Filter Changes**
   - Filters out any entries that do not have a defined `changeType`.
   - Ensures only actual regulation changes pass through.

6. **Send Alerts to Queue**
   - For every detected change, sends a message containing the regulation change details serialized as JSON to a RabbitMQ queue named `compliance-alerts`.
   - Requires RabbitMQ credentials for connection.

7. **Send Email Alert**
   - Sends an email alert to the compliance team for each detected regulatory change.
   - Email includes:
     - Subject: `Crypto Regulation Change Alert: {changeType}`
     - Body detailing:
       - Regulation ID
       - Region
       - Description
       - Effective Date
   - Uses Gmail credentials for sending emails.

8. **Save Current Data**
   - Writes the current regulatory data fetched from the API back to `data/previousRegulations.json`.
   - This ensures the workflow has updated data for comparison next time it runs.

---

## Detailed Workflow Execution

1. The workflow starts and reads the last stored regulation data from `data/previousRegulations.json` (or initializes an empty file if none exists).

2. It fetches the latest regulatory updates from the external API using HTTP Basic Authentication.

3. The new data is compared with the previously read data:
   - Regulations not present in the previous data are marked as **New Regulation**.
   - Regulations present but with different details are marked as **Updated Regulation**.

4. Changes without a valid `changeType` are filtered out.

5. For each valid change, two parallel alerting actions occur:
   - A message containing the regulation change details is published to the `compliance-alerts` RabbitMQ queue.
   - An email notification is sent to `compliance-team@example.com`.

6. Finally, the workflow saves the latest API data to the local JSON file to serve as the reference point for future runs.

---

## Prerequisites

- **API Credentials:** HTTP Basic Auth credentials configured in n8n with ID `1`.
- **RabbitMQ Credentials:** RabbitMQ connection configured with ID `2`.
- **Google API Credentials:** Gmail API credentials configured with ID `3`.
- Ensure the directory `data/` exists for storing the JSON file, or allow the workflow to create it.

---

## Configuration Details

### Fetch Regulatory Updates (HTTP Request)
- **Method:** GET
- **URL:** `https://regulatory-api.example.com/v1/crypto-regulations/changes`
- **Authentication:** HTTP Basic Auth
- **Response Format:** JSON

### Detect Changes (Function Node)
- Compares old and new regulations by their `id`.
- Outputs list of new or updated regulations with a `changeType`.

### Send Alerts to Queue (RabbitMQ Node)
- **Queue Name:** `compliance-alerts`
- **Message:** JSON string of the change object.

### Send Email Alert (Gmail Node)
- **Recipient:** `compliance-team@example.com`
- **Subject:** `Crypto Regulation Change Alert: {changeType}`
- **Body:** Includes regulation details for review.

### File Handling
- Reads and writes regulation data JSON file at `data/previousRegulations.json`.
- Creates file if not present on first run.

---

## Usage

- Trigger this workflow manually or schedule it to run periodically to continuously monitor regulation changes.
- Ensure all credentials are correctly set up in n8n before activating the workflow.
- Monitor RabbitMQ queue and email inbox to process alerts and take appropriate compliance measures.

---

## Summary

This workflow enables efficient monitoring and alerting for cryptocurrency regulatory changes by integrating API data fetching, change detection, message queue alerting, and direct email notifications, all coordinated within n8n's automation environment.