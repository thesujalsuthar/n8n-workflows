# AI-Powered Daily Digest of Personalized Health and Wellness Tips

This workflow automates the creation and delivery of a personalized daily digest containing curated health and wellness tips. It fetches data from multiple health-related sources, aggregates the information, uses AI to generate a tailored summary based on the user’s profile, and sends the final digest via email.

---

## Workflow Overview

### 1. Fetch Nutrition Tips  
- **Node Name:** Fetch Nutrition Tips  
- **Type:** HTTP Request  
- **Description:** Retrieves daily nutrition tips in JSON format from the Nutrition API endpoint (`https://nutrition-api.example.com/daily-tips`).

### 2. Fetch Exercise Tips  
- **Node Name:** Fetch Exercise Tips  
- **Type:** HTTP Request  
- **Description:** Retrieves daily exercise tips in JSON format from the Exercise API endpoint (`https://exercise-api.example.com/daily-tips`).

### 3. Fetch Mental Health RSS  
- **Node Name:** Fetch Mental Health RSS  
- **Type:** HTTP Request  
- **Description:** Fetches a mental health RSS feed in XML format from the RSS URL (`https://mentalhealth-rss.example.com/feed`).

### 4. Fetch General Wellness Tips  
- **Node Name:** Fetch General Wellness Tips  
- **Type:** HTTP Request  
- **Description:** Retrieves daily general wellness tips in JSON format from the General Wellness API endpoint (`https://generalwellness-api.example.com/daily-tips`).

---

### 5. Set Mental Health XML Content  
- **Node Name:** Set Mental Health XML Content  
- **Type:** Set  
- **Description:** Stores the raw XML content fetched from the Mental Health RSS feed into a new JSON property (`rssContent`) for further processing.

### 6. Parse Mental Health RSS XML  
- **Node Name:** Parse Mental Health RSS XML  
- **Type:** XML to JSON  
- **Description:** Converts the `rssContent` XML data into JSON format for easier manipulation and integration with other tips.

---

### 7. Aggregate All Tips  
- **Node Name:** Aggregate All Tips  
- **Type:** Merge (Append mode)  
- **Description:** Aggregates tips from all sources — Nutrition, Exercise, Mental Health (RSS items), and General Wellness — into a single list for AI consumption.

---

### 8. AI Curate & Summarize Tips  
- **Node Name:** AI Curate & Summarize Tips  
- **Type:** OpenAI (GPT-4)  
- **Description:**  
Using the aggregated tips and the user profile provided in the input data, this node prompts GPT-4 to generate a concise, actionable, and engaging personalized daily digest summarizing the health and wellness tips tailored specifically for the user.  

- **Prompt Details:**  
  - Model: GPT-4  
  - Temperature: 0.7  
  - Max Tokens: 800  
  - Content includes user profile context and all aggregated tips.

---

### 9. Send Digest Email  
- **Node Name:** Send Digest Email  
- **Type:** Email Send  
- **Description:** Sends the curated daily digest email to the user’s email address extracted from their profile.  
- **Email Details:**  
  - From: no-reply@healthdigest.ai  
  - To: User’s email (dynamic from user profile)  
  - Subject: "Your AI-Powered Daily Health & Wellness Digest"  
  - Body: The AI-generated summary of health and wellness tips.

---

## Data Flow Summary

1. Multiple HTTP requests fetch daily health and wellness tips from various sources (Nutrition, Exercise, Mental Health RSS, and General Wellness).
2. The Mental Health RSS XML feed is set and parsed into JSON format.
3. All tips are combined into one aggregated list.
4. The combined tips and the user profile are passed to the AI model (GPT-4) to generate a personalized and easily digestible summary.
5. The final digest is emailed to the user automatically.

---

## Usage Notes

- The workflow expects a `userProfile` JSON object containing relevant user details, including the email address.
- APIs used must return data in the specified formats (JSON for most, XML for the RSS feed).
- The OpenAI API key/configuration must be set up for the GPT-4 node to function.
- Ensure email credentials/configuration are correctly set up to enable sending emails.

---

## Summary

This workflow seamlessly integrates multiple data sources with advanced AI capabilities to deliver personalized health and wellness content via email, empowering users to stay informed and motivated daily.