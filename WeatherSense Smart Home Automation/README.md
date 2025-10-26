# AI-Powered Local Weather-Driven Smart Home Automation

This workflow automates your smart home devices based on current local weather conditions fetched from OpenWeatherMap API. It integrates AI-driven analysis to decide optimal settings for your thermostat, lighting, and irrigation system to enhance comfort and energy efficiency.

---

## Workflow Overview

The workflow consists of the following main steps:

1. **Fetch Weather Data**  
   Retrieve current weather data for your specified city from the OpenWeatherMap API.

2. **Analyze Weather Conditions**  
   Extract and process key weather parameters such as temperature, precipitation, and sunlight presence.

3. **Decide Device Settings**  
   Determine thermostat temperature, lighting state, and irrigation control based on analyzed weather data.

4. **Set Thermostat**  
   Adjust the smart thermostat to maintain a comfortable indoor temperature.

5. **Adjust Lighting**  
   Turn lighting ON or OFF according to sunlight availability.

6. **Control Irrigation**  
   Enable or disable irrigation depending on recent precipitation levels.

7. **Notify User**  
   Send a notification summarizing the new settings applied to devices.

---

## Node Details

### 1. Fetch Weather Data

- **Type:** HTTP Request  
- **Purpose:** Queries OpenWeatherMap API to get current weather JSON data.  
- **Parameters:**  
  - `q`: City name (replace `YOUR_CITY_HERE` with your local city)  
  - `appid`: OpenWeatherMap API key (replace `YOUR_API_KEY_HERE`)  
  - `units`: Metric system (°C)  

---

### 2. Analyze Weather Conditions

- **Type:** Function  
- **Purpose:** Parses weather data JSON and extracts:  
  - `temperature` in Celsius  
  - `precipitation` volume in last 1 hour (mm)  
  - `sunlight` as boolean indicating clear sky (`true` if clear)  

---

### 3. Decide Device Settings

- **Type:** Function  
- **Purpose:** Uses analyzed weather info to compute:  
  - **Thermostat Setting:**  
    - Below 18°C → set 22°C  
    - Above 26°C → set 20°C  
    - Otherwise → set 21°C  
  - **Lighting:** ON if no sunlight, OFF if sunny  
  - **Irrigation:** ON if precipitation less than 0.1 mm, OFF otherwise  

---

### 4. Set Thermostat

- **Type:** Smart Home API Integration  
- **Operation:** Set thermostat temperature according to decided setting.  
- **Authentication:** Requires API key via `smartHomeApi` credentials.

---

### 5. Adjust Lighting

- **Type:** Smart Home API Integration  
- **Operation:** Turn lighting ON or OFF based on sunlight condition.  
- **Authentication:** Uses `smartHomeApi` credentials.

---

### 6. Control Irrigation

- **Type:** Smart Home API Integration  
- **Operation:** Activate or deactivate irrigation based on precipitation levels.  
- **Authentication:** Uses `smartHomeApi` credentials.

---

### 7. Notify User

- **Type:** Notification  
- **Purpose:** Summarizes and informs user about the applied device settings:  
  - Thermostat temperature  
  - Lighting state (ON/OFF)  
  - Irrigation state (ON/OFF)

---

## Setup Instructions

1. **OpenWeatherMap API:**  
   - Sign up at [OpenWeatherMap](https://openweathermap.org/api) to get a free API key.  
   - Replace `YOUR_CITY_HERE` with your city name in the `Fetch Weather Data` node.  
   - Replace `YOUR_API_KEY_HERE` with your OpenWeatherMap API key.

2. **Smart Home API Credentials:**  
   - Obtain your smart home system API key and configure `smartHomeApi` credentials in your environment.  
   - Ensure your smart home devices support thermostat, lighting, and irrigation controls via this API.

3. **Run the Workflow:**  
   - Trigger manually or schedule as needed to automate based on current weather conditions.

---

## Notes

- Units for temperature are metric (°C). Modify if needed.  
- Precipitation is measured for the last 1 hour. Adjust timing through API query if necessary.  
- Sunlight is approximated by identifying if weather condition is "clear".  
- Notifications are configured to provide feedback for easier monitoring.

---

## License

This workflow is provided as-is for personal and commercial use. Modify and extend according to your smart home system and preferences.