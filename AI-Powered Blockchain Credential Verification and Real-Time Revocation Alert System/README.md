# AI-Driven Blockchain Credential Verification with Multi-Factor Authentication and Revocation Alerts

## Overview

This workflow provides a comprehensive system for verifying digital credentials using AI analysis, blockchain validation, multi-factor authentication (MFA), and real-time revocation alerting. It leverages AI to analyze the authenticity of credentials, confirms on-chain verification details, enforces MFA via SMS, and monitors blockchain events to send immediate revocation notifications.

---

## Workflow Components

### 1. HTTP Request Trigger  
- **Node:** HTTP Request Trigger  
- **Type:** HTTP Trigger (POST)  
- **Endpoint:** `/verify-credential`  
- **Description:** Receives incoming JSON payloads containing credential information for verification.

### 2. AI Analyze Credential Authenticity  
- **Node:** AI Analyze Credential Authenticity  
- **Type:** OpenAI (text2text Transform)  
- **Model:** GPT-4  
- **Function:**  
  - Takes the credential JSON payload from the request.  
  - Evaluates authenticity based on credential format, issuer reputation, signature validity, and consistency.  
  - Outputs a JSON response containing:  
    - `authentic`: `true` or `false`  
    - `reasons`: Array explaining assessment decisions  
    - `score`: Numeric confidence score (0 to 1)

### 3. Blockchain Credential Verification  
- **Node:** Blockchain Credential Verification  
- **Type:** Blockchain node (Ethereum)  
- **Function:** Fetches on-chain transaction data using the blockchain transaction hash from the credential’s verification object to confirm the issuance and authenticity of the credential on Ethereum.

### 4. Generate MFA Code  
- **Node:** Generate MFA Code  
- **Type:** Function  
- **Function:**  
  - Generates a 6-digit random MFA verification code.  
  - Requires `userPhone` field from input payload.  
  - Outputs the verification code and phone number for sending.

### 5. Send MFA SMS  
- **Node:** Send MFA SMS  
- **Type:** Twilio SMS Send  
- **Function:** Sends the MFA code via SMS to the user’s phone number, confirming the second factor of identity verification.

### 6. Verify MFA Code  
- **Node:** Verify MFA Code  
- **Type:** Function  
- **Function:**  
  - Compares the user-provided MFA code (`providedCode`) with the generated code.  
  - Outputs a boolean flag `mfaVerified` to indicate success or failure of MFA verification.

### 7. Watch Blockchain Revocation Logs  
- **Node:** Watch Blockchain Revocation Logs  
- **Type:** Blockchain Trigger (Ethereum)  
- **Function:** Sets a listener on Ethereum blockchain events for `CredentialRevoked` emitted from the credential’s contract address. Enables real-time revocation detection.

### 8. Extract Revocation Info  
- **Node:** Extract Revocation Info  
- **Type:** Function  
- **Function:** Extracts important revocation details such as `credentialId` and `userEmail` from the detected blockchain event data.

### 9. Send Revocation Alert Email  
- **Node:** Send Revocation Alert Email  
- **Type:** Email Send  
- **Function:** Sends an alert email to the user notifying them immediately that their credential has been revoked on the blockchain.

---

## Workflow Flow Summary

1. **Credential Submission:** A POST request with a credential is received at `/verify-credential`.  
2. **AI Analysis:** The credential is analyzed with GPT-4 for authenticity, returning a detailed assessment.  
3. **Blockchain Verification:** The Ethereum blockchain transaction related to the credential is verified for authenticity.  
4. **MFA Process:**  
   - An MFA code is generated and sent via SMS to the user’s phone.  
   - The user submits the MFA code, which is verified to confirm identity.  
5. **Revocation Monitoring:**  
   - Continuously watches the Ethereum contract for `CredentialRevoked` events.  
   - When detected, extracts revocation details and sends a notification email to the user.

---

## Input Payload Example (POST /verify-credential)

```json
{
  "credential": {
    "id": "credential-123",
    "issuer": "Issuer Name",
    "verification": {
      "blockchainTxHash": "0xabc123...",
      "contractAddress": "0xdef456..."
    },
    ...
  },
  "userPhone": "+1234567890",
  "userEmail": "user@example.com",
  "providedCode": "123456"
}
```

---

## Prerequisites

- Valid OpenAI API Key for GPT-4 access.  
- Ethereum blockchain access (via provider or node).  
- Twilio account and credentials configured for SMS sending.  
- Email service credentials configured for sending revocation alerts.  
- Credential contract emitting `CredentialRevoked` events on Ethereum.

---

## Notes

- MFA verification requires inputting the `providedCode` received on the client side.  
- The blockchain watcher runs continuously to provide real-time revocation alerts.  
- The workflow can be extended or customized as per credential schema or alternative blockchain networks.

---

## License

This workflow is provided as-is without warranty. Use responsibly and ensure compliance with data privacy and security standards.