# AI-Powered Smart Home Energy Optimizer and Carbon Footprint Reducer

This workflow is designed to optimize home energy consumption intelligently by integrating real-time data from energy providers, weather services, and smart home appliances. It analyzes the collected data to generate actionable recommendations, reduce carbon footprint, and control smart devices automatically while notifying the user via email.

---

## Table of Contents
- [Overview](#overview)
- [Workflow Components](#workflow-components)
- [Data Flow](#data-flow)
- [Node Description](#node-description)
- [Setup and Credentials](#setup-and-credentials)
- [Execution Flow](#execution-flow)
- [Customization](#customization)

---

## Overview

This smart home energy optimization workflow gathers real-time energy usage, current weather conditions, and energy pricing to analyze and reduce energy consumption and carbon emissions. The workflow provides tailored recommendations and automatically controls smart appliances based on AI-generated optimization plans.

Key functionalities include:
- Fetching real-time energy consumption data.
- Obtaining current weather information based on home location.
- Retrieving the current energy price.
- Calculating carbon footprint from energy usage.
- Generating actionable insights and recommendations.
- Adjusting smart thermostats and other devices.
- Sending email alerts detailing usage and recommendations.
- Leveraging OpenAI GPT-4 to generate personalized optimization plans.

---

## Workflow Components

### Nodes
1. **Set Home Coordinates**
   - Sets the fixed latitude and longitude of the home for weather queries.

2. **Fetch Real-Time Energy Consumption**
   - Pulls current energy consumption (kWh) from the energy provider API using secure API credentials.

3. **Fetch Weather Data**
   - Retrieves current weather conditions for the home location, including temperature, from the weather API.

4. **Fetch Current Energy Pricing**
   - Obtains the current price per kWh to help factor economic considerations into optimization.

5. **Analyze Data and Generate Recommendations**
   - Processes data inputs to calculate carbon footprint and identify usage patterns.
   - Creates actionable recommendations based on consumption, weather, and pricing.

6. **Generate AI Optimization Plan**
   - Uses OpenAI GPT-4 with consumption, appliance status, weather, pricing, and carbon data to craft a prioritized, AI-driven energy optimization plan.

7. **Control Smart Appliances**
   - Sends commands to IoT-enabled appliances to turn them off or adjust settings as per recommendations.

8. **Adjust Thermostat**
   - Sets the temperature of the smart thermostat based on the AI-generated plan.

9. **Send Email Alerts**
   - Notifies the user by email with current energy stats, carbon footprint, and optimization recommendations.

---

## Data Flow

1. The workflow begins by setting the home coordinates.
2. Coordinates feed into the weather data retrieval node.
3. Real-time energy consumption and current pricing data are fetched from the energy provider API.
4. All gathered data (consumption, pricing, weather) flows into the analysis node.
5. Analysis produces recommendations and carbon footprint calculations.
6. The analyzed data triggers two parallel actions:
   - Send an AI-powered optimization plan request to OpenAI.
   - Send an email alert to the user with current usage and recommendations.
7. The AI-generated plan controls smart home devices:
   - Adjusts the thermostat.
   - Turns off appliances if necessary.

---

## Node Description

### 1. Set Home Coordinates  
- **Type:** Function  
- **Role:** Outputs fixed geographic coordinates (latitude 40.7128, longitude -74.0060).  

### 2. Fetch Real-Time Energy Consumption  
- **Type:** HTTP Request  
- **API:** Energy provider endpoint `/v1/consumption/realtime`  
- **Headers:** Authorization with bearer API key  
- **Output:** Current energy consumption in kWh and appliance status.  

### 3. Fetch Weather Data  
- **Type:** HTTP Request  
- **API:** Weather.com current conditions endpoint  
- **Parameters:** Geocode from coordinates, response format (JSON), language, API key  
- **Output:** Weather data, including outdoor temperature.  

### 4. Fetch Current Energy Pricing  
- **Type:** HTTP Request  
- **API:** Energy provider endpoint `/v1/pricing/current`  
- **Output:** Current price per kWh.  

### 5. Analyze Data and Generate Recommendations  
- **Type:** Function  
- **Logic:**  
  - Calculates carbon footprint using consumption and average CO2 per kWh (0.45 kg CO2/kWh).  
  - Categorizes consumption pattern (High if >5 kWh).  
  - Generates recommendations based on temperature, price, and usage patterns.  

### 6. Generate AI Optimization Plan  
- **Type:** OpenAI (GPT-4)  
- **Prompt:** Includes energy consumption, appliance status, weather, pricing, and carbon footprint.  
- **Output:** Personalized prioritized energy optimization plan for the smart home.  

### 7. Control Smart Appliances  
- **Type:** IoT  
- **Action:** Sends device commands (e.g., turn off appliances) based on AI plan and recommendations.  

### 8. Adjust Thermostat  
- **Type:** IoT  
- **Action:** Sets thermostat temperature according to AI optimization plan.  

### 9. Send Email Alerts  
- **Type:** Email  
- **Recipients:** User email (e.g., user@example.com)  
- **Content:** Current energy consumption, carbon footprint, and key recommendations.  

---

## Setup and Credentials

- **Energy Provider API:**  
  - API URL: `https://api.energyprovider.com`  
  - Requires API key credentials linked to `energyProviderApi`.

- **Weather API:**  
  - API URL: `https://api.weather.com`  
  - Requires API key credentials linked to `weatherApi`.

- **OpenAI API:**  
  - Uses GPT-4 model with credentials labeled `openAiApi`.

- **SMTP Credentials:**  
  - Configured for sending emails (credential identifier: `userEmailSmtp`).

- **Smart Home IoT Devices:**  
  - Device IDs (e.g., `smartThermostat01`) must be configured in IoT platform connected with this workflow.

---

## Execution Flow

1. **Initialize coordinates** →  
2. **Fetch weather data** →  
3. Parallel branch:  
   - **Fetch real-time consumption** →  
   - **Fetch current energy price** →  
4. **Analyze & generate base recommendations** →  
5. Branch into:  
   - **Trigger OpenAI for an advanced optimization plan** →  
     - Adjust thermostat  
     - Control smart appliances  
   - **Send email alert to user**  

---

## Customization

- **Coordinates:**  
  Modify the `Set Home Coordinates` node to your home's latitude and longitude.

- **Email Recipients:**  
  Change email address in the `Send Email Alerts` node to your desired recipient.

- **Energy Price Thresholds & Consumption Levels:**  
  Tune thresholds in `Analyze Data and Generate Recommendations` function node as needed.

- **Appliance Control Logic:**  
  Extend or modify commands in IoT nodes to support additional devices or actions.

- **AI Prompt:**  
  Enhance or tailor the OpenAI prompt to yield different styles of optimization plans.

---

This workflow empowers users to dynamically optimize their home energy consumption, save costs, and reduce environmental impact through AI-driven insights and automated smart device control.