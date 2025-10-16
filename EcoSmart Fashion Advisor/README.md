# AI-Powered Sustainable Fashion Outfit Recommender

This workflow provides personalized sustainable fashion outfit recommendations based on user preferences, current weather, and occasion. It leverages an eco-friendly clothing database and an AI model to generate engaging and tailored recommendation messages.

---

## Workflow Nodes

### 1. User Input
- **Type:** Manual Trigger
- **Description:** Captures user inputs as a list of objects with the following options:
  - `preferences`: Object containing user preferences (e.g., preferred clothing types).
  - `weather`: String describing the current weather (e.g., warm, cool, rainy).
  - `occasion`: String describing the occasion (e.g., casual, formal).
- **Purpose:** Starts the workflow with user-specified input parameters.

---

### 2. Clothing Items Database
- **Type:** Function
- **Description:** Defines a static list of clothing items with the following attributes:
  - `id`: Unique identifier
  - `name`: Name of the clothing item
  - `ecoScore`: Eco-friendliness score (scale 1-10)
  - `suitableWeather`: Array of weather types suitable for the item
  - `occasions`: Array of suitable occasions
  - `type`: Clothing category (e.g., top, bottom, outerwear, dress)
- **Purpose:** Serves as the data source for sustainable clothing options.

---

### 3. Filter and Rank Outfits
- **Type:** Function
- **Logic:**
  - Extracts user preferences, weather, and occasion from the inputs.
  - Filters clothing items based on suitable weather.
  - Further filters based on the occasion.
  - Sorts filtered items by highest `ecoScore`.
  - Applies an optional filter by user preferred clothing types.
  - Limits the final list to top 5 recommendations.
- **Purpose:** Generates a ranked list of tailored sustainable outfit options.

---

### 4. Generate Recommendation Text
- **Type:** OpenAI (GPT-4)
- **Parameters:**
  - Model: `gpt-4`
  - Temperature: `0.7`
  - Max Tokens: `512`
- **Prompt:**
  - System role: Assistant generates personalized, friendly, and engaging recommendations focused on sustainability.
  - User role: Provides filtered outfit options and asks for a recommendation message highlighting eco-friendliness and suitability considering user preferences, weather, and occasion.
- **Purpose:** Creates a natural language message to present outfit recommendations.

---

### 5. Prepare Output
- **Type:** Function
- **Description:** Compiles the final output containing:
  - User preferences
  - Weather condition
  - Occasion
  - List of recommended outfits
  - Generated recommendation message from the AI
- **Purpose:** Packages all relevant data for downstream use or display.

---

## Connections and Data Flow
1. **User Input** triggers the workflow:
   - Passes user parameters to the *Clothing Items Database* node.
   - Simultaneously routes the user inputs to the *Filter and Rank Outfits* node.
2. *Clothing Items Database* outputs the clothing data.
3. The clothing data and user inputs converge in the *Filter and Rank Outfits* node.
4. Output of filtered outfit recommendations is sent to the *Generate Recommendation Text* node.
5. AI-generated recommendation text is then forwarded to the *Prepare Output* node.
6. The final output contains all user data and friendly recommendations ready for consumption.

---

## Usage
- Trigger the workflow manually.
- Provide user preferences (optional preferred clothing types), current weather, and occasion.
- Receive a curated list of sustainable fashion items tailored to the inputs.
- Get a polished, engaging recommendation message suitable for end-user display or further integration.

---

## Clothing Data Summary

| Name                     | EcoScore | Suitable Weather        | Occasions              | Type       |
|--------------------------|----------|------------------------|------------------------|------------|
| Organic Cotton T-Shirt   | 9        | warm                   | casual                 | top        |
| Recycled Polyester Jacket| 8        | cold, rainy            | casual, outdoor        | outerwear  |
| Hemp Blend Jeans         | 7        | cool, warm             | casual, semi-formal    | bottom     |
| Linen Dress              | 8        | warm                   | formal, semi-formal    | dress      |
| Tencel Shirt             | 9        | cool, warm             | formal, casual         | top        |
| Recycled Wool Sweater    | 8        | cold                   | casual, semi-formal    | top        |
| Organic Cotton Shorts    | 9        | warm                   | casual                 | bottom     |
| Recycled Nylon Raincoat  | 8        | rainy                  | outdoor                | outerwear  |

---

This workflow ensures sustainable fashion recommendations are not only environmentally conscious but also personalized and contextually appropriate.