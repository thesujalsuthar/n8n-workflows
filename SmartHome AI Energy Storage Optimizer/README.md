# AI-Powered Smart Home Energy Storage Optimization and Renewable Integration

This workflow leverages real-time data streams and AI-driven logic to optimize energy storage usage in a smart home environment. It integrates renewable energy forecasts, real-time consumption data, and dynamic electricity pricing to make intelligent battery charge/discharge decisions aimed at cost savings and environmental benefits.

---

## Workflow Overview

The workflow performs the following key functions:

1. **Fetch Renewable Generation Forecast**: Retrieves solar power forecast data from the National Weather Service.
2. **Retrieve Real-Time Energy Consumption**: Collects current home energy consumption data from a local API.
3. **Get Dynamic Electricity Pricing**: Obtains up-to-date electricity price information from a local API.
4. **Optimize Battery Usage**: Applies AI-based decision logic to determine the most cost-effective and sustainable battery charging or discharging actions.
5. **Notify User**: Sends a push notification summarizing the recommended battery action and important metrics.
6. **Generate Report**: Creates a timestamped text report with detailed optimization results.
7. **Email Daily Report**: Sends the generated report via email to the user for monitoring and record-keeping.

---

## Nodes Description

### 1. Renewable Generation Forecast (`httpRequest`)

- **Type:** HTTP Request
- **Purpose:** Fetch solar power forecast data from [NWS Digital Forecast](https://forecast.weather.gov/MapClick.php).
- **URL:**  
  `https://forecast.weather.gov/MapClick.php?lat=40.7128&lon=-74.0060&FcstType=digitalJSON`
- **Position:** (250, 300)

### 2. Real-Time Energy Consumption (`httpRequest`)

- **Type:** HTTP Request
- **Purpose:** Retrieve current energy consumption in kilowatts (kW) from local smart home API.
- **URL:** `http://localhost:8080/api/energy/consumption/realtime`
- **Authentication:** Header-based (configured within the node)
- **Position:** (250, 100)

### 3. Dynamic Electricity Pricing (`httpRequest`)

- **Type:** HTTP Request
- **Purpose:** Access the latest electricity price per kW from a local API.
- **URL:** `http://localhost:8080/api/energy/pricing/dynamic`
- **Authentication:** Header-based (configured within the node)
- **Position:** (250, 500)

### 4. Optimize Battery Usage (`function`)

- **Type:** Function Node (custom JavaScript)
- **Purpose:** Based on inputs from the forecast, real-time consumption, and pricing nodes, decides optimal battery action (charge, discharge, or idle).
- **Logic:**
  - If electricity price > $0.20/kW and there is consumption, discharge battery to reduce grid usage (up to 5 kW).
  - If price < $0.10/kW and good solar forecast exists, charge battery using excess renewable energy (up to 5 kW).
  - Otherwise, remain idle.
- **Outputs:**  
  - `action` — Battery command (`charge`, `discharge`, `idle`)  
  - `batteryChangeKW` — Power to charge or discharge  
  - `consumption` — Current consumption in kW  
  - `forecastSolar` — Solar forecast in kW  
  - `price` — Current electricity price per kW  
- **Position:** (550, 300)
- **Notes:** Includes AI heuristic decision logic within the code for easy customization.

### 5. Send User Notification (`pushbullet`)

- **Type:** Pushbullet Notification
- **Purpose:** Sends an immediate notification detailing the battery action and related data.
- **Title:** "Home Energy Storage Update"
- **Message Content:**  
  ```
  Battery action: {{action}}, Change: {{batteryChangeKW}} kW
  Consumption: {{consumption}} kW
  Solar Forecast: {{forecastSolar}} kW
  Price: ${{price}} per kW
  ```
- **Position:** (850, 250)

### 6. Generate Report File (`writeBinaryFile`)

- **Type:** Write Binary File
- **Purpose:** Saves a timestamped plain text report summarizing battery action and key metrics.
- **Filename Pattern:** `energy_optimization_report_YYYY_MM_DD_HH_mm.txt`
- **Encoding:** UTF-8
- **Position:** (850, 400)

### 7. Email Daily Report (`emailSend`)

- **Type:** Email Send
- **Purpose:** Sends the generated report as an email attachment to the user.
- **From:** `smart-home@energyopt.example.com`
- **To:** `user@example.com`
- **Subject:** "Daily Energy Storage Optimization Report"
- **Email Body:**  
  Includes daily battery action, battery power change, consumption, solar forecast, and electricity price to help monitor optimization performance.
- **Attachment:** The generated report file, named `energy_optimization_report_YYYY_MM_DD.txt`
- **Position:** (1100, 400)

---

## Workflow Connections

- **Data Flow:**
  - The three HTTP request nodes (`Real-Time Energy Consumption`, `Renewable Generation Forecast`, `Dynamic Electricity Pricing`) feed their outputs into the `Optimize Battery Usage` function node as separate inputs.
  - The output of `Optimize Battery Usage` triggers both the `Send User Notification` and `Generate Report File` nodes.
  - Once the report file is generated, it is sent via the `Email Daily Report` node.

---

## How it Works

1. The workflow begins by simultaneously fetching:
   - Current energy consumption,
   - Renewable solar power forecast,
   - Dynamic electricity prices.
2. These inputs are passed into the decision logic function, which calculates whether to charge or discharge the battery or remain idle, based on cost and renewable availability.
3. The optimization decision is sent immediately as a notification to the user’s mobile device.
4. Meanwhile, a detailed report is saved locally and emailed daily to aid in monitoring energy storage optimization performance.

---

## Prerequisites

- Local APIs serving real-time consumption and dynamic pricing at:
  - `http://localhost:8080/api/energy/consumption/realtime`
  - `http://localhost:8080/api/energy/pricing/dynamic`
- Pushbullet account and API key configured in the Pushbullet node for sending notifications.
- SMTP/email credentials configured in the Email Send node to dispatch daily reports.
- Write permissions to save report files locally.

---

## Customization

- Update the weather forecast URL for different locations by modifying the latitude and longitude in the `Renewable Generation Forecast` node.
- Modify the decision logic in the `Optimize Battery Usage` function node to incorporate more advanced AI algorithms or different optimization criteria.
- Change notification or report email recipients and content as needed.

---

## Summary

This workflow automates smart home energy storage management with AI-powered decision-making by integrating multiple data sources. It delivers actionable insights via notifications and daily reports, enabling cost-effective and environmentally friendly usage of home battery systems combined with renewable energy sources.