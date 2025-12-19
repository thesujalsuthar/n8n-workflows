# Quantum Computing Research Collaboration and Innovation Tracker

This workflow automates the process of tracking the latest quantum computing research papers, identifying collaboration opportunities, detecting emerging trends, storing relevant data efficiently, and sending timely email notifications to interested researchers.

---

## Workflow Overview

1. **Fetch Quantum Computing Papers**  
   Retrieves the latest publications related to quantum computing from the [arXiv Quant-Ph RSS feed](https://arxiv.org/rss/quant-ph).

2. **Store Research Papers**  
   Saves the fetched papers into a MongoDB collection named `researchPapers`. Uses an upsert operation to avoid duplicates based on the paper `id`.

3. **Filter Collaboration Opportunities**  
   Filters papers whose titles include the term "collaboration" to identify joint research opportunities.

4. **Store Collaboration Opportunities**  
   Stores these collaboration-related papers into a separate MongoDB collection called `collaborations`, using the paper link as a unique key to prevent duplicates.

5. **Get Stored Papers**  
   Retrieves all stored research papers from the `researchPapers` collection for trend analysis.

6. **Detect Emerging Trends**  
   Analyzes stored papers to detect the presence of predefined quantum computing trending keywords such as:
   - quantum machine learning  
   - error correction  
   - quantum algorithms  
   - quantum supremacy  
   - entanglement  
   - quantum cryptography  

7. **Prepare Notification Message**  
   Prepares a summary message listing the emerging trends detected, or a default message if no significant trends are found.

8. **Set Notification Recipients**  
   Specifies the email recipients for notifications, defaulting to `researchers@quantumlab.org`.

9. **Send Email Notification**  
   Sends an email with the latest emerging trends and collaboration opportunities to the specified recipients using SMTP credentials.

---

## Nodes Description

| Node Name                     | Type                     | Function                                                                 |
|-------------------------------|--------------------------|--------------------------------------------------------------------------|
| Fetch Quantum Computing Papers | RSS Feed Read            | Pulls all recent quantum physics papers from arXiv's RSS feed.          |
| Store Research Papers          | MongoDB                  | Upserts papers into `researchPapers` collection using the paper `id`.    |
| Filter Collaboration Opportunities | Function            | Filters papers containing "collaboration" in their titles.               |
| Store Collaboration Opportunities | MongoDB               | Upserts filtered papers into `collaborations` collection by `link`.     |
| Get Stored Papers             | MongoDB                  | Retrieves all research papers from the database for analysis.           |
| Detect Emerging Trends        | Function                 | Identifies trending quantum computing topics based on keywords.         |
| Prepare Notification Message  | Set                      | Formats the notification text with detected trends list.                |
| Set Notification Recipients   | Function                 | Sets email recipient(s) for notifications.                              |
| Send Email Notification       | Email Send               | Sends out the notification email with the trends summary.               |

---

## Prerequisites

- **MongoDB database** with collections named `researchPapers` and `collaborations`.
- **SMTP account credentials** for sending email notifications.
- n8n automation tool configured with:
  - MongoDB credentials.
  - SMTP credentials.

---

## Configuration

1. **MongoDB Credentials**  
   Set up your MongoDB credential in n8n and replace `"Your MongoDB Credential"` in the respective MongoDB nodes.

2. **SMTP Credentials**  
   Configure your SMTP credentials in n8n and assign them to the "Send Email Notification" node.

3. **Email Recipients**  
   Modify the `Set Notification Recipients` node function code if you want to change or add email recipients.

---

## Execution Flow

1. The workflow fetches recent quantum computing papers from arXiv.
2. Papers are stored/upserted into MongoDB.
3. Collaboration-related papers are filtered and stored separately.
4. All stored papers are fetched to analyze current quantum computing trends.
5. An email message summarizing emerging trends is generated.
6. Recipients are defined, and the notification is sent via email.

---

## Result

- Up-to-date database of quantum computing research papers.
- Separate database of collaboration opportunities.
- Automated email updates highlighting emerging research trends and collaboration chances for quantum computing researchers.

---

## Notes

- The workflow currently looks for the keyword **"collaboration"** in paper titles to identify collaboration opportunities — this can be customized in the `Filter Collaboration Opportunities` node.
- Trending topics are matched from a predefined list of keywords and can be updated in the `Detect Emerging Trends` node.
- Emails default to `researchers@quantumlab.org` unless changed in the recipient node.

---

Thank you for using the Quantum Computing Research Collaboration and Innovation Tracker!