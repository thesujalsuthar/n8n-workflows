# AI-Driven Personalized Nutrition and Meal Planning Assistant

## Overview
This workflow provides an AI-driven personalized nutrition and meal planning assistant that takes user dietary preferences, health goals, and allergies as input and outputs a tailored meal plan along with a consolidated shopping list. It integrates a user input API, validates preferences, fetches meal options from a nutritional database, generates a meal plan, creates a shopping list, and returns the result via an API response.

---

## Workflow Components

### 1. User Preferences API (`User Preferences API`)
- **Type:** HTTP Request Trigger (POST)
- **Endpoint:** `/user-preferences`
- **Description:** Entry point for users to submit their dietary preferences, health goals, and allergies in JSON format.
- **Expected Input Example:**
```json
{
  "dietaryPreferences": ["vegetarian", "gluten-free"],
  "healthGoals": ["weight loss", "high protein"],
  "allergies": ["peanuts", "shellfish"]
}
```

---

### 2. Validate Preferences (`Validate Preferences`)
- **Type:** Function Node
- **Description:** Validates that the user input contains all required fields: `dietaryPreferences`, `healthGoals`, and `allergies`.
- **Behavior:** 
  - Throws an error if any required field is missing.
  - Passes validated preferences to the next node.

---

### 3. Get Meal Options from Nutritional Database (`Get Meal Options from Nutritional Database`)
- **Type:** Airtable Node
- **Description:** Retrieves all meal records categorized under "meals" from the Airtable nutritional database.
- **Requirements:** Airtable API credentials are required (`<AIRTABLE_CREDENTIAL_ID>`).
- **Data Fetched:**
  - Meals’ name
  - Diet tags (e.g., vegetarian, vegan, gluten-free)
  - Ingredients list
  - Nutritional information (calories, macros)

---

### 4. Generate Meal Plan (`Generate Meal Plan`)
- **Type:** Function Node
- **Description:** Filters and composes a personalized meal plan based on user preferences and allergies.
- **Filtering Logic:**
  - Excludes meals containing allergens.
  - Includes meals that match all dietary preferences.
  - (Optional) Health goal filters can be integrated (calorie count, macro ratios).
- **Meal Plan Output:**
  - Three meals: Breakfast, Lunch, Dinner
  - Each meal includes name, ingredients, and nutritional info.

---

### 5. Create Shopping List (`Create Shopping List`)
- **Type:** Function Node
- **Description:** Aggregates ingredients from the meal plan into a consolidated shopping list, summing quantities for repeated items.
- **Output:**
  - Shopping list with ingredient names, total quantities, and units.
  - Includes meal plan details for reference.

---

### 6. Output Meal Plan API (`Output Meal Plan API`)
- **Type:** HTTP Response Node
- **Endpoint:** `/send-meal-plan`
- **Description:** Returns the generated meal plan and the shopping list as a JSON response to the client.

---

## Execution Flow
1. Receive user preferences via POST request to `/user-preferences`.
2. Validate input data.
3. Fetch meal options from the Airtable nutritional database.
4. Generate a personalized meal plan filtering per preferences and allergies.
5. Create a consolidated shopping list from the meals’ ingredients.
6. Respond to the client with the final meal plan and shopping list via the `/send-meal-plan` endpoint.

---

## Setup and Configuration

- **Airtable Integration:**
  - Create an Airtable base with meals categorized properly.
  - Include fields: `name`, `dietTags`, `ingredients` (with name, quantity, unit), `nutrition`.
  - Set up credentials in n8n with the Airtable API key and base ID.
  - Replace `<AIRTABLE_CREDENTIAL_ID>` with your actual credential ID.

- **API Endpoints:**
  - `/user-preferences`: Accepts user input POST requests.
  - `/send-meal-plan`: Outputs the meal plan with shopping list in the response.

---

## Input Schema

```json
{
  "dietaryPreferences": ["string"],
  "healthGoals": ["string"],
  "allergies": ["string"]
}
```

- `dietaryPreferences`: Array of diet types (e.g., vegetarian, keto).
- `healthGoals`: Array of health goals (e.g., weight loss, muscle gain).
- `allergies`: Array of allergens to avoid (e.g., peanuts, gluten).

---

## Output Schema

```json
{
  "mealPlan": [
    {
      "mealType": "Breakfast" | "Lunch" | "Dinner",
      "name": "string",
      "ingredients": [
        {
          "name": "string",
          "quantity": number,
          "unit": "string"
        }
      ],
      "nutrition": {
        // Nutritional information per meal
      }
    }
  ],
  "shoppingList": [
    {
      "name": "string",
      "quantity": number,
      "unit": "string"
    }
  ]
}
```

---

## Error Handling
- Missing or invalid input fields result in an error response.
- If no suitable meals are found, the meal plan returned may be empty.
- Airtable API errors must be handled outside or logged appropriately.

---

## Notes
- This workflow can be extended to include AI-driven personalized recommendations or integration with other nutritional APIs.
- Health goal filtering logic can be enhanced with calorie and macronutrient calculation.
- The shopping list assumes consistent units for the same ingredient.

---

## License
This project is provided as-is for customization and extension according to use case needs.