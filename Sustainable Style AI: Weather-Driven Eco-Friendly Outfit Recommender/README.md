# AI-Powered Sustainable Fashion Recommender Workflow

This workflow generates sustainable fashion outfit recommendations based on the user's city, personal clothing preferences, and current weather conditions fetched from OpenWeatherMap. It filters sustainable clothing brands’ items to create a personalized outfit suitable for the weather and user preferences.

---

## Workflow Overview

The workflow consists of four primary nodes connected sequentially:

1. **Set User Input**  
2. **Get Weather**  
3. **Get Sustainable Brands**  
4. **Generate Outfit Recommendation**

---

## Nodes Description

### 1. Set User Input

- **Type:** Function  
- **Purpose:** Initializes and sets user input data including the city and clothing preferences (color and category).  
- **Default Input:**
  ```json
  {
    "city": "San Francisco",
    "preferences": {
      "color": "green",
      "category": "top"
    }
  }
  ```
- **Code Logic:** Returns an object containing the city and preferences, which will be used in subsequent nodes.

---

### 2. Get Weather

- **Type:** HTTP Request  
- **Purpose:** Retrieves the current weather data for the provided city using the OpenWeatherMap API.  
- **Endpoint:**  
  `https://api.openweathermap.org/data/2.5/weather`  
- **Query Parameters:**
  - `q`: city (from user input)  
  - `units`: metric (to get temperature in Celsius)  
  - `appid`: OpenWeatherMap API key (provided via credentials)  
- **Authentication:** Header-based authentication referencing the stored OpenWeatherMap API credentials.  
- **Response Format:** JSON  
- **Output:** JSON object containing detailed weather information (e.g., weather type, temperature).

---

### 3. Get Sustainable Brands

- **Type:** Function  
- **Purpose:** Supplies a hardcoded list of sustainable clothing brands along with their product items.  
- **Data Provided:**  
  - **Brands:** EcoWear, GreenThreads, SustainStyle  
  - **Items:** Each brand includes items with properties: name, category, color, and features such as "lightweight," "warm," "waterproof," etc.  
- **Output:** JSON object containing an array of sustainable brands with their clothing items.

---

### 4. Generate Outfit Recommendation

- **Type:** Function  
- **Purpose:** Creates a personalized outfit recommendation by filtering sustainable brand items based on weather and user preferences.  
- **Logic Details:**

  - **Input Sources:**
    - User preferences (color, category)  
    - Current weather main condition (e.g., rain, clear)  
    - Current temperature in Celsius  
    - Sustainable brands and their items  

  - **Filtering Steps:**
    1. **Filter by Preferences:**  
       - Match item color with user's preferred color (if specified).  
       - Match item category with preferred category (if specified).

    2. **Filter by Weather:**  
       - If weather includes "rain," select items with features "waterproof" or "rainproof."  
       - If temperature < 15°C, select items marked as "warm" or category "outerwear."  
       - If temperature > 25°C, select "lightweight" items or those in category "top" or "bottom."  
       - Otherwise, no restrictions applied.
  
  - **Aggregation:**  
    - Flattens all items from all brands into a single list.  
    - Applies filters sequentially.  
    - Groups filtered items by category (e.g., top, bottom, outerwear) to form an outfit recommendation.  
    - Each recommended item includes its name, brand, and features.

- **Output:** JSON object containing the grouped outfit recommendation.

---

## Credentials

- **OpenWeatherMap API:**  
  - The node "Get Weather" uses a credential named `OpenWeatherMap API` to securely provide the API key required for authentication.

---

## Connections Flow

```
Set User Input --> Get Weather --> Get Sustainable Brands --> Generate Outfit Recommendation
```

- User input feeds into weather fetching.  
- Weather data triggers fetching sustainable brands.  
- Brands and weather data feed into the outfit recommendation logic.

---

## Usage

1. **Configure Credentials:**  
   Add your OpenWeatherMap API key in the workflow credentials under `OpenWeatherMap API`.

2. **Set User Preferences:**  
   Customize the city and preferences in the "Set User Input" node if desired.

3. **Run Workflow:**  
   Trigger the workflow to receive the outfit recommendations based on real-time weather and sustainable brand items.

4. **Output:**  
   The generated outfit recommendation provides categorized clothing suggestions for the user tailored to both weather conditions and personal preferences.

---

## Example Output

```json
{
  "outfit": {
    "top": [
      {
        "name": "Organic Cotton T-Shirt",
        "brand": "EcoWear",
        "features": ["lightweight", "breathable"]
      }
    ],
    "outerwear": [
      {
        "name": "Recycled Polyester Jacket",
        "brand": "EcoWear",
        "features": ["warm", "waterproof"]
      }
    ],
    "bottom": [
      {
        "name": "Hemp Jeans",
        "brand": "GreenThreads",
        "features": ["durable"]
      }
    ]
  }
}
```

---

## Summary

This workflow leverages live weather data and curated sustainable fashion items to recommend personalized outfits that optimize comfort, style, and sustainability based on the user's location and preferences.