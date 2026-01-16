# Real-Time Cryptocurrency Market Arbitrage Opportunity Alert System

## Overview

This workflow continuously monitors real-time cryptocurrency prices from Binance and Kraken exchanges, identifies arbitrage opportunities based on configurable profit thresholds, and immediately notifies users via email and Slack alerts. It supports the trading pairs: BTCUSDT, ETHUSDT, and LTCUSDT.

---

## Workflow Components

### 1. **Trigger Every Minute**

- **Type:** Time Trigger  
- **Interval:** Every 60 seconds  
- **Role:** Automatically starts the workflow every minute to fetch the latest price data and detect arbitrage opportunities in near real-time.

---

### 2. **Get Binance Prices**

- **Type:** HTTP Request  
- **API Endpoint:** `https://api.binance.com/api/v3/ticker/price`  
- **Parameters:**
  - Symbols: `["BTCUSDT","ETHUSDT","LTCUSDT"]`  
- **Role:** Fetches the latest ticker prices from Binance for the specified pairs.

---

### 3. **Get Kraken Prices**

- **Type:** HTTP Request  
- **API Endpoint:** `https://api.kraken.com/0/public/Ticker`  
- **Parameters:**
  - Pair: `XBTUSDT,ETHUSDT,LTCUSDT`  
- **Role:** Retrieves the latest ticker prices from Kraken for the given pairs. Note that Kraken uses "XBT" instead of "BTC" which is normalized in the next step.

---

### 4. **Compare Prices & Detect Arbitrage**

- **Type:** Function  
- **Role:**  
  - Parses and normalizes the price data from both Binance and Kraken.  
  - Converts Kraken symbols (e.g., `XBTUSDT`) to Binance style (e.g., `BTCUSDT`).  
  - Calculates arbitrage profit percentages both ways:  
    - Buy on Binance, sell on Kraken  
    - Buy on Kraken, sell on Binance  
  - Uses a minimum profit threshold of 0.5% to filter arbitrage signals.  
  - Outputs detected arbitrage opportunities in a structured JSON format if any meet the threshold.

---

### 5. **Send Email Alert**

- **Type:** Email Send  
- **Trigger Condition:** Runs only if arbitrage opportunities are detected by the previous node.  
- **Alert Content:**  
  - Subject: e.g., `Crypto Arbitrage Alert - BTCUSDT`  
  - Body includes:  
    - Trading pair  
    - Buy exchange and price  
    - Sell exchange and price  
    - Estimated profit percentage  
- **Role:** Sends an immediate email notification to alert users of arbitrage opportunities.

*Note: Configure SMTP/email credentials in n8n to enable sending.*

---

### 6. **Send Slack Alert**

- **Type:** Slack Node  
- **Trigger Condition:** Runs alongside the email alert on detected arbitrage.  
- **Alert Message:**  
  - Customized Slack message notifying about the arbitrage opportunity with pair, buy/sell exchanges, prices, and profit.  
- **Role:** Pushes instantaneous alerts directly into Slack channels for fast team awareness.

*Note: Slack credentials and webhook URL should be configured in n8n for proper delivery.*

---

## Data Flow and Integration

- **Trigger Every Minute** initiates the workflow every 60 seconds.  
- The **Get Binance Prices** and **Get Kraken Prices** nodes run concurrently to fetch market prices from their respective APIs.  
- The data is then passed into the **Compare Prices & Detect Arbitrage** function node which calculates profit margins and identifies opportunities.  
- When arbitrage is detected (profit ≥0.5%), alerts are sent both via **Send Email Alert** and **Send Slack Alert** nodes in parallel.

---

## Configuration

- **Trading Pairs:** BTCUSDT, ETHUSDT, LTCUSDT  
- **Minimum Arbitrage Profit Threshold:** 0.5% (modifiable in the function node code)  
- **Email Settings:** Requires prior configuration of SMTP/email credentials within n8n.  
- **Slack Settings:** Requires Slack credentials or webhook configuration in n8n for Slack messages.

---

## Usage

1. Import this workflow into your n8n instance.  
2. Configure email SMTP node credentials and Slack credentials/webhook.  
3. Adjust the minimum profit threshold or trading pairs as needed in the function node.  
4. Activate the workflow.  
5. Receive real-time alerts via email and Slack whenever arbitrage opportunities arise.

---

## Notes

- Kraken uses the symbol `XBT` for Bitcoin whereas Binance uses `BTC`; this is automatically normalized.  
- The workflow processes only specific pairs listed but can be extended by modifying the API calls and the function node mapping.  
- Profit calculations do not account for fees, slippage, or transfer time — users should verify before trading.

---

## License

This workflow is provided as-is for educational and monitoring purposes. Use responsibly.

---

**Stay alert and happy arbitraging!**