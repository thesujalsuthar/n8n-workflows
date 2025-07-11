# Automated Cryptocurrency Portfolio Tracker and Alert System

## Overview

This workflow automates the process of tracking the value of a cryptocurrency portfolio by fetching live prices from multiple exchanges, calculating the total portfolio value, logging it, and sending alerts when significant changes occur.

---

## Workflow Components

### 1. Fetch Binance Prices

- **Type:** HTTP Request
- **Function:** Retrieves the latest price data for BTC, ETH, and BNB trading pairs against USDT from Binance API.
- **Endpoint:** `https://api.binance.com/api/v3/ticker/price?symbols=["BTCUSDT","ETHUSDT","BNBUSDT"]`
- **Response Format:** JSON

### 2. Fetch Coinbase BTC Price

- **Type:** HTTP Request
- **Function:** Fetches the current spot price of Bitcoin (BTC) in USD from Coinbase API.
- **Endpoint:** `https://api.coinbase.com/v2/prices/BTC-USD/spot`
- **Response Format:** JSON

### 3. Fetch Coinbase ETH Price

- **Type:** HTTP Request
- **Function:** Fetches the current spot price of Ethereum (ETH) in USD from Coinbase API.
- **Endpoint:** `https://api.coinbase.com/v2/prices/ETH-USD/spot`
- **Response Format:** JSON

### 4. Calculate Portfolio Value

- **Type:** Function
- **Function:** 
  - Defines a static portfolio holding:
    - BTC: 0.5
    - ETH: 2
    - BNB: 10
  - Parses Binance prices from the first node.
  - Extracts Coinbase BTC and ETH prices as fallback.
  - Determines the current price for each asset, preferring Binance prices.
  - Calculates the total portfolio value by multiplying holdings with respective prices.
  - Outputs prices and total portfolio value as JSON.

```js
const portfolio = {
  BTC: 0.5,
  ETH: 2,
  BNB: 10
};

const pricesBinance = {};

// Parse Binance prices
items[0].json.forEach(item => {
  const symbol = item.symbol.replace('USDT', '');
  pricesBinance[symbol] = parseFloat(item.price);
});

// Coinbase prices
const coinbaseBTC = parseFloat(items[1].json.data.amount);
const coinbaseETH = parseFloat(items[2].json.data.amount);

// Use Binance prices, fallback to Coinbase if unavailable
const btcPrice = pricesBinance.BTC || coinbaseBTC;
const ethPrice = pricesBinance.ETH || coinbaseETH;
const bnbPrice = pricesBinance.BNB || 0;

const portfolioValue = 
  (portfolio.BTC * btcPrice) + 
  (portfolio.ETH * ethPrice) + 
  (portfolio.BNB * bnbPrice);

return [{ json: {
  btcPrice,
  ethPrice,
  bnbPrice,
  portfolioValue
} }];
```

### 5. Append Portfolio Value to File

- **Type:** File Append
- **Function:** Records the calculated portfolio value in a JSON file for historical tracking.
- **File:** `cryptocurrency-portfolio-values.json`
- **Options:**
  - Append mode (new data is appended, not overwritten)
  - JSON indentation: 2 spaces
  - Includes timestamp in ISO date-time format
  - Adds newline after each entry

### 6. Check for Significant Change

- **Type:** Function
- **Function:** 
  - Reads the last two entries from the portfolio values file.
  - Calculates the percentage change between the two most recent portfolio values.
  - Compares the absolute percentage change against a defined threshold of 5%.
  - Outputs the change percentage if the threshold is exceeded; otherwise, no output.

```js
const THRESHOLD_PERCENT = 5;
const fs = require('fs');
const path = 'cryptocurrency-portfolio-values.json';

let contents = '';
try {
  contents = fs.readFileSync(path, 'utf8');
} catch (e) {
  return [];
}

const lines = contents.trim().split('\n');
if (lines.length < 2) {
  return [];
}

const getValueFromLine = (line) => {
  try {
    const obj = JSON.parse(line);
    return obj.portfolioValue;
  } catch(e) {
    return null;
  }
}

const lastValue = getValueFromLine(lines[lines.length-1]);
const prevValue = getValueFromLine(lines[lines.length-2]);

if (lastValue === null || prevValue === null) {
  return [];
}

const changePercent = ((lastValue - prevValue) / prevValue) * 100;

if (Math.abs(changePercent) >= THRESHOLD_PERCENT) {
  return [{ json: { changePercent }}];
} else {
  return [];
}
```

### 7. Send Email Alert

- **Type:** Email Send
- **Function:** Sends an email alert when a significant portfolio value change (≥5%) is detected.
- **Email Subject:** 🚨 Portfolio Alert: Significant Change Detected
- **Email Body:** "Your cryptocurrency portfolio value has changed by {changePercent}% since the last check."
- **Credentials:** Requires configured SMTP account credentials.

### 8. Send Messaging App Alert

- **Type:** Telegram Send Message
- **Function:** Sends an alert message via Telegram when a significant change is detected.
- **Message:** "🚨 Portfolio Alert: Your cryptocurrency portfolio value has changed by {changePercent}%."
- **Credentials:** Requires Telegram API credentials and specified chat ID.

---

## Workflow Sequence

1. Fetch latest prices from Binance.
2. Fetch BTC price from Coinbase.
3. Fetch ETH price from Coinbase.
4. Calculate total portfolio value.
5. Append the portfolio value to a JSON log file.
6. Check the last two logged values for a significant change (≥5%).
7. If change detected, send:
   - An email alert.
   - A Telegram message alert.

---

## How To Use

1. **Setup Credentials**
   - Configure your SMTP details for email sending.
   - Setup Telegram API credentials and replace `"YOUR_CHAT_ID"` with your chat ID.

2. **Configure Portfolio (Optional)**
   - Update the holdings in the `Calculate Portfolio Value` function to reflect your portfolio.

3. **Run Workflow**
   - Schedule or trigger this workflow to run at your desired frequency (e.g., hourly or daily).

4. **Monitor Alerts**
   - Monitor your email and Telegram for portfolio value change alerts.

---

## File Storage

- The portfolio values are stored as newline-delimited JSON objects in `cryptocurrency-portfolio-values.json`.
- Each entry includes portfolio value and timestamp for historical tracking.

---

## Alert Threshold

- The threshold for sending alerts is set to a 5% change in portfolio value between the last two recorded entries.
- This can be adjusted in the `Check for Significant Change` node by modifying the `THRESHOLD_PERCENT` constant.

---

## Requirements

- Access to public Binance and Coinbase price APIs.
- SMTP server credentials for sending email notifications.
- Telegram bot token and chat ID for Telegram alerts.
- Node.js environment with filesystem access (for file reading/writing).

---

Thank you for using the Automated Cryptocurrency Portfolio Tracker and Alert System!