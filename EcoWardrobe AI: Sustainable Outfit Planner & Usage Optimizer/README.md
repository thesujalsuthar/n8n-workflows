# Sustainable Style AI: Smart Outfit Planner & Wardrobe Optimizer

This workflow provides an AI-powered solution to recommend eco-friendly outfit choices tailored to the user's preferences, current climate conditions, and wardrobe usage data. It optimizes outfit suggestions by factoring in sustainability, personal style, and weather, while also encouraging balanced usage of wardrobe items.

---

## Workflow Overview

1. **Receive User Preferences**  
   A webhook endpoint listens for POST requests from users containing their style preferences, location, and user ID.

2. **Load User Wardrobe**  
   Retrieves the user's entire wardrobe data from a MongoDB collection, including eco-friendly labels, styles, temperature suitability, and wear counts.

3. **Get Climate Data**  
   Fetches current weather data for the user's specified location using the WeatherAPI service.

4. **Generate Eco-Friendly Outfit Recommendations**  
   Runs a function that filters wardrobe items based on eco-friendliness, user style preferences, and current temperature. It then sorts items to recommend less worn pieces first, returning the top 3 outfit recommendations.

5. **Prepare Usage Optimization Data**  
   Extracts the IDs of the recommended items to prepare data for updating their usage counts.

6. **Update Wardrobe Usage**  
   Upserts the recommended wardrobe items in MongoDB, incrementing their `timesWorn` count to encourage rotation and sustainability.

---

## Nodes Description

### 1. Receive User Preferences (`Webhook`)
- **Type:** Webhook (POST)  
- **Webhook Path:** `/user-preferences`  
- **Purpose:** Receives user JSON payload containing:
  - `userId`: Unique identifier for the user.
  - `location`: Geographic location for weather data.
  - `preferences`: Style preferences array.
  - `wardrobe`: Array of wardrobe items with metadata.

---

### 2. Load User Wardrobe (`MongoDB`)
- **Operation:** Get many documents  
- **Collection:** `wardrobeItems`  
- **Query:** Filters wardrobe items by the user's `userId`.  
- **Purpose:** Loads all wardrobe items for the active user.

---

### 3. Get Climate Data (`HTTP Request`)
- **Method:** GET  
- **URL:** `https://api.weatherapi.com/v1/current.json`  
- **Query Parameters:**  
  - `q`: The user's location (dynamic).  
  - `key`: WeatherAPI key (replace with your own).  
- **Purpose:** Retrieves current temperature and weather conditions for styling context.

---

### 4. Generate Eco-Friendly Outfit Recommendations (`Function`)
- **Function Logic:**  
  - Filters wardrobe items labeled as eco-friendly (`isEcoFriendly`).  
  - Matches items to user style preferences (`styles`).  
  - Filters items suitable for the current temperature based on `minTemp` and `maxTemp` metadata.  
  - Sorts the filtered list by `timesWorn` ascending to prioritize less frequently worn items.  
  - Returns the top 3 recommended outfit items.

---

### 5. Prepare Usage Optimization Data (`Function`)
- **Purpose:**  
  Extracts the recommended item IDs from the previous step to prepare data for updating usage statistics.

---

### 6. Update Wardrobe Usage (`MongoDB`)
- **Operation:** Upsert (update or insert)  
- **Collection:** `wardrobeItems`  
- **Upsert Key:** `id` (item identifier)  
- **Update:** Increments the `timesWorn` field by 1 for each recommended item.  
- **Credentials:** Requires MongoDB connection string and database name.

---

## Input Data Structure Example

User POST payload to webhook `/user-preferences` should be like:

```json
{
  "userId": "user123",
  "location": "San Francisco",
  "preferences": {
    "styles": ["casual", "boho", "minimalist"]
  },
  "wardrobe": [
    {
      "id": "item1",
      "style": "casual",
      "isEcoFriendly": true,
      "minTemp": 10,
      "maxTemp": 25,
      "timesWorn": 3
    },
    {
      "id": "item2",
      "style": "boho",
      "isEcoFriendly": false,
      "timesWorn": 7
    }
    // More items...
  ]
}
```

---

## Configuration & Setup

- Replace `YOUR_WEATHERAPI_KEY` in the **Get Climate Data** node with your valid WeatherAPI key.
- Configure the MongoDB credentials in **Load User Wardrobe** and **Update Wardrobe Usage** nodes with your MongoDB connection string and target database.
- Ensure your MongoDB `wardrobeItems` collection contains documents with wardrobe item metadata, including `id`, `userId`, `style`, `isEcoFriendly`, `timesWorn`, `minTemp`, and `maxTemp` where applicable.

---

## Execution Flow

```plaintext
Receive User Preferences (Webhook) 
    → Load User Wardrobe (MongoDB)
        → Get Climate Data (HTTP Request)
            → Generate Eco-Friendly Outfit Recommendations (Function)
                → Prepare Usage Optimization Data (Function)
                    → Update Wardrobe Usage (MongoDB)
```

---

## Outcome

The workflow returns the top 3 eco-friendly outfit recommendations aligned with user preferences and weather conditions, promoting sustainable wardrobe usage and balanced wear of clothing items.

---

## License

This workflow is open for customization and use under MIT License.