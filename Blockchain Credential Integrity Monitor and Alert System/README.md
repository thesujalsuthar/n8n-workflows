# AI-Powered Blockchain Credential Verification and Instant Revocation Notification System

## Overview

This workflow automates the process of verifying blockchain-based credentials, detecting status changes like revocation or updates, and instantly notifying the relevant parties via multiple communication channels including Email, SMS, and Slack.

---

## Workflow Breakdown

### 1. Periodic Trigger

- **Type:** Interval Trigger
- **Interval:** Every 300 seconds (5 minutes)
- **Purpose:** Periodically initiates the entire verification and notification process.

---

### 2. Generate Credential List

- **Type:** Function Node
- **Function:** Simulates fetching a list of credentials to verify.
- **Output:** An array of objects, each containing a `credentialId`.
- **Note:** In production, this could be replaced by a call to a database or an external API to fetch live credentials.

---

### 3. Blockchain Credential Status Check

- **Type:** Function Node
- **Function:** Checks the blockchain to determine the current status of each credential.
- **Input:** `credentialId`
- **Output:** An object containing:
  - `status`: One of `"active"`, `"revoked"`, or `"updated"`.
  - `details`: Metadata about the credential, including who it was issued to and the issuance date.
- **Implementation Detail:** Currently simulates blockchain status check with a randomization mechanism. Replace this placeholder with actual blockchain API calls for real data.

---

### 4. Check Revoked or Updated

- **Type:** If Node (Boolean Condition)
- **Condition:** Evaluates whether the credential status is either `"revoked"` or `"updated"`.
- **Branches:**
  - **True branch:** Triggers notifications.
  - **False branch:** Ends the workflow for that credential (no notifications sent).

---

### 5. Notification Nodes

If a credential is revoked or updated, notifications are sent immediately through:

#### a. Send Email Notification

- **Type:** Email Send Node
- **From:** `no-reply@yourdomain.com`
- **To:** Email address extracted from the credential details (`issuedTo`)
- **Subject:** `"Credential Status Alert: <status>"`
- **Message:** Notifies the recipient about the change in credential status with the credential ID and current status.

#### b. Send SMS Notification

- **Type:** Twilio Node
- **To:** Phone number extracted from the credential details (`issuedTo`)
- **Message:** Alerts about the credential status change.
- **Credentials:** Requires Twilio API credentials configured in `Twilio Account`.

#### c. Send Messaging Platform Notification

- **Type:** Slack Node
- **Channel:** `#credential-alerts`
- **Message:** Public alert that a credential has been revoked or updated and requires immediate attention.
- **Credentials:** Requires Slack Bot token setup under `Slack Bot`.

---

## Connections Flow

1. **Periodic Trigger** activates.
2. Generates the list of credential IDs in **Generate Credential List**.
3. Each credential is checked on the blockchain via **Blockchain Credential Status Check**.
4. Status results are filtered in **Check Revoked or Updated**:
   - If status is `"revoked"` or `"updated"`, sends notifications via:
     - Email
     - SMS
     - Slack
   - If status is `"active"`, no notification is triggered and processing for that credential ends.

---

## Setup Instructions

1. **Configure Credentials:**
   - Set up Twilio API credentials as `Twilio Account` in your environment.
   - Set up Slack Bot token as `Slack Bot` with access to the `#credential-alerts` channel.

2. **Blockchain Integration:**
   - Replace the placeholder code in **Blockchain Credential Status Check** with a real API call to your blockchain or credential verification service.

3. **Email Configuration:**
   - Replace `no-reply@yourdomain.com` with your valid sending email address and configure the email node to use your mail server or service.

4. **Adjust Intervals and Credential Source:**
   - Modify the **Periodic Trigger** interval as needed.
   - Replace the simulated credential list in **Generate Credential List** with a real data source.

---

## Summary

This workflow enables real-time verification and notification of credential state changes on a blockchain, ensuring swift communication to credential holders and relevant teams, enhancing security and compliance management.