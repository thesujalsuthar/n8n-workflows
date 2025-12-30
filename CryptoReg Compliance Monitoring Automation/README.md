# Cryptocurrency Regulatory Compliance Monitoring Workflow

This workflow automates the process of monitoring regulatory news and announcements from multiple financial authorities, analyzing their impact on a cryptocurrency portfolio, and delivering a consolidated compliance report via email and Slack.

---

## Workflow Overview

The workflow fetches RSS feeds from three regulatory authorities:

- **SEC (U.S. Securities and Exchange Commission)**
- **FCA (Financial Conduct Authority, UK)**
- **MAS (Monetary Authority of Singapore)**

It then parses and combines these feeds, filters news by region and cryptocurrency types, analyzes the impact of the news on a predefined cryptocurrency portfolio, generates a detailed report, and sends notifications via email and Slack.

---

## Nodes Description

### 1. **Trigger Webhook**

- **Type:** Webhook Trigger
- **Purpose:** Serves as the entry point to start the workflow. An external service can trigger the workflow by calling this webhook URL.

### 2. **SEC RSS Feed**

- **Type:** HTTP Request
- **URL:** `https://www.sec.gov/news/press-releases.xml`
- **Purpose:** Fetches the latest press releases and news from the SEC RSS feed in XML format.

### 3. **FCA RSS Feed**

- **Type:** HTTP Request
- **URL:** `https://www.fca.org.uk/news.rss`
- **Purpose:** Fetches the latest news from the FCA RSS feed in XML format.

### 4. **MAS RSS Feed**

- **Type:** HTTP Request
- **URL:** `https://www.mas.gov.sg/rss/announcements.xml`
- **Purpose:** Fetches the latest announcements from the MAS RSS feed in XML format.

### 5. **Parse SEC Feed**

- **Type:** XML Transform
- **Operation:** Parse XML from SEC RSS Feed response.
- **Purpose:** Converts the SEC RSS XML feed data into JSON format for easier manipulation.

### 6. **Parse FCA Feed**

- **Type:** XML Transform
- **Operation:** Parse XML from FCA RSS Feed response.
- **Purpose:** Converts the FCA RSS XML feed data into JSON format.

### 7. **Parse MAS Feed**

- **Type:** XML Transform
- **Operation:** Parse XML from MAS RSS Feed response.
- **Purpose:** Converts the MAS RSS XML feed data into JSON format.

### 8. **Filter and Combine Feeds**

- **Type:** Function
- **Purpose:** 
  - Combines the parsed JSON feed items from SEC, FCA, and MAS.
  - Flattens all received news items into a single array.
  - Filters items based on:
    - **Region:** Optional filter by a specific geographic region keyword.
    - **Cryptocurrency Types:** Optional list to filter news related to specific cryptocurrencies.
  - Returns only news items that match the filter criteria.

### 9. **Analyze Impact on Portfolio**

- **Type:** Function
- **Purpose:** 
  - Accepts a cryptocurrency portfolio (array of assets with `name`).
  - Analyzes each filtered news item by checking if the item’s title or description mentions any portfolio cryptocurrencies.
  - Assigns an **impact score** based on the count of portfolio mentions.
  - Filters out news items with zero impact.
  - Returns only news updates with a positive impact score.

### 10. **Generate Compliance Report**

- **Type:** Function
- **Purpose:**
  - Formats the analyzed news into a textual compliance report.
  - Each news item in the report contains:
    - Title
    - Link
    - Publication Date
    - Impact Score
  - The final report is constructed as a string, ready for delivery.

### 11. **Send Compliance Report Email**

- **Type:** Email Send
- **Purpose:** Sends the generated compliance report via email.
- **Email Subject:** "Cryptocurrency Regulatory Compliance Report"
- **Recipients:** 
  - Determined dynamically from input JSON `email` property.
  - Falls back to the default email from SMTP credentials if none specified.
- **Credentials:** Uses configured SMTP account for sending.

### 12. **Send Slack Alert**

- **Type:** Slack
- **Purpose:** Sends the compliance report as a Slack message to a specified channel.
- **Slack Channel:** 
  - Determined dynamically from input JSON `slackChannel` property.
  - Defaults to `#general` channel if none specified.
- **Credentials:** Uses configured Slack API credentials.

---

## Inputs / Parameters

- **Webhook Trigger Input:**
  - Optional JSON payload can include:
    - `region` (string) — Filter news by a specific geographic region keyword.
    - `cryptoTypes` (array of strings) — List of cryptocurrency names or types to filter news.
    - `portfolio` (array of objects) — List of portfolio crypto assets. Each asset should have a `name` property.
    - `email` (string) — Recipient email address for the compliance report.
    - `slackChannel` (string) — Slack channel name for sending alerts.

---

## How It Works Step-by-Step

1. **Workflow is triggered** by an external HTTP webhook call.
2. **RSS feeds are fetched** from SEC, FCA, and MAS websites.
3. **RSS XML feeds are parsed** into JSON format.
4. **Feeds are merged and filtered** based on the region keyword and cryptocurrency filters.
5. **Impact is analyzed** by comparing news content against the provided cryptocurrency portfolio.
6. **Compliance report is generated** summarizing impactful news.
7. **Report is sent by Email** to the specified recipient.
8. **Report is sent as a Slack alert** to the specified Slack channel.

---

## Setup and Credentials

- **SMTP Email Credentials:** Required for sending emails via "Send Compliance Report Email" node.
- **Slack API Credentials:** Required for sending Slack messages.
- **Configure the Webhook URL** for external triggering.

---

## Customization

- Modify the **region** and **cryptoTypes** filters by passing them as parameters to the webhook JSON input to refine news filtering.
- Update the **portfolio** array with your specific cryptocurrency holdings to get relevant impact analysis.
- Change recipient email and Slack channel dynamically through webhook inputs or by updating the workflow nodes directly.

---

## Notes

- The workflow expects each portfolio asset to have a `name` string used for keyword matching.
- The impact score is a simple count of mentions, which can be enhanced to a more complex scoring mechanism if needed.
- The workflow handles RSS feeds in XML and transforms them to JSON for processing.
- Both email and Slack notifications are optional but recommended for timely alerting.

---

This workflow provides a streamlined automated solution for cryptocurrency compliance monitoring based on regulatory announcements from major financial authorities.