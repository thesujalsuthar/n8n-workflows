# Unified Social Media Engagement Aggregator

This n8n workflow aggregates social media engagement metrics from Facebook, Twitter, Instagram, and LinkedIn every 15 minutes, stores the consolidated metrics in a Google Sheet, and sends a Slack notification when total likes across all platforms exceed a milestone.

---

## Workflow Overview

### 1. Schedule Trigger
- **Type:** Schedule Trigger
- **Interval:** Runs every 15 minutes
- **Purpose:** Initiates the workflow on a time-based trigger to collect the latest social media metrics.

### 2. Facebook Metrics
- **Node Type:** Facebook Graph API
- **Authentication:** OAuth2 ("Facebook OAuth2" credentials required)
- **Operation:** Retrieves post metrics including likes, shares, comments, and follower count from the authenticated user’s Facebook account.
- **Fields gathered:** 
  - Likes summary
  - Shares count
  - Comments summary
  - Followers count

### 3. Twitter Metrics
- **Node Type:** Twitter
- **Authentication:** OAuth1 ("Twitter OAuth1" credentials required)
- **Operation:** Fetches user tweets with public engagement metrics.
- **Additional fields:** 
  - `public_metrics` (includes like count, retweet count, reply count)
  - Maximum 100 tweets pulled per execution

### 4. Instagram Metrics
- **Node Type:** HTTP Request (using Instagram Graph API)
- **Authentication:** OAuth2 or HTTP Basic (configure credentials accordingly)
- **Endpoint:** `me?fields=followers_count,media_count,media{like_count,comments_count}`
- **Purpose:** Fetches follower count and aggregated likes and comments from media posted by the authenticated Instagram user.

### 5. LinkedIn Metrics
- **Node Type:** LinkedIn API
- **Authentication:** OAuth2 ("LinkedIn OAuth2" credentials required)
- **Operation:** Retrieves organization-level statistics including:
  - Followers count
  - Likes
  - Comments
  - Shares

### 6. Aggregate Metrics
- **Node Type:** Function
- **Purpose:** Consolidates data returned from Facebook, Twitter, Instagram, and LinkedIn nodes into a unified JSON structure.
- **Operations performed:**
  - Facebook: Totals likes, shares, comments, followers
  - Twitter: Sums likes, retweets, replies, and provides follower count
  - Instagram: Aggregates likes, comments, followers from media and profile
  - LinkedIn: Includes followers, likes, comments, shares

### 7. Append To Google Sheet
- **Node Type:** Google Sheets
- **Authentication:** OAuth2 ("Google Sheets OAuth2" credentials required)
- **Operation:** Appends a new row to a specified Google Sheet with the current datetime and all aggregated metrics.
- **Fields appended:**
  - Timestamp
  - Facebook likes, shares, comments, followers
  - Twitter likes, retweets, replies, followers
  - Instagram likes, comments, followers
  - LinkedIn likes, comments, shares, followers

### 8. Check Milestone Likes
- **Node Type:** If Condition
- **Condition:** Checks if the total likes across Facebook, Twitter, Instagram, and LinkedIn are greater than 1000.
- **Purpose:** Triggers notification only if the likes milestone is exceeded.

### 9. Send Notification
- **Node Type:** Slack
- **Authentication:** Slack API credentials ("Slack API" credentials required)
- **Operation:** Sends a message to a configured Slack channel notifying that the social media likes milestone has been reached with the total likes count.

---

## Connections Flow

- The **Schedule Trigger** simultaneously triggers all social media metrics nodes.
- Each social media metric node outputs data to the **Aggregate Metrics** function node.
- The aggregated results are sent in parallel to:
  - **Append To Google Sheet** to store the data
  - **Check Milestone Likes** to evaluate if the notification condition is met
- If the milestone condition is true, the **Send Notification** node sends a Slack message.

---

## Setup & Configuration

### Credentials Required
- **Facebook OAuth2 API**
- **Twitter OAuth1 API**
- **Instagram API credentials** (configured in HTTP Request node)
- **LinkedIn OAuth2 API**
- **Google Sheets OAuth2 API**
- **Slack API credentials**

### Google Sheet Setup
- Replace `your_google_sheet_id_here` in the "Append To Google Sheet" node with your Google Sheet ID.
- Ensure the spreadsheet contains a sheet named `Sheet1` or adjust the range accordingly.
- Set headers in the sheet as follows (optional but recommended):
  ```
  Timestamp | Fb Likes | Fb Shares | Fb Comments | Fb Followers | Tw Likes | Tw Retweets | Tw Replies | Tw Followers | IG Likes | IG Comments | IG Followers | LI Likes | LI Comments | LI Shares | LI Followers
  ```

### Instagram HTTP Request Authentication
- Configure with required OAuth2 or HTTP Basic credentials to access Instagram Graph API.
- Make sure the token has appropriate permissions to read media and follower data.

---

## Execution

- The workflow runs automatically every 15 minutes.
- Each run appends current engagement metrics from all platforms to the Google Sheet.
- When total likes across platforms surpass 1000, a Slack notification is sent.

---

## Notes

- Make sure all API credentials have the correct scopes and permissions.
- API rate limits may affect how often data can be fetched.
- Adjust the milestone threshold and schedule trigger interval as needed.
- The workflow is currently **inactive** and must be activated in n8n.