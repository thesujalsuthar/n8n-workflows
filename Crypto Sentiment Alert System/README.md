# Crypto Market Sentiment Analysis and Alert

This workflow monitors social media and news sources for cryptocurrency-related content, performs sentiment analysis, detects significant sentiment changes, and sends alerts via Telegram.

---

## Workflow Overview

The workflow consists of the following sequential steps:

### 1. Fetch Tweets
- **Node:** `Fetch Tweets`
- **Description:** Retrieves recent tweets containing `#bitcoin`, `#ethereum`, or `#crypto`.
- **Parameters:**
  - Search query: `#bitcoin OR #ethereum OR #crypto`
  - Maximum results: 100
  - Tweet mode: Extended (to get full tweet text)

### 2. Fetch News
- **Node:** `Fetch News`
- **Description:** Fetches recent news articles mentioning bitcoin, ethereum, or crypto.
- **Parameters:**
  - Query terms: `bitcoin, ethereum, crypto`
  - Language: English
  - Sort by: Published date (latest first)
  - Page size: 50 articles

### 3. Prepare Text
- **Node:** `Prepare Text`
- **Description:** Extracts relevant text content from tweets or news articles.
- **Logic:** 
  - For tweets: uses `full_text` or `text`
  - For news: concatenates `title` and `description`
- **Output:** A single text field named `text` for sentiment analysis

### 4. Sentiment Analysis (OpenAI)
- **Node:** `Sentiment Analysis (OpenAI)`
- **Description:** Uses OpenAI GPT-3.5 Turbo model to analyze the sentiment of each text.
- **Input:** Text from the previous step
- **Output:** JSON with:
  - `score`: Sentiment score between -1 (negative) to 1 (positive)
  - `label`: One of `negative`, `neutral`, or `positive`

### 5. Parse Sentiment
- **Node:** `Parse Sentiment`
- **Description:** Parses the JSON response from OpenAI to extract the sentiment score and label.
- **Output:** JSON containing:
  - `score`
  - `label`
  - `originalText` (the raw sentiment JSON string)

### 6. Identify Cryptocurrencies
- **Node:** `Identify Cryptocurrencies`
- **Description:** Detects which cryptocurrencies are mentioned in the original text.
- **Crypto Keywords:** `bitcoin, btc, ethereum, eth, crypto, cryptocurrency`
- **Output:** Adds field `detectedCrypto` listing matched keywords

### 7. Detect Significant Sentiment Change
- **Node:** `Detect Significant Sentiment Change`
- **Description:** Compares current sentiment scores with previously stored values in global state to detect significant changes (> 0.3 difference).
- **Logic:**
  - Loads previous sentiment scores from global storage
  - For each detected cryptocurrency, compares current and previous scores
  - Triggers alert if the absolute difference > 0.3
  - Updates global state with current sentiment scores
- **Output:** Alerts containing cryptocurrency symbol, change value, and new sentiment score (or empty if no significant change)

### 8. Send Telegram Alert
- **Node:** `Send Telegram Alert`
- **Description:** Sends a message to a specified Telegram chat when a significant sentiment change is detected.
- **Message Format:**  
  `Alert: Significant sentiment change detected for {CRYPTO}. Change: {CHANGE}, new sentiment score: {NEW SCORE}`
- **Parameters:**
  - `chatId`: Your Telegram chat ID (must be set)

---

## Configuration

- **Twitter Node:** Requires Twitter API credentials.
- **News API Node:** Requires API key for NewsAPI.
- **OpenAI Node:** Requires OpenAI API key.
- **Telegram Node:** Requires Telegram bot token and chat ID to send alerts.

---

## How it Works

1. Tweets and news articles about bitcoin, ethereum, and crypto are collected.
2. Text content is extracted and fed to OpenAI's sentiment analysis model.
3. Sentiment scores and labels are parsed and mapped to corresponding cryptocurrencies.
4. Current sentiment values are compared against previous ones.
5. If a significant sentiment change is detected for any cryptocurrency, an alert message is sent to Telegram.

---

## Notes

- The global state (`previousSentiment`) tracks sentiment scores across workflow executions.
- Sentiment threshold for alerts is set to a change greater than 0.3 (adjustable in the function node).
- Make sure to replace `YOUR_TELEGRAM_CHAT_ID` with your actual Telegram chat ID in the Telegram node.
- Ensure proper API credentials and permissions for all integrated services.