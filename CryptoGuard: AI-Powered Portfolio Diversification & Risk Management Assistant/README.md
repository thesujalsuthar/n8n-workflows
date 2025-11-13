# AI-Powered Cryptocurrency Portfolio Diversification and Risk Management Advisor

## Overview

This workflow leverages real-time cryptocurrency market data and AI to analyze your crypto portfolio, assess risk exposure, and provide personalized diversification and risk management advice. The insights are delivered via a customized email alert to help you make informed decisions about your investments.

---

## Workflow Steps

### 1. Fetch Real-Time Market Data

- **Node Type:** HTTP Request  
- **Description:** Retrieves the latest price and 24-hour percentage change data for a set of popular cryptocurrencies (Bitcoin, Ethereum, Ripple, Litecoin, Chainlink, Cardano, Solana, Polkadot, Binance Coin, and Dogecoin) from the CoinGecko API.
- **Endpoint:** `https://api.coingecko.com/api/v3/simple/price`
- **Parameters:**
  - `ids`: List of cryptocurrencies to fetch.
  - `vs_currencies`: USD prices.
  - `include_24hr_change`: Include 24-hour price change percentage.
- **Output:** JSON object with current prices and 24h changes for each cryptocurrency.

### 2. Analyze Portfolio Risk Exposure

- **Node Type:** Function  
- **Description:**  
  This node calculates your portfolio’s current value and risk exposure based on real-time prices and your cryptocurrency holdings.  
- **Input:**  
  - Portfolio holdings, e.g.:  
    ```json
    [
      { "symbol": "bitcoin", "amount": 1.5 },
      { "symbol": "ethereum", "amount": 10 }
    ]
    ```
  - Real-time prices from the previous node.
- **Calculations performed:**  
  - Value of each holding in USD (`amount * current price`).
  - Portfolio total value.
  - Exposure percentage of each coin `(holding value / total portfolio value) * 100`.
  - Basic risk score: Weighted sum of absolute 24h price changes, weighted by exposure percentage. A higher score indicates increased portfolio volatility risk.
- **Output:**  
  Detailed holdings with values, exposure percentages, total portfolio value, and an overall risk score.

### 3. Generate Diversification & Risk Advice (AI)

- **Node Type:** OpenAI GPT-4  
- **Description:**  
  This node uses an AI language model to provide personalized advice based on the portfolio summary, current market trends, and risk score.  
- **Prompt Highlights:**  
  - You are a financial AI assistant specialized in crypto portfolio management.
  - Analyze portfolio data provided.
  - Provide diversification recommendations and risk management strategies.
  - Suggest adding or reducing specific cryptocurrencies.
  - Focus on risk reduction if risk score exceeds 5.
  - Respond in structured JSON containing:
    - `recommendations` (array of actionable suggestions)
    - `rationale` (explanation of advice)
    - `urgencyLevel` (Low, Medium, High)
- **Output:** Structured JSON with AI-generated diversification and risk advice.

### 4. Format Alert Message

- **Node Type:** Function  
- **Description:**  
  Parses the JSON output from the AI node and constructs a clear, user-friendly alert message incorporating:  
  - User’s portfolio total value.  
  - Calculated risk score.  
  - Risk urgency level.  
  - Actionable recommendations.  
  - Rationale behind the advice.  
- **Output:** A formatted alert message string ready for sending.

### 5. Send Personalized Alert Email

- **Node Type:** Email Send  
- **Description:**  
  Sends the formatted alert message via email to the user.  
- **Email Configuration:**
  - Recipient email (set to your desired address).
  - Subject: “Your Crypto Portfolio Diversification & Risk Management Report”
  - Email Body: Both plain text and HTML versions of the alert message.
- **Credentials:** Requires valid SMTP credentials to send emails.

---

## Configuration & Usage

- **Portfolio Input:**  
  In the "Analyze Portfolio Risk Exposure" node, update the `portfolio` array with your current holdings including `symbol` (cryptocurrency id) and `amount` (units held).

- **Email Settings:**  
  Modify the recipient email address in the "Send Personalized Alert Email" node (`channel` parameter). Ensure your SMTP credentials are configured correctly in n8n.

- **AI Model:**  
  Uses GPT-4 model via OpenAI credentials configured in n8n.

---

## Summary

This workflow automates your cryptocurrency portfolio monitoring by:  
- Fetching real-time prices and market changes.  
- Calculating portfolio values and risk exposure.  
- Generating AI-driven diversification and risk management advice.  
- Delivering personalized advice directly to your inbox in a clear and actionable format.

Stay proactive with your crypto investments by leveraging this AI-powered advisor!