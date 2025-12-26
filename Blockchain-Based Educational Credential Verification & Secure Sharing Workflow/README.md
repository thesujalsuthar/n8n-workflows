# Blockchain Educational Credential Verification & Secure Sharing Workflow

## Overview

This workflow facilitates the verification of educational credentials using a blockchain-based system and securely shares the verified credentials via encrypted email. It automates the entire process from credential submission to notifying users and sending encrypted credentials to recipients.

---

## Workflow Nodes and Steps

### 1. Credential Submission  
- **Type:** HTTP Trigger (POST)  
- **Path:** `/submit-credential`  
- **Description:** Receives credential data submitted via HTTP POST requests. The submitted information includes credential details and user identifiers.

### 2. Prepare for Verification  
- **Type:** Function  
- **Description:** Extracts and prepares relevant data from the submitted credential for blockchain verification. This includes:
  - `credentialId`: Unique ID of the credential, if available.
  - `credentialData`: Raw or structured credential data.
  - `userId`: Unique identifier of the user.

### 3. Blockchain Verification  
- **Type:** HTTP Request  
- **Description:** Sends a verification request to the blockchain node API to check the authenticity of the submitted credential.  
- **Request details:**  
  - URL dynamically constructed based on the presence of `credentialId`.  
  - Authorization header with Bearer token (`YOUR_BLOCKCHAIN_API_TOKEN`).  
  - Expects JSON response indicating verification result.

### 4. Check Verification Status  
- **Type:** Function  
- **Description:** Processes the blockchain API response to determine the verification status.  
- **Logic:**
  - Sets status to `"verified"` if verification succeeds.
  - Sets status to `"failed"` if verification fails.
  - Defaults to `"unknown"` if verification result is ambiguous.

### 5. Conditional Routing: If Verified  
- **Type:** If  
- **Condition:** Checks if the status is `"verified"`.  
- **Description:** Routes the workflow based on verification status:
  - If verified, proceeds to notify the user of success and filter for further processing.
  - If not verified, triggers notification of failure to the user.

### 6. Notify User Success  
- **Type:** Email Send (SMTP)  
- **From:** `no-reply@credentialplatform.example`  
- **To:** User’s email (falls back to `userId@example.com`)  
- **Subject:** Credential Verification Success  
- **Content:** Notifies the user that their credential has been successfully verified on the blockchain.

### 7. Notify User Failure  
- **Type:** Email Send (SMTP)  
- **From:** `no-reply@credentialplatform.example`  
- **To:** User’s email (falls back to `userId@example.com`)  
- **Subject:** Credential Verification Failed  
- **Content:** Alerts the user that the credential verification failed and suggests verifying the submitted information.

### 8. Filter Verified Credentials  
- **Type:** Filter  
- **Condition:** Passes only credentials with status `"verified"` for the next step.  
- **Description:** Filters out any credential that did not pass verification to avoid unnecessary processing.

### 9. Encrypt Credential  
- **Type:** Function  
- **Description:** Encrypts the verified credential data to secure it before sharing.  
- **Encryption Details:**
  - Uses AES-256-CBC symmetric encryption with a secret key from environment variable `ENCRYPTION_KEY`.
  - Generates a random initialization vector (IV) for each encryption.
  - Concatenates IV and encrypted data as a hex string for transmission.

### 10. Send Secure Credential  
- **Type:** Email Send (SMTP)  
- **From:** `no-reply@credentialplatform.example`  
- **To:** Recipient email (falls back to `employer@company.com`)  
- **Subject:** Verified Educational Credential  
- **Content:**  
  - Provides the encrypted credential in the email body.  
  - Includes instructions for decryption using the appropriate key.

---

## Environment Variables

- `ENCRYPTION_KEY`: Secret key used for AES-256-CBC encryption of credential data. Must be exactly 32 bytes for AES-256.

---

## Important Notes

- Replace `"YOUR_BLOCKCHAIN_API_TOKEN"` with your actual API token for authentication with the blockchain node.
- Ensure SMTP email settings are correctly configured for sending emails.
- The recipients of the secure credentials must have the means to decrypt the received encrypted data.
- This workflow assumes the blockchain verification API response includes a boolean `verified` field.
- Error handling in the workflow assumes that unsuccessful verification leads to user failure notification.

---

## How to Use

1. Deploy this workflow in your n8n instance.
2. Configure environment variables and API keys accordingly.
3. Submit credential data to the `/submit-credential` endpoint via HTTP POST.
4. The workflow will automatically:
   - Verify the credential on the blockchain.
   - Notify the user of the status.
   - Encrypt and securely share the verified credential via email.

---

## Summary

This workflow streamlines the verification and secure dissemination of educational credentials leveraging blockchain for trust and cryptographic encryption for confidentiality, enhancing the integrity and privacy of credential sharing processes.