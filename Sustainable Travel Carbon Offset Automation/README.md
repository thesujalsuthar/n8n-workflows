# AI-Powered Sustainable Travel Carbon Offset Tracker and Optimizer

This workflow enables users to calculate the carbon footprint of their travel, receive personalized carbon offset recommendations, securely process payments for offsets, and get notified via email and Telegram upon successful purchase. It streamlines the entire sustainable travel carbon offsetting process, leveraging AI and automation.

---

## Workflow Overview

### 1. Webhook Trigger  
- **Node:** `Webhook Trigger`  
- **Type:** Webhook (POST)  
- **Path:** `/offset-travel`  
- **Purpose:** Entry point for external clients to send travel data (mode, distance, date, payment info, contact details) to start the workflow.

---

### 2. Calculate Carbon Footprint  
- **Node:** `Calculate Carbon Footprint`  
- **Type:** HTTP Request (POST)  
- **Endpoint:** `https://api.carbonfootprint.com/travel/emissions`  
- **Request Body:**  
  ```json
  {
    "travelMode": <mode>,
    "distanceKm": <distance>,
    "travelDate": <date>
  }
  ```  
- **Purpose:** Sends user travel data to the Carbon Footprint API to calculate the emissions in kilograms of CO2.  
- **Response:** Expected JSON containing `emissions_kg`.

---

### 3. Generate Offset Recommendations  
- **Node:** `Generate Offset Recommendations`  
- **Type:** Function  
- **Function Logic:** Generates two personalized carbon offset purchase recommendations from third-party providers based on emissions. Each recommendation contains:  
  - `providerName`  
  - `offsetAmount` (kg CO2)  
  - `priceUSD` (calculated at provider-specific rates per kg)  
  - `verification` standard  
  - `purchaseUrl` for checkout  

- **Providers Included:**  
  - Green Offset Co (Price: $0.02 per kg CO2, Verified Carbon Standard)  
  - EcoTrees (Price: $0.018 per kg CO2, Gold Standard)

---

### 4. Process Payment  
- **Node:** `Process Payment`  
- **Type:** Stripe Payment  
- **Parameters:**  
  - Payment method: Credit card  
  - Card details: Provided in webhook request (`cardNumber`, `expMonth`, `expYear`, `cvc`)  
  - Amount: Price of selected offset option in USD  
  - Currency: USD  
  - Description: "Carbon Offset Purchase for travel emissions"  
- **Purpose:** Processes the payment securely through Stripe for the offset purchase.

---

### 5. Send Email Notification  
- **Node:** `Send Email Notification`  
- **Type:** Email Send  
- **From:** `no-reply@sustainabletravel.app`  
- **To:** User email provided in webhook (`userEmail`)  
- **Subject:** "Your Carbon Offset Purchase Confirmation"  
- **Body:**  
  Confirmation message including:  
  - Offset provider name  
  - Offset amount (kg CO2)  
  - Amount charged in USD  
  - Verification standard  
  - A thank you note from the Sustainable Travel Team

---

### 6. Send Telegram Notification  
- **Node:** `Send Telegram Notification`  
- **Type:** Telegram Message  
- **To:** User's Telegram chat ID (`userTelegramChatId`)  
- **Message:** Confirmation with offset amount, provider name, and amount charged in USD.

---

## Data Flow

1. User submits travel and payment details via POST request to `/offset-travel` webhook.
2. Calculate Carbon Footprint node calls external API with the travel mode, distance, and date.
3. Emissions data feeds into the function node generating offset recommendations with prices and purchase URLs.
4. Selected offset recommendation triggers Stripe payment with user card details.
5. Upon successful payment, the workflow sends confirmation notifications via email and Telegram.

---

## Webhook Payload Example

```json
{
  "mode": "flight",
  "distance": 1200,
  "date": "2024-07-15",
  "cardNumber": "4242424242424242",
  "expMonth": "12",
  "expYear": "2025",
  "cvc": "123",
  "userEmail": "user@example.com",
  "userTelegramChatId": "123456789"
}
```

---

## Prerequisites & Setup

- **Carbon Footprint API Access:** Ensure API keys or access to `https://api.carbonfootprint.com` if required.
- **Stripe Account:** Setup Stripe credentials for the payment processing node.
- **Email Service:** Configure the email send node with appropriate SMTP or email provider credentials.
- **Telegram Bot:** Create a Telegram bot and obtain the necessary permissions and chat IDs for notifications.
- **Deployment:** Host the workflow on an n8n instance or compatible automation platform.

---

## Summary

This workflow offers an end-to-end sustainable travel carbon offset solution integrating:

- User travel carbon footprint calculation  
- AI-powered offset recommendations  
- Secure payment processing  
- Multi-channel purchase confirmations  

Making carbon offsetting seamless and accessible for eco-conscious travelers.