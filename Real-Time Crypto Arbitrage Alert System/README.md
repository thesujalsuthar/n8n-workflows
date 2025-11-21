# Real-Time Cryptocurrency Price Arbitrage Alert System

This **n8n** workflow monitors cryptocurrency prices across three major exchanges — Binance, Coinbase, and Kraken — and detects real-time arbitrage opportunities. When profitable arbitrage is identified after accounting for exchange fees and transfer time penalties, the system sends instant alerts via **Telegram** and **Email**.

---

## Workflow Overview

### 1. Set Crypto Symbols & Alert Config
- Defines the target cryptocurrency trading pairs for each exchange:
  - Binance symbol (e.g., `BTCUSDT`)
  - Coinbase symbol (e.g., `BTC-USD`)
  - Kraken symbol (e.g., `XBTUSD`)
- Configures the email address to receive alerts.
- Emits JSON data with these parameters for downstream nodes.

### 2. Fetch Current Prices from Exchanges
The workflow retrieves the latest trade prices in real-time from each exchange via HTTP GET requests:

- **Binance Price Fetch**
  - Endpoint: `https://api.binance.com/api/v3/ticker/price?symbol={{ symbol }}`
  - Returns the current price for the Binance trading pair.

- **Coinbase Price Fetch**
  - Endpoint: `https://api.pro.coinbase.com/products/{{ symbolCoinbase }}/ticker`
  - Returns the current ticker info including price for Coinbase.

- **Kraken Price Fetch**
  - Endpoint: `https://api.kraken.com/0/public/Ticker?pair={{ symbolKraken }}`
  - Returns public ticker data including price for Kraken.

All three return JSON formatted responses used for price analysis.

### 3. Arbitrage Analyzer (Function Node)
- Extracts prices for the defined cryptocurrency pair from all three exchanges.
- Applies known fee structures for each exchange:

  | Exchange | Fee     |  
  |----------|---------|  
  | Binance  | 0.1%    |  
  | Coinbase | 0.5%    |  
  | Kraken   | 0.26%   |

- Accounts for transfer times (in minutes) as a penalty on profits due to risk or costs of crypto transfer:

  | Exchange | Transfer Time (min) |  
  |----------|---------------------|  
  | Binance  | 10                  |  
  | Coinbase | 15                  |  
  | Kraken   | 20                  |

- Calculates adjusted arbitrage profit opportunities by comparing all buy-sell combinations among exchanges.
- Outputs an array of arbitrage opportunities where the adjusted profit is positive.

### 4. Check Arbitrage Present (IF Node)
- Checks if any arbitrage opportunities were found.
- If yes, triggers alerts.

### 5. Send Telegram Alert
- Sends a formatted message via Telegram bot API.
- Message includes details:
  - Buy exchange and price
  - Sell exchange and price
  - Estimated profit margin after fees and transfer penalties
  - Cumulative transfer time estimate

### 6. Send Email Alert
- Sends an email with the same details as the Telegram alert.
- Email is sent from the configured SMTP email account to the alert email.

---

## Configuration

### Required Credentials
- **Telegram API:** Telegram bot credentials with access to your chat.
- **SMTP Account:** SMTP server credentials for sending email.
- No API keys needed for public price endpoints of Binance, Coinbase, or Kraken.

### Symbols & Alert Email
Set your desired cryptocurrency and alert email in the **Set Crypto Symbols & Alert Config** node by modifying:

```js
{
  symbol: 'BTCUSDT',          // Binance symbol
  symbolCoinbase: 'BTC-USD',  // Coinbase symbol
  symbolKraken: 'XBTUSD',     // Kraken symbol
  alertEmail: 'user@example.com'  // Email to receive alerts
}
```

---

## How It Works

1. The workflow starts by setting crypto symbols and alert config.
2. Concurrently fetches prices from Binance, Coinbase, and Kraken.
3. The Arbitrage Analyzer function evaluates all possible buy-sell pairs by:
   - Adding fees for buying
   - Subtracting fees for selling
   - Further adjusting profit by estimated transfer time penalties.
4. If any profitable arbitrage exists, the system:
   - Sends an immediate Telegram alert.
   - Sends an email alert detailing the opportunity.
5. If none are present, no alerts are sent.

---

## Notes

- This workflow uses public APIs that do not require authentication.
- The transfer time penalty is an arbitrary factor to account for risks and delays.
- Fees are hardcoded but can be modified inside the Function node.
- Ensure your Telegram bot and email credentials are correctly configured.
- Adjust polling frequency as needed to balance update speed against API rate limits.

---

## License

This workflow is provided as-is for educational and practical use without warranty. Customize as needed for your specific arbitrage strategies and risk management.