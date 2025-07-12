# Automated Climate Impact Tracker and Report Generator

## Overview

This workflow automates the process of tracking climate impact metrics and generating a detailed report. It fetches carbon emissions and temperature change data from climate-related APIs, aggregates and analyzes the data by year, creates a comprehensive markdown report, and emails it to relevant stakeholders. The process is scheduled to run automatically every Monday at 9:00 AM.

---

## Workflow Components

### 1. Scheduled Trigger  
- **Type:** Schedule Trigger  
- **Schedule:** Every Monday at 9:00 AM  
- **Purpose:** Initiates the workflow weekly to keep the report up to date.

### 2. Get Carbon Emissions Data  
- **Type:** HTTP Request  
- **Method:** GET  
- **Endpoint:** `https://api.climateapi.example.com/v1/carbon-emissions`  
- **Response Format:** JSON  
- **Purpose:** Retrieves raw carbon emissions data, expected to include yearly emission data.

### 3. Get Temperature Changes Data  
- **Type:** HTTP Request  
- **Method:** GET  
- **Endpoint:** `https://api.climateapi.example.com/v1/temperature-changes`  
- **Response Format:** JSON  
- **Purpose:** Retrieves temperature change data per year.

### 4. Aggregate and Analyze Data  
- **Type:** Function Function  
- **Inputs:** Outputs of both carbon emissions and temperature changes requests  
- **Functionality:**  
  - Aggregates carbon emissions and temperature changes by year.  
  - Sums total carbon emissions per year.  
  - Calculates the average temperature change if multiple records exist for the same year.  
- **Output:** Cleaned, aggregated dataset with `year`, `carbonEmissions`, and `temperatureChange`.

### 5. Generate Report  
- **Type:** Function Function  
- **Input:** Aggregated, analyzed data from the previous step  
- **Functionality:**  
  - Creates a markdown-formatted climate impact report.  
  - Report includes a table with columns: Year, Carbon Emissions (in MtCO2e), and Temperature Change (in °C).  
  - Adds a footer note stating the report was generated automatically.  
- **Output:** A JSON object containing the full markdown report text.

### 6. Send Email  
- **Type:** Email Send  
- **From:** no-reply@climate-tracker.example.com  
- **To:** stakeholders@company.com  
- **Subject:** Automated Climate Impact Report  
- **Body:**  
  - Contains the markdown report in both plain text and HTML (wrapped in `<pre>` tags for formatting).  
- **Purpose:** Delivers the report to defined stakeholders for review or further action.

---

## Data Flow Summary

1. The **Scheduled Trigger** fires every Monday at 9:00 AM.
2. Concurrently fetches **Carbon Emissions** and **Temperature Changes** data from APIs.
3. Both datasets flow into **Aggregate and Analyze Data**, where they are combined and analyzed by year.
4. The aggregated data is sent to **Generate Report**, which formats the information into a markdown report.
5. Finally, the report is emailed out through the **Send Email** node.

---

## Report Format Example

```
# Climate Impact Report

Year | Carbon Emissions (MtCO2e) | Temperature Change (°C)
--- | --- | ---
2020 | 1234.56 | 1.02
2021 | 1278.90 | 1.05
2022 | 1300.00 | 1.10

*Generated automatically by the Automated Climate Impact Tracker.*
```

---

## Prerequisites & Configuration

- Access to the climate data API (`https://api.climateapi.example.com`) with necessary permissions.
- Email service configured in n8n with valid SMTP details for sending the report.
- Correct email addresses set for sender (`no-reply@climate-tracker.example.com`) and recipients (`stakeholders@company.com`).
- n8n instance scheduled to run at the specified trigger time.

---

## Customization

- Adjust API endpoints or parameters in the HTTP Request nodes if API details change.
- Modify scheduling settings in **Scheduled Trigger** to change report frequency or timing.
- Update recipient email list in the **Send Email** node to include additional stakeholders.

---

This automation streamlines the process of monitoring climate data trends and communicating key insights without manual intervention.