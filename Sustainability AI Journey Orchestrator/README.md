# AI-Powered Personalized Sustainability Journey Planner and Habit Optimizer

This workflow leverages AI and database integrations to create and manage personalized sustainability journey plans. It generates actionable sustainability plans, evaluates environmental impact, provides progress monitoring metrics, and tracks community engagement for users.

---

## Workflow Overview

The workflow consists of the following key components:

1. **Generate Sustainability Plan**  
   Calls the OpenAI GPT-4 API to create a personalized sustainability journey plan including tips, habit formation techniques, environmental impact assessments, and suggestions for community engagement.

2. **Parse AI Response**  
   Extracts the generated sustainability plan from the OpenAI API response.

3. **Store Plan in DB**  
   Saves the personalized sustainability plan into the Firebase database under the path `user_habits`.

4. **Generate Progress Metrics**  
   Sends the sustainability plan back to OpenAI GPT-4 to generate measurable progress metrics and environmental impact assessments.

5. **Parse Progress Metrics**  
   Extracts the generated progress metrics and impact assessment from the AI response.

6. **Store Progress Metrics**  
   Saves the progress metrics data into Firebase under `user_progress`.

7. **Fetch Community Engagement**  
   Retrieves user-specific community engagement data via an external API.

8. **Parse Community Data**  
   Parses the retrieved community engagement data.

9. **Store Community Engagement**  
   Saves the community engagement data into Firebase under `user_community_engagement`.

---

## Node Details

### 1. Generate Sustainability Plan  
- **Type:** HTTP Request  
- **API:** OpenAI Chat Completions (GPT-4)  
- **Request:** POST  
- **Payload Highlights:**  
  - System prompt defines AI's role to create personalized sustainability plans.  
  - User prompt requests actionable, habit-based, and impact-driven sustainability plans.  
  - Temperature set to 0.7 for balanced creativity.  
- **Authentication:** Bearer token using `OPENAI_API_KEY` environment variable.

### 2. Parse AI Response  
- **Type:** Function  
- **Function:** Extracts generated plan text from OpenAI's JSON response.

### 3. Store Plan in DB  
- **Type:** Firebase  
- **Path:** `user_habits`  
- **Operation:** Update  
- **Data:** Stores the plan text under property `plan`.  
- **Credential:** Requires Firebase credentials.

### 4. Generate Progress Metrics  
- **Type:** HTTP Request  
- **API:** OpenAI Chat Completions (GPT-4)  
- **Request:** POST  
- **Payload Highlights:**  
  - System prompt frames AI's role to evaluate environmental impact and metrics.  
  - User prompt passes the generated sustainability plan text to generate progress metrics.  
  - Temperature set to 0.7.  
- **Authentication:** Bearer token using `OPENAI_API_KEY`.

### 5. Parse Progress Metrics  
- **Type:** Function  
- **Function:** Extracts progress metrics and impact assessment text from OpenAI's response.

### 6. Store Progress Metrics  
- **Type:** Firebase  
- **Path:** `user_progress`  
- **Operation:** Update  
- **Data:** Stores metrics text under property `metrics`.  
- **Credential:** Firebase credentials required.

### 7. Fetch Community Engagement  
- **Type:** HTTP Request  
- **Request:** GET  
- **URL Template:** `https://community.api.example.org/engagements?user_id={{$json["userId"]}}`  
- **Purpose:** Retrieves community engagement data related to the specified user.

### 8. Parse Community Data  
- **Type:** Function  
- **Function:** Parses the community engagement response JSON.

### 9. Store Community Engagement  
- **Type:** Firebase  
- **Path:** `user_community_engagement`  
- **Operation:** Update  
- **Data:** Stores community engagement data under `engagements`.  
- **Credential:** Requires Firebase credentials.

---

## Data Flow & Connections

- The sustainability plan is generated first (Node 1), parsed (Node 2), then simultaneously stored (Node 3) and sent to generate progress metrics (Node 4).  
- Metrics from Node 4 are parsed (Node 5) and stored (Node 6).  
- Separately, user community engagement data is fetched (Node 7), parsed (Node 8), and stored (Node 9).

---

## Prerequisites

- **OpenAI API Key:** Set as environment variable `OPENAI_API_KEY`.  
- **Firebase Credentials:** Properly configured and authorized for database update operations.  
- **Community Engagement API:** Accessible endpoint at `https://community.api.example.org/engagements` with valid user IDs.

---

## Usage

1. Trigger or schedule the workflow with user context including `userId` and current habits/preferences.  
2. The workflow generates, stores, and updates the sustainability plan, progress metrics, and community engagement data in the database automatically.  
3. Use stored data to provide personalized sustainability coaching, habit monitoring, and community interaction insights.

---

## Notes

- Temperature setting of 0.7 in AI requests balances creativity and factual responses.  
- Firebase paths (`user_habits`, `user_progress`, `user_community_engagement`) can be customized as needed.  
- Community engagement API URL and parameters must be tailored to specific implementations.

---

This workflow streamlines the creation, evaluation, and continuous tracking of personalized sustainability journeys powered by AI and integrated community insights.