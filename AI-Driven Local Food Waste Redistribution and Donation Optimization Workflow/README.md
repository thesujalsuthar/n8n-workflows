# AI-Powered Local Food Waste Reduction and Donation Optimization System

This workflow is designed to optimize the process of redistributing surplus food within local communities by leveraging AI to minimize food waste and enhance donation scheduling. It automates data retrieval, processing, route optimization, stakeholder notification, and impact tracking.

---

## Workflow Overview

### 1. Get Surplus Food Data
- **Node Type:** HTTP Request
- **Description:** Fetches all available surplus food data from the `surplus-food-data` endpoint.
- **Output:** Raw data including location, food type, quantity, and pickup windows for surplus food items.

### 2. Aggregate Surplus Food Data
- **Node Type:** Function
- **Description:** Aggregates raw surplus food data by location.
- **Process:** Groups food items by location and structures details including food types, quantities, and pickup windows.
- **Output:** JSON object mapping each location to its list of surplus food items.

### 3. Optimize Redistribution Routes & Schedules
- **Node Type:** OpenAI (GPT-4o-mini)
- **Description:** Uses an AI model to generate optimized pickup and redistribution schedules.
- **Input:** Aggregated surplus food data containing locations, food types, quantities, and pickup windows.
- **Output:** JSON array of route objects, each including:
  - `location`: Pickup location
  - `scheduledPickupTime`: Optimized time for food pickup
  - `foodItems`: List of food items (type and quantity)
  - `assignedDriver`: Driver responsible for the route

### 4. Get Stakeholders
- **Node Type:** CRM System
- **Description:** Retrieves a list of stakeholders (e.g., drivers, donors, recipients) involved in the food redistribution process.
- **Output:** List of users with details including location, role, name, and email.

### 5. Prepare Notifications
- **Node Type:** Function
- **Description:** Cross-references optimized routes with stakeholder data to prepare email notifications.
- **Process:** 
  - Matches stakeholders to routes by location or role (drivers).
  - Constructs personalized email messages detailing pickup schedules and food items.
- **Output:** Notification payloads ready for sending emails.

### 6. Send Notifications
- **Node Type:** Email Send
- **Description:** Sends email notifications to all relevant stakeholders including drivers and location contacts.
- **Input:** Prepared email details (`toEmail`, `subject`, `text`).

### 7. Track Impact Metrics
- **Node Type:** Custom API
- **Description:** Logs key impact metrics for monitoring and reporting.
- **Metrics Tracked:**
  - `totalFoodSaved`: Total quantity of food available for pickup (aggregated from surplus data).
  - `totalRoutesPlanned`: Number of optimized routes planned.
  - `notificationsSent`: Number of notification emails sent.
  - `timestamp`: Timestamp of the workflow execution.
- **Output:** Records created in the impact tracking system.

---

## Data Flow and Connections

- The workflow starts by fetching surplus food data.
- Data is aggregated by location for better processing.
- AI optimizes redistribution routes and assigns drivers.
- Stakeholder info is fetched to prepare for notifications.
- Notifications are generated combining route data and stakeholder info.
- Emails are sent to drivers and location contacts.
- Impact metrics are recorded post notifications.

---

## Usage Notes

- Ensure API endpoints for surplus food data and CRM system are properly configured.
- OpenAI API key and model access (`gpt-4o-mini`) must be set up to enable route optimization.
- Email sending configuration must be set for the Send Notifications node.
- Custom API for tracking impact metrics should accept the defined fields.

---

## Summary

This AI-powered workflow streamlines local food waste reduction by automating surplus food data retrieval, optimizing donation routes, notifying stakeholders, and capturing impact metrics—helping communities efficiently redistribute surplus food and reduce waste.