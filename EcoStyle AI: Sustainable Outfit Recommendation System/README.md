# Sustainable Fashion Outfit Recommendation Workflow

This workflow automates personalized sustainable fashion outfit suggestions by fetching eco-friendly fashion items from multiple sources, merging and deduplicating the data, generating AI-powered recommendations based on user preferences, and sending the suggestions via email and Telegram.

---

## Workflow Overview

### 1. Scheduled Trigger

- **Node:** `Scheduled Trigger`
- **Purpose:** Triggers the workflow every day at 9:00 AM.
- **Configuration:** Set to execute daily at 09:00 hours.

---

### 2. Get User Preferences

- **Node:** `Get User Preferences`
- **Purpose:** Provides static user preferences for outfit recommendations.
- **Details:** Contains example preferences including email, Telegram chat ID, style, size, preferred colors, and occasion.
  
Example preferences:
```json
{
  "email": "user@example.com",
  "telegramChatId": "",
  "style": "bohemian",
  "size": "M",
  "preferredColors": ["green", "earth tones"],
  "occasion": "casual"
}
```

---

### 3. Fetch Sustainable Fashion Items from APIs

- **Nodes:**  
  - `Fetch Sustainable Fashion Items 1`  
  - `Fetch Sustainable Fashion Items 2`
  
- **Purpose:** Fetch eco-friendly, trending fashion items from two different APIs with specific filters.

- **API Details:**

  1. **Fetch Sustainable Fashion Items 1**  
     - URL: `https://api.sustainablestyle.com/fashion-items`  
     - Query Parameters:  
       - category = outfits  
       - material = eco-friendly  
       - trending = true  
     - Response format: JSON

  2. **Fetch Sustainable Fashion Items 2**  
     - URL: `https://api.greenfashionhub.org/items`  
     - Query Parameters:  
       - filter = eco_materials  
       - sort = popularity_desc  
     - Response format: JSON

---

### 4. Merge & Deduplicate Items

- **Node:** `Merge & Deduplicate Items`
- **Purpose:** Combines the fashion items fetched from both APIs and removes duplicates based on `id` or `name`.
- **Logic:** Merges both item lists into one unique list by checking for duplicate IDs or names.

---

### 5. AI Recommendation Generation

- **Node:** `AI Recommendation`
- **Purpose:** Uses GPT-4 model to generate a personalized sustainable outfit recommendation.
- **Input:**
  - User preferences from "Get User Preferences".
  - Merged and deduplicated list of fashion items.
- **Prompt:**  
  The AI is instructed to act as an assistant specialized in sustainable fashion and generate a nicely formatted outfit suggestion, emphasizing eco-friendly materials, trends, and suitability to the user.

---

### 6. Format Email Content

- **Node:** `Format Email Content`
- **Purpose:** Formats the AI response content into an email subject and body.
- **Details:**  
  - Email subject: "Your Personalized Sustainable Fashion Outfit Suggestion"  
  - Email text: Content from AI recommendation.

---

### 7. Send Communication

- **Nodes:**  
  - `Send Email`  
  - `Send Telegram Message`
  
- **Purpose:** Sends the personalized outfit suggestion via email and Telegram message.
  
- **Email Configuration:**  
  - From: `no-reply@ecofashion.ai`  
  - To: User's email (`preferences.email`)  
  - Subject and Text: from formatted content.
  
- **Telegram Configuration:**  
  - Chat ID: User's Telegram chat ID (`preferences.telegramChatId`)  
  - Message: AI-generated recommendation.

---

## Workflow Execution Order

1. **Scheduled Trigger** activates the workflow daily at 9:00 AM.
2. **Get User Preferences** loads the user-specific data.
3. Two parallel HTTP requests fetch sustainable fashion items from different APIs.
4. Results from both APIs are merged and deduplicated.
5. The AI generates a personalized outfit recommendation based on the merged data and user preferences.
6. The recommendation is formatted into email content.
7. The formatted suggestion is sent to the user via email and Telegram.

---

## Requirements

- n8n instance with:
  - HTTP Request node enabled.
  - OpenAI node configured with access to GPT-4.
  - Email Send node configured with an SMTP server.
  - Telegram node configured with a valid bot token and permissions.
  
- User correspondence info (email and optional Telegram chat ID).

---

## Customization

- Modify **Get User Preferences** to dynamically fetch real user data or integrate with a database.
- Update API endpoints or query parameters to fit specific sources of sustainable fashion.
- Adjust AI prompt in the **AI Recommendation** node to tailor suggestions or tone.
- Configure email sender details and messaging options accordingly.

---

## Notes

- The workflow currently uses static user preferences for demonstration.
- Telegram messaging requires users to have started a conversation with the bot beforehand.
- Ensure API keys and sensitive information are securely handled in your n8n credentials.

---

Thank you for using the Sustainable Fashion Outfit Recommendation Workflow!