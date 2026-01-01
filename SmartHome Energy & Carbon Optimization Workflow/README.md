# SmartHome AI Energy Optimizer & Carbon Footprint Reducer

This n8n workflow intelligently optimizes your smart home energy usage by adjusting thermostat settings and other devices based on real-time weather data and energy prices. It aims to reduce energy consumption and lower your carbon footprint, while providing alerts and daily reports through Telegram.

---

## Workflow Overview

The workflow runs every 5 minutes and performs the following steps:

1. **Fetch current energy pricing data** from an energy price API for your specific region.
2. **Fetch current weather data** (temperature) for your city using the OpenWeatherMap API.
3. **Calculate the optimal thermostat temperature** based on energy prices and outdoor temperature.
4. **Set thermostat temperature** accordingly using your smart home platform.
5. **Optimize additional smart devices** based on energy price signals.
6. **Log optimization data** locally to a file.
7. **Check for high energy prices** and send immediate Telegram alerts if detected.
8. **Send daily Telegram reports** summarizing the energy use and estimated carbon footprint reduction.

---

## Nodes Description

### 1. Trigger Every 5 Minutes
- A scheduled trigger node that starts the workflow every 300 seconds (5 minutes).

### 2. Fetch Energy Pricing
- HTTP GET request to fetch current energy prices from `https://api.energyprice.com/v1/current`.
- Requires:
  - `region` (your region code)
  - `apikey` (your energy price API key)
- Outputs realtime electricity cost per kWh.

### 3. Fetch Weather Data
- HTTP GET request to OpenWeatherMap API: `https://api.openweathermap.org/data/2.5/weather`
- Requires:
  - `q` (your city name)
  - `appid` (your OpenWeatherMap API key)
  - `units` set to `metric` for °C.
- Retrieves current outdoor temperature.

### 4. Calculate Optimal Temperature
- Function node that uses the inputs:
  - Outdoor temperature (`outdoorTemp`)
  - Current energy price (`energyPrice`)
- Logic:
  - Base temperature: 21°C
  - If energy price > 0.30 $/kWh:
    - Lower target heating temperature to 19°C if cold outside (< 15°C)
    - Increase cooling threshold to 25°C if warm outside (> 25°C)
  - If energy price < 0.15 $/kWh: set target to comfortable 21°C
  - Otherwise, set target to 20°C
- Outputs target thermostat temperature, outdoor temperature, and energy price.

### 5. Set Thermostat Temperature
- Sends the calculated `targetTemperature` to your smart thermostat device using the smart home API.
- Requires:
  - `deviceId` of your thermostat
  - Smart home API credentials

### 6. Optimize Additional Devices
- Sends an `optimizeEnergyUsage` command to other registered smart home devices.
- Parameter used: current `energyPrice`
- Helps reduce overall energy usage during peak price periods.

### 7. Log Optimization Data
- Appends current optimization data (timestamp, outdoor temp, energy price, target temp) to a local text file `energy-optimization-log.txt`.

### 8. Check High Energy Price (IF Node)
- Checks if current energy price exceeds 0.30 $/kWh.
- If true, triggers alerts.

### 9. Send Alert (Telegram Node)
- Sends an immediate alert to your Telegram chat notifying high energy prices and optimizations being applied.
- Requires:
  - Telegram API credentials
  - Chat ID

### 10. Send Daily Report (Telegram Node)
- Sends a daily summary of:
  - Outdoor temperature
  - Energy price
  - Target thermostat temperature
  - Estimated carbon footprint reduction (calculated as `(21 - targetTemperature) * 0.5` kg CO2)
- Requires Telegram API credentials and Chat ID.

---

## Configuration Instructions

Before activation, configure the following placeholders with your actual data:

| Placeholder                      | Description                                   |
|---------------------------------|-----------------------------------------------|
| `YOUR_CITY`                     | Your city for weather data lookup             |
| `YOUR_OPENWEATHERMAP_API_KEY`  | API key for OpenWeatherMap                     |
| `YOUR_REGION_CODE`              | Region code for energy price API               |
| `YOUR_ENERGYPRICE_API_KEY`      | API key for energy price service               |
| `YOUR_THERMOSTAT_DEVICE_ID`    | Device ID of your smart thermostat             |
| `YOUR_SMART_DEVICE_ID`          | Device ID for other smart home devices         |
| `YOUR_SMARTHOME_CREDENTIAL_ID` | Credentials for your smart home API            |
| `YOUR_TELEGRAM_CREDENTIAL_ID`  | Telegram API credentials                        |
| `YOUR_CHAT_ID`                 | Telegram chat ID to send alerts and reports   |

---

## Dependencies

- **OpenWeatherMap API** - To get real-time weather data.
- **EnergyPrice API** (or similar) - To fetch current electricity prices.
- **Smart Home API** compatible with n8n smartHome node - To control thermostat and other devices.
- **Telegram Bot API** - For notifications and reports.

---

## Usage Notes

- Ensure API keys have sufficient permissions.
- Update device IDs and credentials.
- Adjust the temperature thresholds in the function node if necessary to better reflect your comfort and energy goals.
- The workflow logs every optimization cycle for future analysis.
- You can modify the alert thresholds or the frequency of the trigger node to suit your preferences.

---

## License

This workflow is open for personal and commercial use. Attribution appreciated.

---

By leveraging intelligent, data-driven automation, this workflow helps lower your smart home's energy costs and carbon emissions efficiently and conveniently.