# Unified Social Media Engagement Aggregator

This workflow aggregates social media engagement metrics from Facebook, Instagram, and Twitter accounts, analyzes changes compared to previous data, and sends alerts via Slack for significant spikes or drops in engagement. It also updates a centralized data store for dashboard visualization.

---

## Workflow Overview

The workflow fetches posts and media metrics, consolidates them into a uniform format, compares current engagement metrics to historical data, generates alerts for significant changes, sends notifications through Slack, and updates stored data for dashboards.

---

## Nodes Description

### 1. Facebook Posts

- **Type:** HTTP Request  
- **Authentication:** OAuth2 (Facebook API)  
- **Endpoint:** `/me/posts`  
- **Fields Retrieved:**  
  - `id`  
  - `message`  
  - `likes.summary(true)` (total count)  
  - `comments.summary(true)` (total count)  
  - `shares`  
- **Purpose:** Retrieve the current user's Facebook posts and their engagement metrics.

---

### 2. Instagram Media

- **Type:** HTTP Request  
- **Authentication:** OAuth2 (Instagram API)  
- **Endpoint:** `/v1.0/me/media`  
- **Fields Retrieved:**  
  - `id`  
  - `caption`  
  - `like_count`  
  - `comments_count`  
- **Purpose:** Retrieve media items posted by the authenticated Instagram user along with their likes and comments.

---

### 3. Twitter Metrics

- **Type:** Twitter Node  
- **Authentication:** Twitter API  
- **Operation:** Get Tweet statistics  
- **Parameters:**  
  - Tweet ID (dynamically extracted)  
  - `tweet.fields`: `public_metrics` (includes like, reply, retweet counts)  
- **Purpose:** Obtain engagement metrics of tweets from the authenticated Twitter account.

---

### 4. Merge Engagement Data

- **Type:** Merge  
- **Mode:** Merge by Index  
- **Purpose:** Combine outputs from Facebook, Instagram, and Twitter data fetch nodes into a single collection.

---

### 5. Consolidate Engagement Metrics

- **Type:** Function  
- **Purpose:** Normalize and unify engagement data into a consistent format with the following structure per item:  
  ```json
  {
    "platform": "Facebook|Instagram|Twitter",
    "id": "<post/media/tweet id>",
    "message|caption|text": "<content text>",
    "likes": <number>,
    "comments": <number>,
    "shares": <number> (0 for Instagram)
  }
  ```  
- **Details:**  
  - Facebook likes, comments, and shares extracted from summaries.  
  - Instagram likes and comments directly from media data (shares default to 0).  
  - Twitter public metrics mapped to likes, comments (replies), and shares (retweets).

---

### 6. Load Previous Totals

- **Type:** Function  
- **Purpose:** Load previously stored engagement totals from global workflow static data to compare against current totals.

---

### 7. Analyze Engagement Changes

- **Type:** Function  
- **Purpose:** Compare current aggregated metrics per platform with previous totals.  
- **Logic:**  
  - Sum `likes`, `comments`, and `shares` per platform from the consolidated data.  
  - Detect significant changes using a ±30% threshold for spikes and drops.  
  - Generate alert messages describing increases or decreases in engagement metrics.

---

### 8. Send Alerts

- **Type:** Slack  
- **Authentication:** Slack API  
- **Operation:** Send incoming messages to Slack channel  
- **Message:**  
  - Lists spike and drop alerts separated by new lines.  
  - If none detected, sends “No significant engagement changes detected.”  
- **Purpose:** Notify stakeholders of meaningful changes in social media engagement.

---

### 9. Save Current Totals

- **Type:** Function  
- **Purpose:** Store current aggregated engagement totals in global workflow static data for future comparisons and output dashboard data.

---

### 10. Update Dashboard Data Store

- **Type:** Data Store (n8n)  
- **Operation:** Write  
- **Key:** `dashboardData`  
- **Value:** Latest consolidated engagement totals  
- **Purpose:** Update centralized dashboard data with the latest engagement statistics.

---

## Workflow Execution Flow

1. Fetch Facebook posts, Instagram media, and Twitter tweets metrics in parallel.  
2. Merge their outputs into a single collection.  
3. Consolidate and normalize engagement metrics across platforms.  
4. Load previous totals to compare current engagement data.  
5. Analyze significant changes, generating alerts based on threshold logic.  
6. Send alerts through Slack for any detected spikes or drops.  
7. Save current totals for use in the next execution.  
8. Update the dashboard data store with the latest metrics.

---

## Prerequisites & Setup

- OAuth2 credentials for Facebook and Instagram APIs.  
- Twitter API credentials with access to retrieve tweet metrics.  
- Slack API credentials for sending messages.  
- n8n workflow static data permissions (for storing and loading totals).  
- A dashboard or visualization system connected to the `dashboardData` key in n8n’s data store.

---

## Key Points

- The threshold for alerts is ±30% change in engagement metrics.  
- Instagram shares default to 0 as the platform does not expose share data.  
- Alerts consolidate increases and decreases separately and send a summary message.  
- Workflow state is preserved between runs using n8n static data for accurate change detection.  
- Data is structured uniformly to simplify downstream processing or visualization.

---

This workflow provides a centralized, automated way to monitor social media engagement trends and respond proactively with timely notifications and updated dashboards.