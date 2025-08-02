# Eco-Friendly Outfit Recommendation Workflow

This workflow automates the process of generating personalized eco-friendly outfit recommendations based on a user's wardrobe data, sustainability information about materials, and user preferences. It runs daily and tracks outfit usage for continuous improvement.

---

## Workflow Overview

1. **Daily Trigger**  
   Runs once every day at 8:00 AM to start the process automatically.

2. **Get User Wardrobe**  
   Fetches the user's wardrobe items from the Fashion Data API using an authenticated HTTP request. The user ID is dynamically retrieved from the incoming data or defaults to `"defaultUserId"`.

3. **Filter Eco-Friendly Wardrobe Items**  
   Filters wardrobe items by sustainability score, selecting only those with a score **7 or higher** out of 10.

4. **Get Material Sustainability Data**  
   Retrieves detailed sustainability data about the materials of the filtered wardrobe items from the Sustainability DB API, using material IDs.

5. **Attach Material Sustainability Info**  
   Maps the retrieved materials data back to each wardrobe item and attaches detailed sustainability information.

6. **Generate Outfit Recommendations**  
   Creates multiple outfit recommendations based on:
   - Eco-friendliness (items with low environmental impact)
   - Diversity of outfit types (top, bottom, outerwear, footwear, accessory)
   - User preferences such as style and climate (sample preferences are defined within the function)

7. **Track Outfit Usage**  
   Sends the generated outfit recommendations back to the Fashion Data API to track usage and help improve future recommendations.

8. **Format Output**  
   Formats the recommendations into a clean JSON output structure for downstream usage or display.

---

## Node Details

### 1. Daily Trigger  
- Type: Cron Trigger  
- Schedule: Executes daily at 08:00 AM.

### 2. Get User Wardrobe  
- Type: HTTP Request  
- Endpoint: `https://api.fashiondata.example.com/v1/wardrobe`  
- Authentication: Bearer token header using `Fashion API Key` credential  
- Parameter: `userId` dynamically populated  
- Response: JSON containing wardrobe items.

### 3. Filter Eco-Friendly Wardrobe Items  
- Type: Function  
- Logic: Filters wardrobe items with `sustainabilityScore >= 7`.

### 4. Get Material Sustainability Data  
- Type: HTTP Request  
- Endpoint: `https://api.sustainabilitydb.org/v1/materials`  
- Authentication: Header API key using `Sustainability DB Key` credential  
- Query parameters: `materialIds` concatenated from filtered wardrobe items  
- Response: JSON containing detailed sustainability info for materials.

### 5. Attach Material Sustainability Info  
- Type: Function  
- Logic: Matches material sustainability data to each wardrobe item and attaches it to the item record.

### 6. Generate Outfit Recommendations  
- Type: Function  
- Logic:  
  - Filters items with a low environmental impact rating (`impactRating <= 3`).  
  - Groups items by type (e.g., top, bottom, outerwear).  
  - Constructs up to 3 outfits by selecting one item of each type per outfit.  
  - Applies example user preferences (styles, climate).  
  - Returns an array of outfit recommendation objects.

### 7. Track Outfit Usage  
- Type: HTTP Request  
- Endpoint: `https://api.fashiondata.example.com/v1/usage/track`  
- Authentication: Bearer token header using `Fashion API Key` credential  
- Body: Sends `userId` and JSON-stringified outfit data  
- Purpose: Logs outfit usage to help improve recommendation quality.

### 8. Format Output  
- Type: Function  
- Logic: Wraps the output into a `recommendations` JSON object for easy consumption.

---

## Credentials Required

- **Fashion API Key**  
  Used for authentication with the Fashion Data API. Must be set up in n8n credentials.

- **Sustainability DB Key**  
  Used for authentication with the Sustainability Database API. Must be set up in n8n credentials.

---

## Configuration Notes

- **User Identification:**  
  The user ID is dynamically fetched from workflow input (e.g., `userId`, or nested `user.id`), with a fallback to `"defaultUserId"`.

- **Sustainability Thresholds:**  
  - Wardrobe items with a sustainability score below 7 are excluded.  
  - Materials with an environmental impact rating above 3 are deprioritized.

- **Outfit Preferences:**  
  Preferences are currently hardcoded in the function node and can be adjusted for style and climate variations.

---

## How to Use

1. Import this workflow into your n8n instance.
2. Add and configure the required credentials:
   - `Fashion API Key`
   - `Sustainability DB Key`
3. Adjust any hardcoded parameters or user preferences as needed in the function nodes.
4. Activate the workflow to run daily at 8:00 AM.
5. Monitor the output and usage tracking for continued improvement of outfit recommendations.

---

End of documentation.