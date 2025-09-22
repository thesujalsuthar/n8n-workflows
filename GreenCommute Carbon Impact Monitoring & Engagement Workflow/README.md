# GreenCommute Carbon Savings & Impact Tracker

## Overview

The **GreenCommute Carbon Savings & Impact Tracker** workflow automates the process of tracking daily and weekly carbon savings based on users' commuting habits. It fetches commute logs, calculates total distance traveled by various eco-friendly modes, estimates carbon emissions saved compared to car use, stores the summaries, and generates insightful reports and suggestions to encourage greener commuting.

---

## Workflow Components

### 1. Daily Trigger  
- **Trigger time:** Every day at 9:00 AM  
- Purpose: Initiates the daily data fetching and processing.

### 2. Fetch Today Commute Logs  
- **Operation:** Executes an SQL query against the PostgreSQL database.  
- **Query:**  
  ```sql
  SELECT * FROM commute_logs WHERE DATE(timestamp) = CURDATE();
  ```  
- Purpose: Retrieves all commute records logged for the current date.

### 3. Calculate Total Distance By Mode  
- **Function:** Aggregates total kilometers traveled per mode: `bike`, `walk`, and `transit`.  
- Filters commute logs and sums distances for each green mode.

### 4. Calculate Carbon Savings  
- **Emission factors (kg CO2e per km):**  
  - Bike: 0 (carbon neutral)  
  - Walk: 0 (carbon neutral)  
  - Transit: 0.089 (average bus/train)  
  - Car (for comparison): 0.271  
- Computes:  
  - Total emissions from logged green commutes  
  - Estimated emissions if car was used for same distance  
  - Net emissions saved by choosing green commute modes

### 5. Store Daily Summary  
- Inserts summarized data into the `carbon_impact_summaries` table with the following columns:  
  - `date`  
  - `total_distance_bike`  
  - `total_distance_walk`  
  - `total_distance_transit`  
  - `emissions_actual_kg`  
  - `emissions_car_kg`  
  - `emissions_saved_kg`

---

### 6. Weekly Report Trigger  
- **Trigger time:** Every Monday at 10:00 AM  
- Purpose: Begins the weekly reporting process.

### 7. Fetch Last 7 Days Summaries  
- **Query:**  
  ```sql
  SELECT date, total_distance_bike, total_distance_walk, total_distance_transit, emissions_actual_kg, emissions_car_kg, emissions_saved_kg
  FROM carbon_impact_summaries
  WHERE date >= current_date - interval '7 days';
  ```  
- Purpose: Retrieves carbon impact summaries from the last 7 days.

### 8. Compile Weekly Report  
- Aggregates totals over the past week:  
  - Distance by mode  
  - Actual emissions  
  - Car emissions equivalent  
  - Carbon saved  
- Generates suggestions to improve green commuting habits based on thresholds:
  - Encourage biking if biking distance < 10 km  
  - Encourage walking if walking distance < 5 km  
  - Encourage more public transit use if transit distance < 15 km  
  - Applaud good habits if all thresholds met  
- Formats a textual weekly impact summary report.

### 9. Send Weekly Email Report  
- Sends the compiled weekly report via email.  
- Configured with:  
  - From: `no-reply@greencommute.example`  
  - To: user email (defaults to `user@example.com` if not specified)  
  - Subject: “Your GreenCommute Weekly Carbon Impact Report”  
  - Body: Contains the weekly impact report.

---

### 10. Fetch Recent Commute Logs  
- **Operation:** SQL query fetching commutes from the past 24 hours:  
  ```sql
  SELECT id, mode, distance_km, start_location, end_location, timestamp FROM commute_logs WHERE timestamp >= NOW() - INTERVAL '1 day';
  ```  
- Purpose: Gets recent commute entries for generating daily suggestions.

### 11. Simulate Route & Carbon API  
- Placeholder function simulating an external API call for enhanced routing or carbon footprint estimation.  
- Currently passes commute data through unchanged.

### 12. Generate Daily Suggestions  
- Analyzes recent commute data to produce personalized suggestions:  
  - Add short bike trips if biking < 2 km  
  - Walk for short distances if walking < 1 km  
  - Use public transit more if transit < 5 km  
- Returns daily suggestions array.

### 13. Send Daily Suggestion Notification  
- Sends daily suggestions as a notification using Pushbullet.  
- Displays a default motivational message if no suggestions are present.

---

## Credentials Required

- **Postgres DB Credentials:** To connect and query the PostgreSQL database containing commute logs and impact summaries.  
- **SMTP Credentials:** For sending weekly email reports.  
- **Pushbullet API:** For delivering daily suggestion notifications.

---

## Database Schema Summary

### commute_logs  
Stores individual commute entries with fields like:  
- `id`  
- `mode` (`bike`, `walk`, `transit`, etc.)  
- `distance_km`  
- `start_location`  
- `end_location`  
- `timestamp`

### carbon_impact_summaries  
Stores daily summarized impact data with columns:  
- `date` (date of summary)  
- `total_distance_bike` (km)  
- `total_distance_walk` (km)  
- `total_distance_transit` (km)  
- `emissions_actual_kg` (actual emissions from green commutes)  
- `emissions_car_kg` (emissions if car was used)  
- `emissions_saved_kg` (emissions saved by green commuting)

---

## Scheduling Summary

| Node                  | Trigger Time             | Frequency      |
|-----------------------|-------------------------|----------------|
| Daily Trigger         | 09:00 AM                 | Every day      |
| Weekly Report Trigger | 10:00 AM on Mondays      | Weekly         |

---

## How It Works

1. **Daily Routine:**  
   - At 9 AM, fetch all commute logs from today.  
   - Calculate total distance per green commute mode.  
   - Estimate carbon emissions saved compared to car usage.  
   - Store summarized data in the database.  
   - Fetch recent logs and generate personalized suggestions.  
   - Send daily eco-friendly commuting suggestions via Pushbullet.

2. **Weekly Summary:**  
   - On Mondays at 10 AM, fetch last week’s summaries.  
   - Compile an aggregated report and tailored suggestions.  
   - Email the report to the user.

---

## Summary

This workflow facilitates continuous monitoring and encouragement of environmentally friendly commuting habits by:  
- Tracking user commute modes daily.  
- Calculating and recording carbon savings regularly.  
- Providing actionable insights and motivational feedback.  
- Supporting both daily nudges and comprehensive weekly summaries for ongoing engagement.