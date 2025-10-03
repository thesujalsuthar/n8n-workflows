# AI-Powered Smart Home Energy Storage Optimizer

This workflow automates the optimization of a smart home's energy storage system by dynamically balancing energy consumption, solar production, and storage levels. It integrates real-time data, performs optimization logic, controls the storage system, evaluates cost savings, and notifies the home administrator.

---

## Workflow Overview

### Nodes and Functionality

1. **Get Real-Time Energy Consumption**
   - Fetches current energy consumption data from the smart home system.
   - API Endpoint: `https://api.smarthome.local/devices/energy_consumption`
   - Response format: JSON

2. **Get Solar Panel Production**
   - Retrieves current solar panel energy production.
   - API Endpoint: `https://api.smarthome.local/devices/solar_panels/production`
   - Response format: JSON

3. **Get Energy Storage Status**
   - Gets the current storage status, including charge level and capacity.
   - API Endpoint: `https://api.smarthome.local/devices/energy_storage/status`
   - Response format: JSON

4. **Optimize Energy Usage** (Function Node)
   - Inputs data from the three previous nodes.
   - Logic:
     - Compares solar production vs consumption.
     - If production > consumption and storage is not full (below 95%), it calculates optimal charging power.
     - If consumption > production and storage is not empty (above 5%), calculates optimal discharging power.
     - Outputs an action: `"charge"`, `"discharge"`, or `"hold"` with corresponding charge/discharge power (kW).

5. **Control Energy Storage System**
   - Sends a POST request to update the energy storage system based on the optimization action.
   - API Endpoint: `https://api.smarthome.local/devices/energy_storage/control`
   - Request Body (JSON):
     ```json
     {
       "action": "<charge|discharge|hold>",
       "charge_kw": <number>,
       "discharge_kw": <number>
     }
     ```
   - Headers: `Content-Type: application/json`

6. **Get Real-Time Energy Prices**
   - Retrieves current energy prices to evaluate cost savings.
   - API Endpoint: `https://api.utilityprovider.com/energy/prices/realtime`
   - Response format: JSON

7. **Evaluate Cost Savings** (Function Node)
   - Inputs data from optimization node and real-time prices node.
   - Logic:
     - If charging and current price is lower than average, estimates cost savings from charging.
     - If discharging and current price is higher than average, estimates cost savings from discharging.
   - Outputs action, power values, and estimated cost savings (in currency).

8. **Notify Smart Home Admin**
   - Sends a summary message to the designated Telegram chat ID.
   - Notification content:
     ```
     Energy Storage Optimization Summary:
     Action: <action>
     Charge Power: <chargeKw> kW
     Discharge Power: <dischargeKw> kW
     Estimated Cost Saving: $<estimatedCostSaving>
     ```
   - Telegram node configured with `chatId` of the admin.

---

## Connections Flow

- The first three data acquisition nodes feed into **Optimize Energy Usage**.
- **Optimize Energy Usage** sends commands to **Control Energy Storage System** and data to **Evaluate Cost Savings**.
- **Get Real-Time Energy Prices** sends pricing data to **Evaluate Cost Savings**.
- **Evaluate Cost Savings** sends summarized details to **Notify Smart Home Admin**.

---

## How to Use

1. Configure API endpoints and authentication if necessary for:
   - Energy consumption
   - Solar production
   - Energy storage status
   - Energy prices
   - Energy storage control system

2. Update the Telegram node:
   - Replace `YOUR_SMART_HOME_ADMIN_CHAT_ID` with the actual Telegram chat ID to receive notifications.

3. Deploy and activate the workflow to enable continuous energy optimization.

---

## Optimization Details

- The optimization ensures energy storage is charged when surplus solar power is available.
- Discharges storage only when consumption surpasses production and storage levels permit.
- Cost evaluation adds a financial layer to recommend charging during low price periods and discharging during high price periods, maximizing savings.
  
---

## Summary

This n8n workflow seamlessly integrates energy data streams, optimizes smart home energy storage utilization, and notifies the administrator — enabling efficient and cost-effective energy management in real-time.