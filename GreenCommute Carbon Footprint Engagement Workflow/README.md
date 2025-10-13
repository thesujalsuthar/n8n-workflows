# GreenCommute Carbon Impact Monitoring & Engagement Workflow

## Overview
The **GreenCommute Carbon Impact Monitoring & Engagement Workflow** is designed to track and analyze users' commuting habits in real-time, calculate the corresponding carbon emissions, and engage users with personalized tips and social media sharing features. This workflow facilitates promoting greener commuting choices through notifications and social media outreach.

---

## Workflow Nodes & Functionality

### 1. Real-Time Transit Data (HTTP Request)
- **Type:** HTTP Request
- **Purpose:** Fetches real-time transit data from an external API endpoint: `https://api.transitdata.com/realtime`.
- **Response Format:** JSON
- **Details:** This node retrieves users' commute data such as travel modes and distances traveled.

---

### 2. Calculate Carbon Emissions (Function)
- **Type:** Function
- **Purpose:** Calculates total carbon emissions based on the transit data.
- **Logic:**
  - Expects an array of trip objects with structure: `{mode: string, distance_km: number}`.
  - Uses predefined emission factors in kg CO2e per kilometer for different transport modes:
    - Car: 0.21
    - Bus: 0.10
    - Train: 0.06
    - Bike: 0.00
    - Walk: 0.00
    - Scooter: 0.02
  - Defaults to 0.2 kg CO2e/km if transit mode is unknown.
  - Outputs total emissions rounded to two decimals along with the original trip data.

---

### 3. Generate Personalized Tips (Function)
- **Type:** Function
- **Purpose:** Provides personalized sustainability tips based on total daily carbon emissions.
- **Logic:**
  - Uses emission thresholds to select appropriate tips:
    - ≤ 0.5 kg: "Great job! Walking or biking reduces your carbon footprint."
    - ≤ 2 kg: "Try using public transit or carpooling for some trips."
    - ≤ 100 kg: "Consider shifting to electric or hybrid vehicles."
  - Outputs a tips string to be used in notifications.

---

### 4. Send Impact Notification (Push Notification)
- **Type:** Push Notification
- **Purpose:** Sends users a daily summary of their commuting carbon footprint along with personalized tips.
- **Channel:** `user-notifications`
- **Content:**
  - Title: "GreenCommute Daily Impact Summary"
  - Text: `"Your daily commute generated {{ totalEmissionsKg }} kg CO2 emissions. Consider these tips: {{ tips }}"`
- **Note:** Receives total emissions and tips dynamically from previous function nodes.

---

### 5. Generate Social Share Text (Function)
- **Type:** Function
- **Purpose:** Creates a shareable text message encouraging users to promote green commuting on social media.
- **Content:**  
  `"I just saved {{ totalEmissionsKg }} kg CO2 by choosing green commute options today! Join me in reducing our carbon footprint. #GreenCommute"`
- **Output:** Share text passed to social media posting node.

---

### 6. Post on Social Media (Social Media Post)
- **Type:** Social Media Post
- **Purpose:** Automatically posts the personalized share text to connected social media accounts.
- **Content:** Uses the dynamically generated share text from the previous node.

---

## Workflow Connections & Data Flow

1. **Real-Time Transit Data** —> fetches data and passes it to —>
2. **Calculate Carbon Emissions** —> outputs total emissions and trips, sending data to three parallel nodes:
   - **Generate Personalized Tips**
   - **Send Impact Notification**
   - **Generate Social Share Text**

3. **Generate Personalized Tips** —> sends tips to —> **Send Impact Notification** to enrich notification content.

4. **Generate Social Share Text** —> sends share text to —> **Post on Social Media**, enabling automatic posting.

5. **Send Impact Notification** delivers the final daily impact message to users based on collected data.

---

## Summary
This workflow continuously monitors commuting activities, computes their environmental impact, engages users with actionable tips, and encourages social advocacy for green commuting. The modular design allows easy integration with real-time transit APIs and social media platforms for a seamless user experience promoting sustainability.