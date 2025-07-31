# AI-Powered Sustainable Fashion Outfit Recommender Workflow

This n8n workflow provides personalized sustainable outfit recommendations by processing user preferences, generating AI-driven styling advice, and fetching eco-friendly products matching the recommendation.

---

## Workflow Overview

1. **Receive User Preferences**  
   An HTTP POST endpoint `/user-preferences` accepts user styling preferences as JSON.  
   **Expected input format:**  
   ```json
   {
     "stylePreferences": ["casual", "boho", ...],
     "preferredColors": ["blue", "green", ...],
     "occasions": ["work", "weekend", ...],
     "climate": "temperate",
     "size": "M",
     "budget": 150
   }
   ```  
   Responds immediately with acknowledgment and echoes back the received preferences.

2. **Prepare AI Prompt**  
   A Function node transforms the incoming preferences into a detailed prompt designed for an AI model. It instructs the AI to provide a personalized sustainable outfit recommendation, including eco-friendly tips and highlighting sustainable materials.

3. **Get AI Styling Recommendation**  
   Sends the prompt to OpenAI’s GPT-4 API (chat completion endpoint) using a POST request, authenticated via API key stored in n8n credentials.

4. **Parse AI Recommendation**  
   Extracts the AI’s response text containing the outfit recommendation.

5. **Prepare Product Search Query**  
   Constructs a search query string for sustainable fashion products based on the user’s style preferences, colors, and size from the initial input.

6. **Fetch Sustainable Products**  
   Queries an external Sustainable Fashion Database API using the prepared search query. The request filters to return only currently available, eco-friendly products and requires an API key credential.

7. **Combine Recommendations and Products**  
   Merges the AI styling recommendation with the list of sustainable products fetched, preparing the final combined output.

8. **Respond to User**  
   Returns a JSON response containing both the personalized AI outfit recommendation and matching eco-friendly products.

---

## Detailed Node Descriptions

### 1. Receive User Preferences  
- **Type:** HTTP Trigger  
- **HTTP Method:** POST  
- **Endpoint path:** `/user-preferences`  
- **Response:**  
  ```json
  {
    "success": true,
    "message": "Preferences received",
    "preferences": {...}
  }
  ```

### 2. Prepare AI Prompt  
- **Type:** Function  
- **Function:** Creates a prompt string including:  
  - Styles  
  - Occasions  
  - Preferred colors  
  - Climate  
  - Size  
  - Budget  
  - Request for eco-friendly tips and sustainable materials mention

### 3. Get AI Styling Recommendation  
- **Type:** HTTP Request  
- **API:** OpenAI Chat Completions (`https://api.openai.com/v1/chat/completions`)  
- **Model:** GPT-4  
- **Headers:** Authorization (API key), Content-Type (application/json)  
- **Request Body:** Uses the prompt from previous node, sets tokens and temperature

### 4. Parse AI Recommendation  
- **Type:** Function  
- **Function:** Extracts recommendation text from OpenAI’s response JSON.

### 5. Prepare Product Search Query  
- **Type:** Function  
- **Function:** Constructs a search query string for sustainable fashion based on:  
  - Style preferences  
  - Preferred colors  
  - Size

### 6. Fetch Sustainable Products  
- **Type:** HTTP Request  
- **API:** Sustainable Fashion DB (`https://api.sustainablefashiondb.com/v1/products`)  
- **Query Parameters:**  
  - `q` = search query from previous node  
  - `available`= true  
  - `eco_friendly`= true  
- **Headers:** Authorization (API key)

### 7. Combine Recommendations and Products  
- **Type:** Function  
- **Function:** Combines AI-generated recommendation text with eco-friendly product data.

### 8. Respond to User  
- **Type:** HTTP Response  
- **Status Code:** 200  
- **Body:** Outputs the combined JSON with:  
  - `outfitRecommendation`: AI’s styling suggestions  
  - `ecoFriendlyProducts`: List of sustainable fashion products

---

## Credentials Required

- **OpenAI API** (API key for GPT-4 chat completions)  
- **Sustainable Fashion DB API** (API key for product database queries)

---

## How to Use

1. Deploy this workflow to your n8n instance.
2. Ensure credentials for OpenAI and Sustainable Fashion DB are configured.
3. Send user preferences as JSON to the HTTP POST endpoint at `/user-preferences`.
4. Receive a response with a personalized sustainable outfit recommendation alongside curated eco-friendly product options.

---

## Input Example

```json
{
  "stylePreferences": ["modern", "minimalist"],
  "preferredColors": ["white", "beige"],
  "occasions": ["office", "casual"],
  "climate": "mild",
  "size": "L",
  "budget": 200
}
```

---

## Output Example

```json
{
  "outfitRecommendation": "We recommend a minimalist white organic cotton shirt paired with beige recycled fiber trousers, perfect for mild climates and office wear. Look out for certifications like GOTS and Fair Trade to ensure sustainability.",
  "ecoFriendlyProducts": [
    {
      "id": "123",
      "name": "Organic Cotton Shirt",
      "brand": "EcoWear",
      "price": 75,
      "color": "white",
      "size": "L",
      "eco_certifications": ["GOTS"]
    },
    {
      "id": "456",
      "name": "Recycled Fiber Trousers",
      "brand": "Green Style",
      "price": 120,
      "color": "beige",
      "size": "L",
      "eco_certifications": ["Fair Trade"]
    }
  ]
}
```

---

## Workflow ID

`ai-powered-sustainable-fashion-outfit-recommender`