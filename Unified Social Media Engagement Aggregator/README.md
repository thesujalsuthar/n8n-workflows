# Smart Social Media Engagement Tracker

## Overview

The **Smart Social Media Engagement Tracker** is an automated workflow designed to aggregate and analyze engagement metrics across multiple social media platforms—Twitter, Instagram, and Facebook—and send a consolidated report via email. This workflow helps you monitor likes, shares, and comments from your social media posts daily to assess overall engagement and improve your social media strategy.

---

## Workflow Breakdown

### 1. Get Tweet Details

- **Node:** Get Tweet Details
- **Platform:** Twitter
- **Operation:** Fetch tweet details by tweet ID
- **Authentication:** OAuth2 (Twitter OAuth2 API)
- **Functionality:** Retrieves engagement metrics such as likes, retweets (shares), and replies (comments) for a specific tweet.
- **Input Parameter:** `tweet_id` (dynamically supplied from incoming JSON)

---

### 2. Get Instagram Media

- **Node:** Get Instagram Media
- **Platform:** Instagram
- **Operation:** Fetch media details by media ID
- **Authentication:** OAuth2 (Instagram OAuth2 API)
- **Functionality:** Retrieves the like and comment counts for a specific Instagram media item.
- **Input Parameter:** Fixed media ID (`id=1` in this workflow, can be customized)

---

### 3. Get Facebook Posts

- **Node:** Get Facebook Posts
- **Platform:** Facebook
- **Operation:** Retrieve posts with engagement insights
- **Authentication:** OAuth2 (Facebook Graph API)
- **Functionality:** Fetches the last 10 posts from the connected Facebook account and extracts the total counts of likes, shares, and comments.
- **Parameters:**
  - `operation`: getPosts
  - `page`: 1 (first page of posts)
  - `fields`: likes, shares, comments
  - `limit`: 10 posts

---

### 4. Analyze Engagement

- **Node:** Analyze Engagement
- **Type:** Function (JavaScript)
- **Functionality:** Aggregates engagement metrics collected from Twitter, Instagram, and Facebook.
- **Logic Summary:**
  - Initializes counters for likes, shares, and comments.
  - Increments counters based on:
    - Twitter public metrics: likes, retweets (shares), replies (comments).
    - Instagram media metrics: likes and comments.
    - Facebook posts: sums likes, shares, and comments across all fetched posts.
- **Output:** A JSON object containing the aggregated engagement metrics.

---

### 5. Send Email Report

- **Node:** Send Email Report
- **Operation:** Send email with aggregated results
- **Authentication:** SMTP credentials for your email provider
- **Email Content:**
  - From: your_email@example.com
  - To: recipient@example.com
  - Subject: Daily Social Media Engagement Report
  - Body: Displays total likes, shares, and comments collected across all platforms.
  - Additional insights placeholder for trend analysis and posting recommendations.
- **Parameters:**
  - Uses workflow expressions to dynamically insert engagement data into the email body.
  - SMTP details:
    - User, Password
    - Host, Port, Secure (TLS/SSL configuration)

---

## Credentials Setup

Before running the workflow, ensure you have valid OAuth2 credentials set up for:

- Twitter OAuth2 API
- Instagram OAuth2 API
- Facebook Graph API

Also configure SMTP credentials to allow the workflow to send email reports.

---

## Execution Flow

1. **Start** at `Get Tweet Details` node using a tweet ID.
2. Data flows sequentially to:
   - `Get Instagram Media`
   - `Get Facebook Posts`
3. Engagement data from all platforms is aggregated in `Analyze Engagement`.
4. The final summarized report is emailed out by `Send Email Report`.

---

## Configuration Tips

- **Tweet ID and Instagram Media ID:** Customize the input parameters to analyze specific posts/media.
- **Email Addresses:** Replace `your_email@example.com` and `recipient@example.com` with actual email addresses.
- **SMTP Server:** Update SMTP host, port, and security settings to match your email provider.
- **Timezone:** Workflow timezone is set to `America/New_York` but can be updated in the settings.
- **Extensibility:** You can add more platforms or automate scheduling to run this workflow daily.

---

## Summary

This workflow provides a streamlined way to consolidate social media engagement metrics and deliver actionable insights via email daily. It saves time monitoring multiple platforms individually and ensures you stay informed about overall social performance.

---

**Note:** This workflow is inactive by default. Enable and schedule according to your operational needs.