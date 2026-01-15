# Interactive AI-Powered Personalized Nutrition and Meal Planning Assistant

## Overview
This workflow enables users to generate a personalized 7-day meal plan tailored to their dietary preferences, health goals, and currently available ingredients. It uses artificial intelligence to produce meal plans including breakfast, lunch, and dinner, accompanied by a grocery list. Additionally, it performs nutritional analysis of the generated meal plan and can place a grocery order based on the grocery list.

---

## Workflow Components

### 1. User Input Type Selection
- **Node Type:** Prompt Node
- **Purpose:** Allows users to select which type of input they want to provide:
  - Meal Preferences (e.g., vegan, gluten-free, keto)
  - Health Goals (e.g., weight loss, muscle gain, heart health)
  - Ingredient Availability (ingredients the user currently has at home)

### 2. User Input Collection
- **Node Type:** Prompt Node
- **Purpose:** Collects detailed input based on the user's selection:
  - Meal preferences as string input.
  - Health goals as string input.
  - Ingredient availability provided as a comma-separated list.

### 3. AI-Based Meal Plan Generation
- **Node Type:** HTTP Request
- **API:** OpenAI Chat Completion API (GPT-4)
- **Purpose:**
  - Sends a structured prompt to the OpenAI API.
  - The prompt instructs the AI to create a personalized 7-day meal plan including all meals.
  - The AI also generates a grocery list necessary for preparing these meals.
- **Input Data:**
  - Meal preferences
  - Health goals
  - Ingredient availability
- **Output:** AI-generated text containing the meal plan and grocery list.

### 4. Parse Meal Plan and Grocery List
- **Node Type:** Function Node
- **Purpose:** Extracts and separates the meal plan and grocery list from the AI-generated response.
- **Process:**
  - Looks for the keyword `"Grocery List:"` in the AI response.
  - Splits the content before this keyword as the meal plan.
  - Splits the content after as the grocery list.

### 5. Nutritional Analysis
- **Node Type:** HTTP Request
- **API:** External Nutrition Analysis API (replace with actual API)
- **Purpose:** Analyzes the nutritional content of the generated meal plan text.
- **Input:** Meal plan text.
- **Output:** Nutritional breakdown (e.g., calories, macronutrients, vitamins).

### 6. Place Grocery Order
- **Node Type:** HTTP Request
- **API:** Grocery Delivery API (replace with actual API)
- **Purpose:** Automatically places an order for grocery items extracted from the grocery list.
- **Input:** Grocery list items parsed into structured JSON with item names and quantities (default quantity: 1).
- **Output:** Order confirmation or status (dependent on API response).

### 7. Finalize and Output
- **Node Type:** No Operation (NoOp) Node
- **Purpose:** Acts as a workflow terminator consolidating outputs from nutritional analysis and grocery order placement.

---

## Configuration Notes

- **OpenAI API Key:** Replace `"YOUR_OPENAI_API_KEY"` with a valid API key.
- **Nutrition Analysis API Key:** Replace `"YOUR_NUTRITION_API_KEY"` and URL with your nutrition API credentials and endpoint.
- **Grocery Delivery API Key:** Replace `"YOUR_GROCERY_API_KEY"` and URL with your grocery delivery service credentials and endpoint.

---

## Usage Flow

1. User selects the type of input they want to provide (meal preferences, health goals, ingredient availability).
2. User inputs their specific details as requested.
3. The AI processes the input to generate a comprehensive weekly meal plan plus a grocery list.
4. The generated text is parsed to separate meal plan details from grocery items.
5. Nutritional analysis is performed on the meal plan.
6. An order is placed for grocery items based on the grocery list.
7. The workflow consolidates the results and completes execution.

---

## Summary
This workflow provides an end-to-end, interactive solution to assist users in planning meals customized to their lifestyle and health needs, while leveraging AI and external services to deliver nutrition insights and streamline grocery procurement.