# AI-Powered Sustainable Fashion Outfit Recommender

This workflow provides personalized, eco-friendly outfit recommendations by combining user preferences, current fashion trends, and sustainability metrics using AI. Built on the n8n automation platform, it seamlessly integrates multiple data sources and AI to deliver tailored outfit suggestions.

---

## Workflow Overview

1. **Receive User Preferences**  
   An HTTP POST endpoint (`/user-preferences`) accepts user inputs such as style, color preferences, size, occasion, and sustainability interests.

2. **Fetch Current Fashion Trends**  
   The workflow retrieves the latest fashion trends from an external fashion trends API.

3. **Fetch Sustainability Metrics**  
   Sustainability scores for fashion products are fetched from a dedicated sustainability metrics API using API key authentication.

4. **Aggregate Inputs**  
   User preferences, current fashion trends, and sustainability data are combined into a single JSON payload for AI processing.

5. **Generate Outfit Recommendations**  
   The aggregated data is sent to an AI model (OpenAI GPT-4) to generate eco-friendly outfit recommendations tailored to the user’s preferences and the latest trends, including sustainability scores.

6. **Format Recommendations**  
   The AI response is parsed and formatted into a structured JSON object containing the recommendations.

7. **Respond to User**  
   The final recommendations are sent back to the user as the HTTP response.

---

## Nodes Detail

### 1. Receive User Preferences (HTTP Trigger)
- **Type:** HTTP Trigger  
- **Method:** POST  
- **Endpoint:** `/user-preferences`  
- **Purpose:** Entry point to receive user preferences data.

### 2. Fetch Current Fashion Trends (HTTP Request)
- **Type:** HTTP Request  
- **URL:** `https://api.fashiontrends.example.com/v1/trends/current`  
- **Authentication:** None  
- **Purpose:** Pull the latest fashion trends data to incorporate into recommendations.

### 3. Fetch Sustainability Metrics (HTTP Request)
- **Type:** HTTP Request  
- **URL:** `https://api.sustainability-metrics.example.com/v1/products/scores`  
- **Authentication:** Bearer Token via header (`Authorization: Bearer <API_KEY>`)  
- **Purpose:** Retrieve sustainability scores for fashion products relevant to the recommendations.

### 4. Aggregate Inputs (Function)
- **Type:** Function  
- **Purpose:** Combines the user preferences, current fashion trends, and sustainability metrics into a single JSON object for feeding into the AI model.

### 5. Generate Outfit Recommendations (HTTP Request)
- **Type:** HTTP Request  
- **URL:** `https://api.openai.example.com/v1/chat/completions`  
- **Authentication:** Bearer Token (`Authorization: Bearer <API_KEY>`)  
- **Body:** Sends a prompt to GPT-4 including user preferences, fashion trends, and sustainability data requesting personalized outfit recommendations with sustainability scores.  
- **Purpose:** Uses AI to generate customized sustainable outfit recommendations.

### 6. Format Recommendations (Function)
- **Type:** Function  
- **Purpose:** Parses the AI’s response JSON string into a usable object. Handles parsing failures with an error message.

### 7. Respond to User (HTTP Response)
- **Type:** HTTP Response  
- **Purpose:** Sends the formatted outfit recommendation results back to the user who initiated the request.

---

## Setup Instructions

### Prerequisites
- n8n installed and running
- API keys for:
  - Sustainability Metrics API (`sustainabilityApi.apiKey`)
  - OpenAI API (`openAI.apiKey`)

### Configuration
1. Import the workflow into your n8n instance.
2. Add your API keys in the credentials section for:
   - `sustainabilityApi` (Bearer token header)
   - `openAI` (Bearer token header)
3. Deploy the workflow and activate it.

---

## Input Example (POST `/user-preferences`)

```json
{
  "style": "casual",
  "preferredColors": ["green", "blue"],
  "size": "M",
  "occasion": "outdoor",
  "sustainabilityFocus": true
}
```

---

## Output Example

```json
{
  "recommendations": [
    {
      "outfit": "Organic cotton t-shirt with recycled polyester jeans",
      "sustainabilityScore": 9.2,
      "trendFactors": ["2024 spring colors", "eco-friendly fabrics"]
    },
    {
      "outfit": "Hemp fiber jacket with natural dye scarf",
      "sustainabilityScore": 8.8,
      "trendFactors": ["vintage revival", "low-impact dyes"]
    }
  ]
}
```

---

## Notes
- The AI prompt is designed to instruct GPT-4 to balance fashion trends with sustainability principles.
- Ensure APIs used for trends and sustainability metrics are active and accessible.
- Handle errors by checking logs on parsing or API failures in the node executions.

---

Thank you for using the AI-Powered Sustainable Fashion Outfit Recommender!