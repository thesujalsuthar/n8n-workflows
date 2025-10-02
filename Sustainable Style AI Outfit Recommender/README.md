# AI-Powered Sustainable Fashion Outfit Recommender

This workflow provides personalized sustainable fashion outfit recommendations based on user preferences, current fashion trends, and sustainability scores of fashion items. Users receive tailored recommendations via their preferred communication channel—email or chat.

---

## Workflow Overview

### 1. User Input Trigger  
- **Type:** HTTP POST Trigger  
- **Endpoint:** `/user-preferences`  
- **Description:** Accepts user preferences including style, minimum sustainability score, email, chat ID, and communication preference (email or chat). The request payload should be a JSON object containing these fields.

### 2. Fetch Current Fashion Trends  
- **Type:** HTTP Request  
- **API:** `https://api.fashiondata.io/v1/trends/current`  
- **Authentication:** Bearer token (`YOUR_FASHION_DATA_API_KEY`)  
- **Description:** Fetches the latest fashion trends and corresponding fashion items data.

### 3. Fetch Sustainability Scores  
- **Type:** HTTP Request  
- **API:** `https://api.sustainabilitymetrics.com/v1/scores`  
- **Authentication:** Bearer token (`YOUR_SUSTAINABILITY_API_KEY`)  
- **Query Parameters:** Sends a list of item IDs from the fashion trends response.  
- **Description:** Retrieves sustainability scores for each fashion item to evaluate eco-friendliness.

### 4. Generate Recommendations  
- **Type:** Function Node  
- **Description:**  
  - Combines user preferences, current fashion trends, and sustainability scores.  
  - Maps fashion items with their sustainability scores.  
  - Filters items based on user style preference and a minimum sustainability score threshold (default 70 if not specified).  
  - Sorts recommended items descending by sustainability score.  
  - Selects top 5 recommendations to return.

### 5. Split Based on Communication Preference  
- **Type:** Switch Node  
- **Description:** Routes the workflow based on the user’s communication preference (`email` or `chat`).

### 6a. Send Recommendation Email  
- **Type:** Email Send  
- **From:** `no-reply@sustainablefashion.ai`  
- **To:** User’s email address (from input JSON)  
- **Subject:** “Your Sustainable Fashion Outfit Recommendations”  
- **Content:** Personalized email listing the recommended sustainable fashion outfits with their scores, greeting the user by name.

### 6b. Send Recommendations via Chat  
- **Type:** Telegram Send  
- **Chat ID:** User’s chat ID (from input JSON)  
- **Content:** Sends a chat message listing sustainable fashion outfit recommendations with scores, addressing the user by name.

---

## Input JSON Structure (POST `/user-preferences`)

```json
{
  "name": "User's Name",
  "email": "user@example.com",
  "chatId": "123456789",
  "style": "casual",
  "minSustainabilityScore": 80,
  "communicationPreference": "email"   // values: "email" or "chat"
}
```

- **name:** User’s full name for personalized messages.  
- **email:** Email address if communication preference is email.  
- **chatId:** Telegram chat ID if communication preference is chat.  
- **style:** Preferred fashion style to filter recommendations (optional).  
- **minSustainabilityScore:** Minimum sustainability score to consider recommended items (defaults to 70).  
- **communicationPreference:** Preferred delivery channel ("email" or "chat").

---

## Setup and Configuration

1. **API Keys**  
   - Obtain API keys for:  
     - Fashion Trends API (`https://api.fashiondata.io`)  
     - Sustainability Metrics API (`https://api.sustainabilitymetrics.com`)  
   - Insert your keys in the respective HTTP Request node headers (`Authorization: Bearer YOUR_API_KEY`).

2. **Email Settings**  
   - Configure email credentials in the Email Send node as per your SMTP provider requirements (currently set to send from `no-reply@sustainablefashion.ai`).

3. **Telegram Bot**  
   - Setup Telegram Bot API access and provide bot token in the appropriate credential area within n8n for the Telegram node.  
   - Collect user chat IDs to send tailored recommendations.

4. **Deploy Workflow**  
   - Import the workflow JSON into n8n.  
   - Activate the workflow and ensure the HTTP POST endpoint is reachable.

---

## How It Works

- User submits preferences via POST to `/user-preferences`.
- Workflow concurrently fetches latest fashion trends and sustainability scores.
- Combines data and applies user filters to generate personalized sustainable outfit recommendations.
- Routes recommendations for delivery based on user’s preferred communication channel.
- Sends recommendations via email or Telegram chat message.

---

## Example Recommendations in Message

```
- Eco-Friendly Denim Jacket (Sustainability Score: 92)
- Organic Cotton T-Shirt (Sustainability Score: 89)
- Recycled Material Sneakers (Sustainability Score: 85)
- Hemp Blend Pants (Sustainability Score: 83)
- Bamboo Fiber Dress (Sustainability Score: 80)
```

---

## Notes

- Ensure the API keys are kept secure and not exposed publicly.  
- The recommended minimum sustainability score threshold can be adjusted to fit user preferences.  
- Telegram chat messages offer a quick, conversational way to deliver recommendations.  
- Email format is suitable for detailed communication and record keeping.

---

Thank you for using the **AI-Powered Sustainable Fashion Outfit Recommender**! Stay stylish and eco-conscious. 🌿