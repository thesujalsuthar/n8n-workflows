# AI-Powered Personalized Grocery Shopping List Generator with Nutrition and Budget Optimization

This workflow is designed to automatically generate and email a personalized grocery shopping list every day at 7:00 AM. The list is optimized based on user-specific dietary preferences, nutrition goals, and budget constraints using OpenAI's GPT-4 model.

---

## Workflow Overview

The workflow consists of the following nodes:

### 1. Daily Trigger
- **Type:** Time Trigger
- **Description:** Triggers the workflow every day at 7:00 AM to start the grocery list generation process.

### 2. Set User Input
- **Type:** Set Node
- **Description:** Defines static user inputs that include:
  - `dietaryPreferences`: User’s dietary restrictions or preferences (e.g., "vegetarian, gluten-free").
  - `nutritionGoals`: Nutrition targets (e.g., "high protein, low carb").
  - `budget`: Maximum budget in USD (e.g., "50").
  - `email`: Recipient’s email address where the list will be sent.

### 3. Generate Grocery List
- **Type:** HTTP Request
- **Description:** Sends a POST request to OpenAI's Chat Completion API (`gpt-4` model) to generate a personalized grocery list.
- **Request Details:**
  - URL: `https://api.openai.com/v1/chat/completions`
  - Headers:
    - `Authorization`: Bearer token using OpenAI API key from credentials.
    - `Content-Type`: application/json
  - Body Parameters:
    - `model`: "gpt-4"
    - `messages`: Includes a system prompt describing the assistant’s role and a user prompt that sends dietary preferences, nutrition goals, and budget.
    - `temperature`: 0.7 for creativity and variability in output.
    - `max_tokens`: 600 to allow detailed responses.
- **Authentication:** Uses OpenAI API credentials.

### 4. Parse AI Response
- **Type:** Function
- **Description:** Parses the JSON response from the OpenAI API, extracting the generated grocery list content from the AI's message.

### 5. Send Email
- **Type:** Email Send
- **Description:** Sends an email to the user with the personalized grocery list.
- **Email Content:**
  - **Subject:** "Your Personalized Grocery Shopping List"
  - **Body:**
    ```
    Hello,

    Here is your personalized grocery shopping list optimized according to your dietary preferences, nutrition goals, and budget:

    {{$json.groceryList}}

    Happy Shopping!
    ```
- **Recipient:** Email address set in the "Set User Input" node.

---

## Configuration Instructions

1. **OpenAI API Key:**
   - Add your OpenAI API key in the workflow credentials named `OpenAI API`.
   
2. **User Inputs:**
   - Update the `dietaryPreferences`, `nutritionGoals`, `budget`, and `email` fields in the **Set User Input** node as per your requirements.

3. **Email Settings:**
   - Ensure that the email node is configured with the correct SMTP settings or email provider credentials to send out emails.

4. **Trigger Time:**
   - The workflow triggers daily at 7:00 AM by default. Modify the **Daily Trigger** node if a different schedule is desired.

---

## How It Works

- Every day at 7:00 AM, the workflow kicks off automatically.
- User details (dietary preferences, nutrition goals, and budget) are set in the workflow.
- These details are sent to the GPT-4 model, which generates a personalized grocery shopping list optimized for nutrition and budget.
- The AI-generated list is then parsed and emailed to the user.

---

## Customize Your Workflow

- **User Preferences:** Modify the input values dynamically by integrating the workflow with a database, form, or API to fetch user-specific data instead of static inputs.
- **AI Model:** Change to other OpenAI models by updating the `model` parameter in the HTTP Request node.
- **Email Content:** Customize the email template to include additional details or formatting as needed.

---

## Summary

This workflow leverages the power of AI to create customized grocery lists tailored to specific dietary and budgetary needs, delivered conveniently via email every morning to help users plan their shopping efficiently and healthily.