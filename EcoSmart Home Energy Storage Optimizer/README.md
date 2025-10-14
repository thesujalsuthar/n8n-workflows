# EcoSmart Home Energy Storage Optimizer

## Overview

The **EcoSmart Home Energy Storage Optimizer** workflow is designed to optimize the charging and discharging of a home energy storage system based on real-time data inputs from the smart meter, solar panels, and local energy pricing. The goal is to maximize the use of solar energy, reduce reliance on grid power during expensive periods, and ultimately save on energy costs.

---

## Workflow Nodes Detail

### 1. Fetch Smart Meter Data
- **Type:** HTTP Request
- **Purpose:** Retrieves the home’s energy consumption and current storage level data for the last 1 hour from the smart meter.
- **Configuration:**
  - Resource: `SmartMeter`
  - Operation: `getData`
  - Time Range: `last_1h`

---

### 2. Fetch Solar Panel Output
- **Type:** HTTP Request
- **Purpose:** Obtains the solar energy generation output for the last 1 hour.
- **Configuration:**
  - Resource: `SolarPanel`
  - Operation: `getOutput`
  - Time Range: `last_1h`

---

### 3. Fetch Local Energy Pricing
- **Type:** HTTP Request
- **Purpose:** Fetches current energy prices for the local region used to determine cost-effective strategies.
- **Configuration:**
  - Resource: `EnergyPricing`
  - Operation: `fetchCurrentPrices`
  - Regions: `local`

---

### 4. Optimize Energy Storage Usage
- **Type:** Function
- **Purpose:** Implements the core optimization logic using data collected from previous nodes.
- **Logic Summary:**
  - **Inputs:** smart meter data (usage, storage level), solar panel output, and local energy price.
  - **Rules:**
    - Charge battery if solar generation is high and energy price is above a high-price threshold.
    - Discharge battery if home usage is high, price is high, and storage has available charge.
    - Avoid battery use during low-price grid periods.
  - **Parameters:**
    - Storage Capacity: 10 kWh (example)
    - High Price Threshold: $0.20 per kWh
    - Low Price Threshold: $0.10 per kWh
  - **Outputs:**
    - Optimal `chargeStorage` and `dischargeStorage` values (in kWh), along with relevant input data snapshot and timestamp.

---

### 5. Send Optimization Alert
- **Type:** Email Send
- **Purpose:** Sends an email notification summarizing the optimization results.
- **Recipients:** homeowner@example.com
- **Email Content:**
  - Timestamp of execution
  - Charge and discharge recommendations (kWh)
  - Current energy price
  - Solar generation and home usage levels
  - Battery storage status

---

### 6. Calculate Cost Savings
- **Type:** Function
- **Purpose:** Calculates estimated cost savings from optimized battery discharge.
- **Calculation:**
  - Estimated savings = discharged energy (kWh) × current energy price ($/kWh)
- **Outputs:**
  - Timestamp
  - Charge and discharge values (kWh)
  - Current price per kWh
  - Estimated cost savings in dollars

---

### 7. Append to Usage Report
- **Type:** Write Binary File
- **Purpose:** Appends the optimization results and cost savings to a CSV report.
- **File:** `EcoSmart_Energy_Usage_Report.csv`
- **Content:**
  - Timestamp
  - Charge storage amount (kWh)
  - Discharge storage amount (kWh)
  - Current price per kWh
  - Estimated cost savings ($)

---

## Workflow Data Flow

1. **Input Data Fetch:**
   - Smart meter, solar panel output, and energy pricing data are independently fetched from their respective sources.
   
2. **Optimization:**
   - The gathered data feeds into the optimization function node, which determines the best charge/discharge actions based on current conditions and thresholds.
   
3. **Results Dissemination:**
   - Optimization results trigger an email notification to the homeowner.
   - Simultaneously, the cost savings calculation processes these results.
   
4. **Reporting:**
   - Cost savings and summary data are appended to a local CSV report, maintaining an ongoing log of system performance over time.

---

## Configuration Notes

- **Email Node:** Update the `toEmail` address to the intended recipient.
- **Storage Capacity and Thresholds:** Customizable within the optimization function code to suit specific home/storage settings.
- **Data Sources:** API endpoints or integrations providing the Smart Meter, Solar Panel, and Energy Pricing data must be configured separately to allow HTTP request fulfillment.

---

## Summary

This workflow facilitates smart, dynamic management of home energy storage to reduce energy costs by leveraging solar production and time-based pricing signals. Automated email alerts and CSV log/report generation support transparency and ongoing performance tracking.