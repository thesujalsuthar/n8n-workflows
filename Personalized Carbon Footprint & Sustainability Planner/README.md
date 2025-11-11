# AI-Driven Personalized Climate Action Planner

This workflow guides users through an interactive process to estimate their carbon footprint and provides personalized recommendations to reduce their environmental impact. It leverages Telegram for user input and communication, calculating emissions based on energy consumption and car travel, then suggests actionable sustainability goals. Additionally, it sends weekly reminders to encourage ongoing commitment.

---

## Workflow Overview

### 1. Start Node  
- **Type**: Function  
- **Purpose**: Initiates the conversation with a welcome message:  
  *"Welcome! Let's help you set achievable sustainability goals."*  

### 2. Ask Energy Consumption  
- **Type**: Telegram Trigger  
- **Question**:  
  *"What is your estimated average monthly energy consumption in kWh?"*  
- **Variable**: `energyConsumption`  
- **Purpose**: Collects user's monthly energy usage.

### 3. Ask Car Travel  
- **Type**: Telegram Trigger  
- **Question**:  
  *"What is your average monthly travel distance by car in kilometers?"*  
- **Variable**: `carDistance`  
- **Purpose**: Collects user's average car travel distance per month.

### 4. Calculate Carbon Footprint  
- **Type**: Function  
- **Calculations**:  
  - Uses constants for emission factors:  
    - 0.233 kg CO2 per kWh (energy)  
    - 0.192 kg CO2 per km (average gasoline car)  
  - Calculates:  
    - `energyEmission` = `energyConsumption` × 0.233  
    - `carEmission` = `carDistance` × 0.192  
    - `totalEmission` = Sum of energy and car emissions  
- **Output**: Estimated monthly carbon footprint in kg CO2 and a message displaying it.

### 5. Generate Recommendations  
- **Type**: Function  
- **Logic**: Provides tailored recommendations based on `totalEmission`:  
  - **> 100 kg CO2**:  
    - Switch to renewable energy sources at home.  
    - Reduce car travel or switch to electric/hybrid vehicles.  
  - **Between 50 and 100 kg CO2**:  
    - Optimize energy usage by turning off unused appliances.  
    - Plan car trips to reduce distance.  
  - **≤ 50 kg CO2**:  
    - Maintain sustainable habits.  
    - Consider planting trees or supporting carbon offset initiatives.  
- **Output**: List of personalized recommendations and introductory message.

### 6. Set Reminder  
- **Type**: Cron  
- **Schedule**: Weekly at 9 AM every Monday  
- **Purpose**: Sends a reminder message to review sustainability goals and continue tracking carbon footprint.

### 7. Send Message  
- **Type**: Telegram  
- **Function**: Sends the carbon footprint estimation message to the user.

### 8. Send Recommendations  
- **Type**: Telegram  
- **Function**: Sends the list of personalized recommendations formatted with bullet points.

### 9. Send Reminder  
- **Type**: Telegram  
- **Function**: Sends the weekly reminder message to the user.

---

## Connections Flow

1. **Start** → Asks energy consumption question.  
2. **Ask Energy Consumption** → Asks car travel distance question.  
3. **Ask Car Travel** → Calculates carbon footprint.  
4. **Calculate Carbon Footprint** →  
   - Sends carbon footprint message.  
   - Generates personalized recommendations.  
5. **Generate Recommendations** → Sends the recommendations message.  
6. **Set Reminder** (Cron) → Sends weekly reminder message.

---

## Requirements

- **Telegram Bot**: Set up a Telegram bot and integrate with n8n via Telegram Trigger and Telegram nodes. Ensure bot has permissions to send and receive messages.  
- **n8n Platform**: Host and run this workflow on n8n automation platform.

---

## Usage

1. User starts interaction via Telegram chat with the bot.  
2. Bot collects energy consumption and car travel data.  
3. Workflow processes user's input to estimate monthly carbon footprint.  
4. User receives an estimate message and personalized sustainability recommendations.  
5. Weekly reminders keep user engaged to review and maintain sustainable habits.

---

## Customization

- Modify emission factors in the **Calculate Carbon Footprint** node for regional accuracy.  
- Adjust threshold values and recommendations in the **Generate Recommendations** node to better fit user demographics or goals.  
- Change reminder schedule in the **Set Reminder** cron node as needed.

---

## Notes

- The carbon footprint estimate is simplified and intended for personal awareness rather than precise measurement.  
- Recommendations encourage actionable steps that users can adopt gradually.  
- The workflow supports continued engagement with the reminder system.