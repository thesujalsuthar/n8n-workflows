# Personalized AI-Driven Carbon Footprint Reduction Planner

## Overview

This workflow is designed to provide users with a personalized plan to reduce their carbon footprint. It leverages AI to analyze user-provided data regarding transportation usage, energy consumption, and dietary habits. The AI estimates the carbon impact of each category and generates tailored, actionable sustainability tips. A reminder email with the plan is then sent to help users stay on track.

---

## Workflow Details

### 1. Webhook - **Get User Input**

- **Type:** Webhook (HTTP POST)
- **Purpose:** Serves as the entry point for the workflow. Receives user data via a POST request at the endpoint: `/personalized-ai-driven-carbon-footprint-reduction-planner`.
- **Expected Input:**
  - `transportationUsage`: Object detailing transportation habits (e.g., vehicle type, miles traveled).
  - `energyConsumption`: Object with home energy usage data (e.g., electricity, gas).
  - `dietaryHabits`: Object describing diet patterns (e.g., meat consumption, local produce).
  - Optional: `email` for sending the output.

- **Response:** Immediately returns a JSON response containing all received entries.

---

### 2. Function Node - **Extract Inputs**

- **Purpose:** Parses and organizes the user input JSON into three main categories:
  - Transportation
  - Energy
  - Diet

- **Output:** Returns structured JSON with the extracted categories for further processing.

---

### 3. AI Node - **AI Carbon Impact Analysis**

- **Type:** OpenAI GPT-4 Integration
- **Purpose:** Acts as an AI sustainability expert analyzing the received data.
- **System Prompt:** Sets the AI’s role as a sustainability expert tasked with analyzing data and generating personalized recommendations.
- **User Prompt:** Injects the user’s categorized data (transport, energy, diet).
- **AI Tasks:**
  - Estimate carbon impact per category.
  - Generate a personalized sustainability plan with actionable tips and reminders.

- **Temperature:** Set to 0.7 for a balanced creative and factual response.

---

### 4. Function Node - **Parse AI Response**

- **Purpose:** Extracts the text content from the AI response.
- **Output:** Formulates JSON with a property `sustainabilityPlan` containing the AI-generated plan.

---

### 5. Function Node - **Create Daily Reminder**

- **Purpose:** Generates a reminder to review and update the sustainability plan.
- **Reminder Content:** "Review your personalized carbon footprint reduction plan and update your activities."
- **Reminder Date:** Set for 1 day after the workflow execution (ISO string format).

- **Output:** JSON containing the reminder text and date.

---

### 6. Email Node - **Send Sustainability Plan Email**

- **Purpose:** Sends the personalized sustainability plan and reminder to the user via email.
- **Sender:** `no-reply@sustainabilityapp.com`
- **Recipient:** Extracted from the user input (`email` field), defaults to `user@example.com` if none provided.
- **Subject:** "Your Personalized Carbon Footprint Reduction Plan"
- **Body:**
  ```
  Hello,

  Here is your personalized carbon footprint reduction plan:

  {{sustainabilityPlan}}

  Reminder: {{reminder}}

  Stay sustainable!
  ```

---

## How to Use

1. **Send a POST request** to the webhook URL:
   ```
   /personalized-ai-driven-carbon-footprint-reduction-planner
   ```
   with JSON body containing user data structured as:
   ```json
   {
     "transportationUsage": { ... },
     "energyConsumption": { ... },
     "dietaryHabits": { ... },
     "email": "user@example.com"
   }
   ```

2. **Receive a response** confirming input receipt.

3. **Check your email** for a personalized carbon footprint reduction plan and daily reminder.

---

## Notes

- Ensure the workflow is active and configured with valid OpenAI API credentials.
- The email sending node requires SMTP setup.
- Customize the input data shape to align with your application's data collection format.
- The reminder is static currently but can be enhanced to support recurring notifications.

---

End of documentation.