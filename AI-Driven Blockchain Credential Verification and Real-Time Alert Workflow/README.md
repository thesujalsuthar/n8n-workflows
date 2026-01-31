# AI-Powered Blockchain Educational Credential Verification and Real-Time Revocation Alert System

## Overview

This workflow leverages blockchain technology and AI-based verification to ensure the authenticity of educational credentials and provides real-time alerts upon credential revocation or updates. It continuously monitors blockchain events related to credentials and notifies the credential owner via email and Slack if any critical status changes occur.

---

## Workflow Nodes Description

### 1. Watch Blockchain Events  
- **Type:** HTTP Request  
- **Purpose:** Continuously listens for blockchain events related to educational credentials, specifically for `credential.revoked` and `credential.updated` events.  
- **Details:**  
  - Uses a GET request to the provided `credentialsApiUrl`.  
  - Watches for changes in credential status to trigger the verification process.

### 2. Get Credential from Blockchain  
- **Type:** HTTP Request  
- **Purpose:** Fetches detailed credential information from the blockchain for the given `credentialId`.  
- **Parameters:**  
  - `credential_id`: Extracted dynamically from the JSON input.  
- **Details:**  
  - Sends a GET request to `credentialsApiUrl` with the credential ID as a query parameter.

### 3. AI Verification Check  
- **Type:** Function  
- **Purpose:** Performs an AI-simulated verification of the credential authenticity.  
- **Verification Logic:**  
  - Checks if the digital signature is valid (`signatureValid`).  
  - Confirms issuer trustworthiness (`issuerTrusted`).  
  - Compares stored data hash with the calculated hash (`dataHash === calculatedHash`).  
- **Output:**  
  - Returns an object with `isVerified` status and the original credential data.

### 4. Check Revocation or Updates  
- **Type:** Function  
- **Purpose:** Checks if the credential status indicates revocation or any update.  
- **Logic:**  
  - Sets an alert flag (`hasAlert`) if the status is either `revoked` or `updated`.  
  - Extracts the current status, credential owner's email, and credential ID for alerting purposes.

### 5. If Revocation or Update Alert  
- **Type:** If Condition  
- **Purpose:** Determines whether to proceed with notifications based on the `hasAlert` flag.  
- **Routing:**  
  - **True branch:** Triggers notification nodes.  
  - **False branch:** Ends the workflow without notifications.

### 6. Send Email Notification  
- **Type:** Email Send  
- **Purpose:** Sends an email alert to the credential owner notifying them of the credential status change.  
- **Email Details:**  
  - From: `no-reply@educationblockchain.ai`  
  - To: Credential owner’s email extracted from the credential data.  
  - Subject: Includes credential status and ID.  
  - Body: Notifies user of the revocation or update with instructions to verify with the issuing institution.  
- **Credential:** Uses configured SMTP credential for email delivery.

### 7. Send Slack Notification  
- **Type:** Slack  
- **Purpose:** Sends a Slack message alerting the credential owner of the status change.  
- **Message:** Includes credential ID and status with an urgent call to review.  
- **Credential:** Uses configured Slack API credential for sending messages.

---

## Workflow Data Flow

1. **Event Detection:** The workflow starts by watching blockchain events related to credential revocation or updates.
2. **Retrieve Credential:** Upon an event, it fetches corresponding credential data from the blockchain.
3. **AI Verification:** The credential undergoes AI-simulated verification checks for authenticity and integrity.
4. **Status Check:** The system determines if the credential status indicates revocation or update.
5. **Conditional Alert:** If the credential is revoked or updated, notifications are sent.
6. **Notifications:** The user receives both an email and a Slack message to ensure prompt awareness.

---

## Prerequisites

- **Blockchain Credential API:** Accessible API endpoint providing credential details and emitting events.
- **SMTP Server:** Configured SMTP credentials to send email notifications.  
- **Slack API:** Slack workspace and API token configured for sending alerts.  
- **n8n Environment:** Workflow designed for automation using the n8n platform.

---

## Configuration

- Set `credentialsApiUrl` and ensure it points to the blockchain provider’s credential API.  
- Configure SMTP credentials with proper authentication details in n8n.  
- Set up Slack API credentials in n8n to enable message sending.  
- Ensure webhook or polling permissions from the blockchain API to watch events.

---

## Summary

This automated system enhances trust and transparency around educational credentials by verifying authenticity with AI methods and providing real-time status alerts directly to the credential owner. It helps prevent fraud and keeps all stakeholders informed in a timely manner.