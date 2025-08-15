# EcoSmart Waste Management Automation

## Overview
This workflow automates the process of monitoring, analyzing, and optimizing waste collection routes using IoT sensor data, environmental conditions, and local regulations. It fetches real-time data, analyzes bin statuses for compliance and fill levels, prepares optimized routes for collection, sends notifications, and saves detailed reports.

---

## Workflow Nodes

### 1. Fetch IoT Sensor Data
- **Type:** HTTP Request (GET)
- **Purpose:** Retrieves current fill levels and locations of waste bins from the IoT sensors API.
- **Endpoint:** `https://api.sensors.example.com/iot-waste-sensor-data`
- **Response Format:** JSON

---

### 2. Fetch Environmental Data
- **Type:** HTTP Request (GET)
- **Purpose:** Obtains current environmental conditions such as temperature and humidity that may influence waste management.
- **Endpoint:** `https://api.environmentaldata.example.com/current-conditions`
- **Response Format:** JSON

---

### 3. Fetch Local Regulations
- **Type:** HTTP Request (GET)
- **Purpose:** Retrieves local government regulations applicable to waste bin types and their maximum allowed fill levels.
- **Endpoint:** `https://api.localgov.example.com/waste-management-regulations`
- **Response Format:** JSON

---

### 4. Analyze Bin Fill Levels & Compliance
- **Type:** Function
- **Purpose:** Processes data from the sensor, environmental, and regulations APIs to:
  - Map each bin’s ID, location, fill level, temperature, and humidity.
  - Check if each bin complies with regulation rules regarding maximum fill levels.
  - Filter bins that require collection (fill level above 75% and regulation compliant).
- **Outputs:** List of bins that need collection (`binsToCollect`).

---

### 5. Prepare Routing Waypoints
- **Type:** Function
- **Purpose:** Generates waypoints from the filtered bins’ geolocations to prepare data for route optimization.
- **Output:** JSON object containing the waypoints and the bins.

---

### 6. Optimize Collection Route
- **Type:** HTTP Request (POST)
- **Purpose:** Sends the waypoints to an external mapping service to generate an optimized waste collection route.
- **Endpoint:** `https://api.mappingservice.example.com/optimize-route`
- **Payload:** JSON containing waypoints.

---

### 7. Process Optimized Route Response
- **Type:** Function
- **Purpose:** Processes the route optimization API response to extract the optimized route and prepare final data for notification and reporting.
- **Output:** Contains a message, route details, and bins for collection.

---

### 8. Send Notification
- **Type:** Slack message
- **Purpose:** Sends a notification to the Slack channel `waste-management-notifications` with:
  - Number of bins to collect.
  - Summary of the optimized route.
  - Scheduling prompt.
- **Credentials:** Slack API credentials must be configured.

---

### 9. Save Report
- **Type:** Write File
- **Purpose:** Saves a detailed JSON report of the current waste management operation.
- **File Path:** `waste-management-reports/report-YYYY-MM-DD.json`
- **Content:** Generated report including bins to collect, environmental data, and local regulations.

---

### 10. Compile Waste Management Report
- **Type:** Function
- **Purpose:** Compiles a comprehensive report containing:
  - The bins to collect.
  - Environmental data.
  - Local regulations.
  - Current timestamp.
- **Output:** JSON report passed to the Save Report node.

---

## Workflow Flow
1. **Fetch IoT Sensor Data** → 
2. **Fetch Environmental Data** → 
3. **Fetch Local Regulations** → 
4. **Analyze Bin Fill Levels & Compliance** → 
   - Branches into:
     - **Prepare Routing Waypoints** → **Optimize Collection Route** → **Process Optimized Route Response** → 
       - **Send Notification**
       - **Save Report**
     - **Compile Waste Management Report** → connected to **Save Report**

---

## Prerequisites
- API access to:
  - IoT sensor data service
  - Environmental data service
  - Local government regulations service
  - Mapping route optimization service
- Slack workspace and configured Slack API credentials for sending notifications.
- File system access or cloud storage for saving reports.

---

## Summary
This automated workflow enables efficient, data-driven management of waste collection by integrating real-time IoT data, environmental factors, and compliance with local regulations. It optimizes routing to save resources and sends actionable notifications to stakeholders while maintaining detailed historical reports.