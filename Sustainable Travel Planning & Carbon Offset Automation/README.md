# Sustainable Travel Planning Workflow

This workflow automates the process of planning a sustainable trip by fetching low-carbon transport options, calculating their carbon emissions, finding local carbon offset projects, compiling a summary report, and sending the report via email.

---

## Workflow Overview

1. **Start Trigger**
   - **Type:** Webhook (POST request)
   - **Endpoint Path:** `/start-planning`
   - **Description:** Initiates the workflow when a POST request is received with trip details (origin, destination, date, and email).

2. **Fetch Low-Carbon Transport Options**
   - **Type:** HTTP Request (GET)
   - **API Endpoint:** `https://api.traveldata.example.com/v1/low-carbon-options`
   - **Parameters:**
     - `from` — Trip origin (from webhook payload)
     - `to` — Trip destination (from webhook payload)
     - `date` — Travel date (from webhook payload)
   - **Headers:** Authorization Bearer token (`YOUR_TRAVELDATA_API_KEY`)
   - **Description:** Retrieves a list of sustainable transport options based on the provided trip details.

3. **Split Transport Options**
   - **Type:** Function
   - **Description:** Splits the retrieved transport options array into individual items for separate processing downstream.

4. **Calculate Carbon Emissions**
   - **Type:** HTTP Request (POST)
   - **API Endpoint:** `https://api.carbonfootprint.example.com/v1/calculate`
   - **Body (JSON):**
     - `transportType` — Type of transport (from current option)
     - `distanceKm` — Travel distance in kilometers (from current option)
     - `passengerCount` — Fixed at 1
   - **Description:** Calculates the estimated carbon emissions (in kg CO2e) for each transport option.

5. **Fetch Local Carbon Offset Projects**
   - **Type:** HTTP Request (GET)
   - **API Endpoint:** `https://api.carbonoffsets.example.com/v1/projects`
   - **Query Parameters:**
     - `location` — Destination location (from trip data)
     - `category` — Set to `travel`
   - **Headers:** Authorization Bearer token (`YOUR_CARBONOFFSET_API_KEY`)
   - **Description:** Retrieves recommended carbon offset projects near the travel destination to compensate for emissions.

6. **Compile Summary**
   - **Type:** Function
   - **Description:** Combines the calculated emissions, transport type, and carbon offset project data into a summary report including:
     - Selected transport option
     - Estimated carbon emissions
     - Number of recommended local offset projects
     - Text summary string

7. **Send Email Report**
   - **Type:** Email Send
   - **Parameters:**
     - Recipient Email: Extracted from the webhook payload (`email`)
     - Subject: `Your Sustainable Travel Plan & Carbon Offset Recommendations`
     - Body: Summary text compiled in the previous step
   - **Description:** Sends the summary report to the user’s email address.

---

## Input Data Format

The initial POST request to the webhook `/start-planning` should include a JSON payload with the following fields:

```json
{
  "from": "OriginCity",
  "to": "DestinationCity",
  "date": "YYYY-MM-DD",
  "email": "user@example.com"
}
```

- **from:** Starting location of the trip
- **to:** Destination location of the trip
- **date:** Planned travel date
- **email:** Recipient email address for the report

---

## Required API Keys and Configuration

- `YOUR_TRAVELDATA_API_KEY`: Access key for the Travel Data API providing low-carbon transport options.
- `YOUR_CARBONOFFSET_API_KEY`: Access key for the Carbon Offset Projects API.
  
Make sure to replace these placeholders within the HTTP Request nodes before activating the workflow.

---

## Summary

This workflow streamlines sustainable travel planning by:

- Providing eco-friendly transport options
- Calculating related carbon emissions
- Suggesting local carbon offset projects
- Delivering a concise, actionable email report to the user

It supports informed decision-making to reduce environmental impact while traveling.