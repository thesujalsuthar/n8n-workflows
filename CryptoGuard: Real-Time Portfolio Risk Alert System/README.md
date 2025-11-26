# Real-Time Cryptocurrency Portfolio Risk Monitoring and Alert System

## Overview
This workflow continuously monitors a user’s cryptocurrency portfolio in real-time, assesses portfolio risk based on market price changes, and sends alerts via email and SMS when the risk exceeds a defined threshold.

---

## Workflow Nodes and Their Functions

### 1. **Trigger**
- **Type:** Interval Trigger
- **Purpose:** Initiates the workflow every 5 minutes (300 seconds).
- **Details:** This node runs automatically at regular intervals to keep the portfolio risk updated in near real-time.

---

### 2. **Set User Portfolio**
- **Type:** Function
- **Purpose:** Defines the user’s portfolio and contact information.
- **Details:** 
  - Contains an array of crypto assets with: 
    - `id` (CoinGecko identifier),
    - `symbol`,
    - `name`,
    - `amount` owned.
  - Stores user contact details (`userEmail`, `userPhone`) used for sending alerts.
- **Example Portfolio:**
  ```json
  [
    { "id": "bitcoin", "symbol": "btc", "name": "Bitcoin", "amount": 1.5 },
    { "id": "ethereum", "symbol": "eth", "name": "Ethereum", "amount": 10 },
    { "id": "cardano", "symbol": "ada", "name": "Cardano", "amount": 5000 }
  ]
  ```

---

### 3. **Fetch Market Data**
- **Type:** HTTP Request
- **Purpose:** Retrieves real-time cryptocurrency price and 24-hour change data from the CoinGecko API.
- **Details:** 
  - Requests current price in USD and 24h percent price change for all coins in the portfolio.
  - Queries the API endpoint: `https://api.coingecko.com/api/v3/simple/price`
  - Uses portfolio coin IDs dynamically to request only relevant data.
- **Authentication:** Access token header (if needed, depending on setup).

---

### 4. **Assess Risk**
- **Type:** Function
- **Purpose:** Calculates the portfolio risk based on fetched market data.
- **Logic:**
  - Enhances each coin with:
    - `current_price_usd`
    - `change_24h_percent`
    - `value_usd` = current price × amount owned
  - Calculates `totalValue` of the portfolio by summing individual coin values.
  - Computes each coin’s allocation percentage of the portfolio.
  - Defines a `risk_factor` for each coin as the absolute 24h price change multiplied by allocation percent.
  - Calculates `overallRisk` as the sum of all coin risk factors.
  - Compares `overallRisk` to a predefined `RISK_THRESHOLD` (0.2).
- **Outputs:**
  - Enhanced portfolio information with risk factors.
  - Overall portfolio risk score.
  - Boolean flag `riskExceeded` indicating if threshold is surpassed.

---

### 5. **Check Risk Threshold**
- **Type:** If Condition
- **Purpose:** Evaluates whether the portfolio risk exceeds the defined threshold.
- **Condition:** 
  - Proceeds if `riskExceeded` equals `true`.
  - If risk is below threshold, the workflow ends without sending alerts.

---

### 6. **Send Risk Alert Email**
- **Type:** Email Send
- **Purpose:** Sends a detailed email alert to the user if risk is too high.
- **Email Details:**
  - **From:** `alerts@cryptomonitor.com`
  - **To:** User email from portfolio data.
  - **Subject:** "Portfolio Risk Alert: Threshold Exceeded"
  - **Body:** 
    - Overall risk score (with 3 decimals).
    - Total portfolio value formatted as USD.
    - Coin-by-coin breakdown including name, symbol, value, 24-hour change, and individual risk factor.
    - A message prompting user action to review the portfolio.

---

### 7. **Send SMS Alert**
- **Type:** HTTP Request (POST)
- **Purpose:** Sends a concise SMS notification via Twilio if risk threshold is exceeded.
- **Details:**
  - Posts to Twilio API endpoint: `https://api.twilio.com/2010-04-01/Accounts/{AccountSid}/Messages.json`
  - Uses basic authentication with Twilio credentials.
  - SMS Body: Brief alert with overall risk score and a prompt to check email for more info.
  - Sends SMS from configured Twilio phone number.
  - Recipient’s phone number is taken from user portfolio data.

---

## Setup and Configuration

### Credentials Required
- **CoinGecko API**: Public API no authentication required, though an access token header is configured for extensibility.
- **Email Account**: Configured for sending emails from `alerts@cryptomonitor.com` (SMTP or other mail service as per n8n email node settings).
- **Twilio Account**: 
  - Account SID
  - Auth Token
  - Verified Twilio phone number

### Customizable Parameters
- **Portfolio Definition:** Update in the **Set User Portfolio** node.
- **Risk Threshold:** Change `RISK_THRESHOLD` value inside **Assess Risk** node to adjust sensitivity (default `0.2`).
- **Alert Contacts:** Set user email and phone number in **Set User Portfolio** node.
- **Trigger Interval:** Modify the interval time in the **Trigger** node, currently set to every 5 minutes.

---

## How It Works

1. The workflow triggers every 5 minutes.
2. The user's portfolio and contact details are set programmatically.
3. The latest prices and 24-hour price change percentages for all portfolio coins are fetched from CoinGecko.
4. The workflow calculates the value of each asset and estimates risk factors by combining price volatility and allocation size.
5. It sums up all coin risk factors to produce an overall portfolio risk score.
6. If the overall risk surpasses the defined threshold:
   - The system sends an email with detailed risk breakdown.
   - Simultaneously, a brief SMS alert is sent for immediate notification.
7. If risk is below the threshold, no alerts are issued.

---

## Notes
- This workflow can be extended with additional risk metrics or alternative alert channels.
- Rate limits on the CoinGecko API and Twilio should be respected.
- Always ensure sensitive credentials (Twilio, email) are securely stored and referenced.

---

## License
This workflow and its contents are provided as-is without warranty. Customize responsibly and test thoroughly before deployment.