# Blockchain-Based Educational Credential Verification and Sharing System

## Overview
This workflow facilitates the verification and sharing of educational credentials using blockchain technology. It ensures that credentials issued by educational institutions are authentic by verifying them against a blockchain smart contract and then allows secure sharing of these verified credentials via expiring, verifiable links.

---

## Workflow Steps

### 1. Start Workflow
- **Type:** Manual Trigger  
- **Description:** Initiates the workflow manually or at workflow start if configured.

### 2. Input Credential Data
- **Type:** Set Node  
- **Purpose:** Collects and formats credential information including:
  - `studentName`
  - `studentID`
  - `institution`
  - `degree`
  - `fieldOfStudy`
  - `dateAwarded`
  - `credentialHash` (a unique blockchain identifier/hash for the credential)

### 3. Verify Credential On Blockchain
- **Type:** Blockchain Invoke  
- **Operation:** Calls the smart contract function `verifyCredential` on the Ethereum mainnet at the configured contract address (`$env.BLOCKCHAIN_CONTRACT_ADDRESS`).
- **Parameters:**  
  - `credentialHash` — Used as input to verify authenticity.
- **Return:** Boolean indicating whether the credential is verified or not.

### 4. Is Credential Verified?
- **Type:** If Condition  
- **Logic:** Checks if the blockchain verification result is `true`.

### 5. Generate Shareable Verifiable Link
- **Type:** Function  
- **When Triggered:** If credential is verified.  
- **Functionality:**  
  - Generates a unique shareable link for the credential containing a token.
  - Token is a base64-encoded JSON with:
    - `credentialId` (credential hash or timestamp-based fallback)
    - `expiryTime` (7 days from generation)
  - The link is built using the base URL configured in `$env.SHARE_BASE_URL` or a default `https://credentials.example.com/share`.

### 6. Notify Employer/Institution
- **Type:** Email Send  
- **Trigger:** After generating the shareable link for verified credentials.  
- **Recipients:** Uses `employerEmail` or `institutionEmail` from input data.
- **Content:** Sends an email with the generated shareable link and notification that the credential is verified and available for review. The link validity is explicitly stated as 7 days.

---

### Handling Failed Verification

#### 7. Handle Verification Failure
- **Type:** Function  
- **Purpose:** Handles the case when blockchain verification fails, returning an error in the workflow.

#### 8. Notify Admin on Failure
- **Type:** HTTP Request  
- **Purpose:** Sends an administrative alert via HTTP notification to the `admin-alerts` channel containing the failed credential hash for further investigation.

---

## Environment Variables Required
- `BLOCKCHAIN_CONTRACT_ADDRESS` — Ethereum smart contract address responsible for credential verification.
- `SHARE_BASE_URL` — Base URL to generate shareable links (defaults to `https://credentials.example.com/share` if not set).

---

## Summary
This workflow securely verifies educational credentials against a blockchain record and shares them only if authentic, ensuring tamper-proof verification and controlled access through expiring verification links. It also includes error handling with administrative notifications for failed verifications.