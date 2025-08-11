# Eco-Efficient Commute Planner and Carbon Offset Scheduler

This workflow helps users plan eco-friendly commutes, calculate associated carbon emissions savings, send notifications about their environmental impact, and schedule reminders to purchase carbon offsets or participate in sustainable activities. 

---

## Workflow Overview

### Nodes and Their Functions

1. **Commute Input**
   - Initial input node that sets commute parameters, including:
     - `origin`: Starting location of the commute
     - `destination`: Ending location of the commute
     - `mode`: Preferred primary commute mode (default: public_transit)
     - `distances`: Distances (in kilometers) traveled for various modes:
       - `public_transit`
       - `biking`
       - `walking`
       - `ride_sharing`
   - This node serves as the input data source for the entire workflow.

2. **Fetch Transit Schedules**
   - Makes an HTTP GET request to the Transit App API endpoint `https://api.transitapp.com/v3/schedules`.
   - Query parameters:
     - `origin`: From the input node
     - `destination`: From the input node
     - `mode`: From the input node
   - Retrieves available transit schedules and route information based on user input.
   
3. **Calculate Carbon Emissions**
   - A Function node calculating carbon emissions and savings from switching commute modes relative to single-occupancy car travel.
   - Uses hardcoded average emission factors (kg CO2 per kilometer):
     - Single occupancy car: **0.271**
     - Public transit: **0.089**
     - Biking: **0**
     - Walking: **0**
     - Ride sharing: **0.136**
   - For each travel mode, it calculates:
     - Total emissions (`distance * emissionFactor`)
     - Carbon saved compared to single-occupancy car emissions.
   - Outputs an array of results with mode, distance, emissions, and saved carbon values.

4. **Send Notification**
   - Sends a push notification using Pushbullet API.
   - Notification content:
     - Title: "Eco Commute Reminder"
     - Message: Communicates the amount of CO2 saved (rounded to two decimals) from the eco-efficient commute.
     - Encourages the user to purchase carbon offsets or participate in eco-conscious activities.
   - Requires configured Pushbullet credentials.

5. **Schedule Offset Reminder**
   - Creates a calendar event on the user's primary Google Calendar.
   - Event details:
     - Summary: "Purchase Carbon Offsets or Join Eco-friendly Activity"
     - Description: Reminder to offset emissions or join sustainable community efforts.
     - Start time: 24 hours from the workflow execution moment.
     - End time: 25 hours from the workflow execution moment.
   - Requires Google Calendar OAuth2 credentials.

---

## Workflow Connections

- The workflow starts with **Commute Input**, passing data to **Fetch Transit Schedules**.
- Data from fetched transit info then feeds into **Calculate Carbon Emissions**.
- Calculated emissions trigger two parallel actions:
  - **Send Notification** (Pushbullet)
  - **Schedule Offset Reminder** (Google Calendar event)

---

## Prerequisites

- Active account and API credentials for:
  - Transit App API (or access to endpoint: `https://api.transitapp.com/v3/schedules`)
  - Pushbullet API (for push notifications)
  - Google Calendar API (for event scheduling)
- n8n workflow automation tool configured with necessary credentials.

---

## How to Use

1. Configure the **Commute Input** node with your commute details:
   - Origin, destination addresses (or IDs as required by the transit API).
   - Preferred mode of transport.
   - Distances (in kilometers) traveled by different modes.

2. Ensure credentials for Pushbullet and Google Calendar are added to the workflow.

3. Execute the workflow manually or trigger it automatically using an external event or scheduler.

4. Receive a notification summarizing your carbon savings after your commute.

5. Get a calendar reminder the next day to purchase carbon offsets or join sustainability initiatives.

---

## Emission Factors Reference

| Mode                   | Emission Factor (kg CO2/km) |
|------------------------|-----------------------------|
| Single occupancy car   | 0.271                       |
| Public transit        | 0.089                       |
| Biking                | 0                           |
| Walking               | 0                           |
| Ride sharing          | 0.136                       |

---

## Summary

This workflow empowers users to make environmentally conscious commuting choices by:

- Providing up-to-date transit schedules.
- Quantifying carbon emission reductions.
- Encouraging actionable follow-ups via reminders and notifications.

It integrates transit data, carbon calculations, and user engagement seamlessly to foster sustainable daily travel habits.