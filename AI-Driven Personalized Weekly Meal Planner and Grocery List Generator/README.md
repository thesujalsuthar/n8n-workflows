# AI-Powered Personalized Nutrition Planner and Grocery List Generator

## Overview

This workflow creates a personalized 7-day meal plan based on user dietary preferences, health goals, and restrictions. It also generates a corresponding grocery list extracted from the meal plan. The workflow leverages OpenAI's GPT-4 model to generate the meal plan and uses custom logic to extract grocery items.

---

## Workflow Nodes Description

### 1. Webhook Trigger

- **Purpose:** Receives user input via an HTTP POST request at the `/nutrition-plan` endpoint.
- **Input:** JSON object containing user preferences and dietary information under the field `userPreferences`.
- **Output:** Triggers the generation of the meal plan.

---

### 2. Generate Meal Plan (OpenAI)

- **Type:** OpenAI GPT-4 Completion
- **Operation:** `createCompletion`
- **Model:** `gpt-4`
- **Prompt:**

  ```
  You are an expert nutrition planner. Based on the following user input, generate a personalized 7-day meal plan with breakfast, lunch, dinner, and two snacks each day. Consider dietary preferences, health goals, and restrictions:

  {{ $json.userPreferences }}

  Provide the meal plan in JSON format with fields: day, meals (breakfast, lunch, dinner, snacks).
  ```
- **Parameters:**  
  - Temperature: 0.7  
  - Max Tokens: 1000  
  - TopP: 1  
  - Frequency Penalty: 0  
  - Presence Penalty: 0  

- **Output:** JSON formatted 7-day meal plan containing meals for each day.

---

### 3. Extract Grocery List (Function)

- **Purpose:** Parses the generated meal plan JSON and extracts grocery items for the week.
- **Approach:**  
  - Parses the meal plan JSON from OpenAI's response.  
  - Iterates over each day and meal (breakfast, lunch, dinner).  
  - Uses a simple heuristic to extract ingredients by searching for the word "with" and capturing following ingredients.  
  - Also processes snacks to add them to the grocery list.  
  - Adds unique grocery items to a Set to avoid duplicates.

- **Output:** JSON object containing a `groceryList` array with extracted grocery items.

- **Note:** The ingredient extraction uses a basic heuristic and may be improved for production use.

---

### 4. Respond with Grocery List

- **Purpose:** Sends the grocery list back in the webhook response.
- **Data Sent:**  
  - `groceryList`: Array of grocery items extracted from the meal plan.

---

### 5. Respond with Meal Plan

- **Purpose:** Sends the complete meal plan back in the webhook response.
- **Data Sent:**  
  - `mealPlan`: The full 7-day meal plan in JSON format.

---

## Workflow Execution Flow

1. **User POSTs** their preferences to `/nutrition-plan`.
2. The webhook triggers the workflow.
3. The **Generate Meal Plan** node sends the preferences to OpenAI GPT-4, requesting a tailored 7-day meal plan in JSON.
4. The **Extract Grocery List** node reads the meal plan JSON and extracts grocery items for shopping.
5. Both the **Respond with Grocery List** and **Respond with Meal Plan** nodes send the generated data back to the user as the HTTP response.

---

## Input Example

```json
{
  "userPreferences": {
    "diet": "vegetarian",
    "caloriesPerDay": 2000,
    "restrictions": ["gluten-free", "nut allergy"],
    "healthGoals": ["weight loss", "improved energy"]
  }
}
```

---

## Output Example

```json
{
  "groceryList": [
    "spinach",
    "chickpeas",
    "quinoa",
    "tomatoes",
    "avocado",
    "almond milk"
  ],
  "mealPlan": [
    {
      "day": "Monday",
      "meals": {
        "breakfast": "Oatmeal with almond milk and berries",
        "lunch": "Quinoa salad with spinach and tomatoes",
        "dinner": "Stir-fried vegetables with chickpeas",
        "snacks": ["Carrot sticks", "Apple"]
      }
    },
    // ... rest of the days
  ]
}
```

---

## Deployment Notes

- **OpenAI API Key:** Ensure the OpenAI node is configured with your valid API key.
- **Webhook URL:** Use the generated webhook URL to send POST requests with user preferences.
- **Ingredient Extraction:** Consider improving the grocery list extraction logic to parse ingredients more reliably if needed.

---

## Summary

This workflow provides an automated service to deliver personalized nutrition plans alongside a curated grocery list, powered by GPT-4 and simple custom logic, accessible via a webhook endpoint.