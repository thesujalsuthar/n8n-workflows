# AI-Powered Blockchain Credential Verification with MFA and Real-Time Revocation Alerts

## Overview

This workflow enables secure and efficient verification of blockchain-based credentials enhanced with Multi-Factor Authentication (MFA) and real-time revocation alerting across multiple communication channels.

---

## Workflow Steps

### 1. Webhook Trigger
- Listens for incoming HTTP `POST` requests at the endpoint `/verify-credential`.
- Initiates the verification process by capturing credential data such as `credentialId` and `userId`.

### 2. Blockchain Verification
- Sends a request to the blockchain node API to verify the validity of the credential using the `credentialId`.
- Uses Basic Authentication credentials configured as `Blockchain Node Basic Auth`.
- Receives verification status in the response.

### 3. Request MFA Token
- Sends a `POST` request to the MFA provider API to generate an MFA token for the given `userId`.
- The API endpoint is `https://mfa-provider.example.com/generate`.
- The request body contains JSON `{ "userId": <userId> }`.

### 4. Validate MFA Status
- Checks the MFA verification status from the response.
- Only proceeds if `mfaStatus` equals `"verified"`; otherwise, the workflow terminates with failure.

### 5. Validate Blockchain Credential
- Evaluates the blockchain verification response.
- Proceeds only if the credential `status` is `"valid"`.
- If invalid, the workflow ends with a failure response.

### 6. Check Revocation Status
- Queries the revocation status API (with authenticated access via `Revocation Status API Auth` credentials).
- Retrieves information to determine whether the credential has been revoked.

### 7. Check If Revoked
- Inspects the revocation response.
- If the credential is revoked (`revoked === true`), triggers alerting.
- If not revoked, no action for revocation alerts is performed.

### 8. Send Revocation Alerts
- Dispatches alerts through multiple communication channels: **Email**, **SMS**, and **Slack**.
- Notification includes a subject line: `"Credential Revocation Alert"`.
- Message body:  
  `"Credential with ID <credentialId> has been revoked in the blockchain system. Immediate action is recommended."`
- Sends alerts to the user's email and phone number, and posts the alert to the Slack channel `#security-alerts`.

### 9. Success Response
- Returns a final success message:  
  `"Credential Verified and MFA Completed"`

### 10. Failure Response
- Returns an error message indicating verification or MFA failure:  
  `"Verification or MFA failed"`

---

## Credentials Required

- **Blockchain Node Basic Auth**: Basic Auth credentials for accessing the blockchain node verification API.
- **Revocation Status API Auth**: Basic Auth credentials for the revocation status API.

---

## Endpoints

- **Webhook Endpoint:** `/verify-credential` (HTTP POST)
- **Blockchain Verification API Template:**  
  `https://blockchain-node.example.com/verify/{credentialId}`
- **MFA Provider API:**  
  `https://mfa-provider.example.com/generate` (HTTP POST)
- **Revocation Status API:**  
  Configured with authenticated access (exact URL managed internally)

---

## Alerting Channels

- **Email:** Sent to the user's registered email (`userEmail`).
- **SMS:** Sent to the user's registered phone (`userPhone`).
- **Slack:** Posted to `#security-alerts` channel.

---

## Summary

This workflow orchestrates a secure multi-step process for credential verification on a blockchain, enhanced by MFA. It proactively monitors revocation status and issues timely alerts to stakeholders, strengthening security and compliance management.