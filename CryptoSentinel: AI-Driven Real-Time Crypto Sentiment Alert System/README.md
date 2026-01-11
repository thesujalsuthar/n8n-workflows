# AI-Powered Crypto Sentiment Analysis and Alert System

This workflow integrates cryptocurrency price data and social media sentiment analysis to provide real-time alerts based on the overall sentiment towards Bitcoin and Ethereum. It fetches the latest crypto prices and recent tweets, analyzes the sentiment of those tweets using OpenAI's GPT-3.5-turbo model, and sends an alert email if sentiment thresholds are exceeded.

---

## Workflow Overview

### 1. Get Crypto Prices

- **Node Name:** Get Crypto Prices  
- **Type:** HTTP Request  
- **Description:** Fetches the current USD prices of Bitcoin and Ethereum from the CoinGecko API.  
- **Endpoint:** `https://api.coingecko.com/api/v3/simple/price`  
- **Query Parameters:**  
  - `ids=bitcoin,ethereum`  
  - `vs_currencies=usd`  
- **Response Format:** JSON  

---

### 2. Get Tweets

- **Node Name:** Get Tweets  
- **Type:** HTTP Request  
- **Description:** Retrieves recent English tweets containing hashtags or mentions of Bitcoin and Ethereum (`#bitcoin`, `#btc`, `#ethereum`, `#eth`) using the Twitter API v2's Recent Search endpoint.  
- **Endpoint:** `https://api.twitter.com/2/tweets/search/recent`  
- **Query Parameters:**  
  - `query=#bitcoin OR #btc OR #ethereum OR #eth lang:en`  
  - `max_results=100` (fetches up to 100 tweets)  
- **Authentication:** Bearer token from Twitter API credentials  
- **Response Format:** JSON  

---

### 3. Sentiment Analysis

- **Node Name:** Sentiment Analysis  
- **Type:** OpenAI  
- **Description:** Sends the collected tweets data to OpenAI's GPT-3.5-turbo model for sentiment analysis. The prompt asks for a JSON response containing a sentiment score for each tweet, where scores range from -1 (very negative) to 1 (very positive).  
- **Model:** gpt-3.5-turbo  
- **Temperature:** 0.7 (to balance creativity and relevance)  
- **Prompt:**  
  - System role guides the model as a cryptocurrency sentiment analysis assistant.  
  - User prompt submits the tweet data for sentiment scoring.

---

### 4. Process Sentiment and Prices

- **Node Name:** Process Sentiment and Prices  
- **Type:** Function  
- **Description:** Parses the AI-generated sentiment JSON output, extracts individual sentiment scores, and calculates the average sentiment across all tweets. This node merges the sentiment data with the crypto price data for consolidated use.  

---

### 5. Check Alert Threshold

- **Node Name:** Check Alert Threshold  
- **Type:** If (Conditional)  
- **Description:** Evaluates whether the average sentiment score surpasses pre-defined thresholds:  
  - Alert if average sentiment is **less than -0.3** (negative sentiment)  
  - Alert if average sentiment is **greater than 0.3** (positive sentiment)  
- Routes the workflow to either send alert or log no alert depending on the sentiment score.

---

### 6. Send Alert Email

- **Node Name:** Send Alert Email  
- **Type:** Email Send  
- **Description:** Sends an email alert if sentiment thresholds are exceeded. Email content includes:  
  - Current Bitcoin and Ethereum prices  
  - Average sentiment score  
  - Detailed sentiment data for each analyzed tweet  
- **From Email:** alerts@cryptosentiment.ai  
- **To Email:** user@example.com (replace with your recipient email)  
- **Subject:** Indicates if sentiment is Positive or Negative based on average sentiment score  
- **Email Formats:** Supports both plain text and HTML formats

---

### 7. No Alert Log

- **Node Name:** No Alert Log  
- **Type:** No Operation (NoOp)  
- **Description:** Logs a message indicating that the sentiment and prices are stable and no alert was triggered. Useful for workflow transparency and monitoring.

---

## Connections Flow

1. **Get Crypto Prices** output connects to **Check Alert Threshold** (input 0).
2. **Get Tweets** output connects to **Sentiment Analysis**.
3. **Sentiment Analysis** output connects to **Process Sentiment and Prices**.
4. **Process Sentiment and Prices** output connects to **Check Alert Threshold** (input 1).
5. **Check Alert Threshold** routes to:  
   - **Send Alert Email** (if sentiment threshold breached)  
   - **No Alert Log** (if sentiment is stable)  

---

## Setup Instructions

1. Configure your Twitter API credentials in n8n and add the **Twitter API Bearer Token** to the “Get Tweets” node credentials.  
2. Configure your OpenAI API credentials for the **Sentiment Analysis** node.  
3. Set your email sending configuration for the **Send Alert Email** node, including sender and recipient emails.  
4. Adjust alert thresholds in the **Check Alert Threshold** node if necessary.  
5. Activate and schedule the workflow to run at the desired frequency for continuous monitoring.

---

## Notes

- The workflow currently uses fixed cryptocurrency IDs (Bitcoin and Ethereum) and search hashtags; you can expand these as needed.  
- The sentiment analysis heavily depends on OpenAI's ability to parse and score the tweets accurately.  
- Ensure API rate limits for Twitter and CoinGecko are respected to avoid request failures.  
- Emails include detailed JSON sentiment data, which can be formatted or processed further if required.

---

This AI-powered system enables proactive monitoring of crypto market sentiment by combining live price data and social media insights, helping users stay informed and respond swiftly to sentiment-driven market shifts.