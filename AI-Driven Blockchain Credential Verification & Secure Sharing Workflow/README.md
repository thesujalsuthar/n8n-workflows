# AI-Powered Blockchain Educational Credential Verification & Secure Sharing

This workflow automates the process of receiving, verifying, storing, and securely sharing educational credentials using AI-powered validation and blockchain technology.

---

## Workflow Overview

1. **Receive Credential Submission**  
   Accepts POST requests containing educational credentials submitted by external systems or users.

2. **Extract Credential Data**  
   Extracts the credential data from the HTTP request payload for processing.

3. **AI Credential Verification**  
   Simulates AI-powered verification by checking required fields in the credential data.  
   - Verifies presence of `studentId`, `courseId`, and `issueDate`.  
   - Generates verification details including status, timestamp, and notes.

4. **Prepare Data for Blockchain**  
   Prepares verified credential data for blockchain storage, including essential fields such as student ID, course ID, issue date, issuer, and verification timestamp.  
   - If verification fails, an error object is generated.

5. **Store Credential on Blockchain**  
   Sends the prepared data to a blockchain node API via a secure authenticated HTTP POST request for immutable storage.

6. **Check Blockchain Storage Result**  
   Confirms whether the blockchain storage was successful by checking for a transaction ID in the response.

7. **Is Verified?**  
   Determines if the credential storage was successful by validating the status and routes workflow accordingly.

8. **Prepare Secure Sharing Package**  
   Creates a minimal, secure sharing package containing:  
   - Authorized party email  
   - Credential identifier (combination of student and course IDs)  
   - Verification timestamp  
   - Blockchain transaction ID  
   *(Note: Encryption or advanced packaging can be added for real-world usage.)*

9. **Send Secure Sharing Email**  
   Sends an email to the authorized party with information about the verified credential and instructions to access it securely.

10. **Prepare Alert on Failure**  
    Prepares an alert payload containing details of verification or storage failure.

11. **Send Alert Email**  
    Sends an alert email to administrators notifying them of the failure for immediate investigation.

---

## Detailed Node Breakdown

### 1. Receive Credential Submission  
- **Type:** HTTP Request (POST)  
- **Endpoint:** `/submit-credential`  
- Accepts credential data in JSON format.

### 2. Extract Credential Data  
- Extracts the `credentialsData` from the incoming JSON payload.

### 3. AI Credential Verification  
- Checks credential structure for required fields: `studentId`, `courseId`, `issueDate`.  
- Sets verification status accordingly (`verified` or `failed`).  
- Records verification timestamp and notes.

### 4. Prepare Data for Blockchain  
- On successful verification, prepares minimal dataset for blockchain.  
- Includes `studentId`, `courseId`, `issueDate`, `issuer` (defaulted to 'Unknown Issuer' if missing), and verification timestamp.

### 5. Store Credential on Blockchain  
- HTTP POST request to `https://your-blockchain-node/api/credentials/store`.  
- Uses Bearer token authentication (`YOUR_BLOCKCHAIN_API_TOKEN`).  
- Sends JSON body containing the prepared credential data.  
- Expects JSON response including `transactionId`.

### 6. Check Blockchain Storage Result  
- Checks for the presence of `transactionId` in the response to confirm success.

### 7. Is Verified?  
- Routes the workflow based on storage success status.

### 8. Prepare Secure Sharing Package  
- Creates a package for secure sharing sent to a predefined authorized party (`university_admin@example.com`).  
- Includes credential ID (concatenated student and course IDs), verification timestamp, and blockchain transaction ID.

### 9. Send Secure Sharing Email  
- Sends email from `no-reply@education-blockchain.com` to authorized party.  
- Email contains summary and instructions for accessing the verified credential.

### 10. Prepare Alert on Failure  
- Creates alert data for failures in verification or blockchain storage.

### 11. Send Alert Email  
- Sends alert email from `alerts@education-blockchain.com` to administrator (`admin@education-blockchain.com`).  
- Provides detailed failure information for prompt action.

---

## Error Handling & Notifications

- If credential verification fails or blockchain storage is unsuccessful, the workflow triggers an alert process.  
- Alerts include detailed diagnostic messages sent to administrators for timely troubleshooting.

---

## Setup & Configuration Notes

- **Blockchain API URL and Token:** Replace `https://your-blockchain-node/api/credentials/store` and `YOUR_BLOCKCHAIN_API_TOKEN` with your actual blockchain node endpoint and authentication token.  
- **Email Addresses:** Update sender and recipient email addresses as required for your organization.  
- **AI Verification Enhancement:**  
  - Currently uses basic field validation; integrate with external AI services if desired for advanced credential validation.  
- **Security:**  
  - Implement encryption for secure sharing package in a production environment.  
  - Secure all API keys and email credentials appropriately.

---

## Usage

- Submit educational credential data via POST request to `/submit-credential` endpoint.  
- Monitor email notifications for both successful verifications or alert messages on failure.  
- Access verified credential information securely via authorized channels referenced in the sharing email.

---

## Credentials Data Sample Format

```json
{
  "studentId": "123456",
  "courseId": "CS101",
  "issueDate": "2024-01-15",
  "issuer": "Example University"
}
```

---

This workflow enables secure, transparent, and tamper-resistant educational credential verification leveraging AI simulation and blockchain technology, coupled with secure sharing and robust failure alerting.