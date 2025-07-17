# AI-Powered Personalized Fitness and Wellness Plan Generator

This workflow leverages AI to generate a customized fitness and wellness plan based on user input and existing fitness data. It automates the process from receiving user details to delivering a detailed plan via email.

---

## Workflow Overview

The workflow consists of the following steps:

1. **User Input**  
   Captures user-provided profile data such as goals, preferences, and personal details.

2. **Read Fitness Data**  
   Reads a JSON file (`fitness_data.json`) containing detailed fitness data relevant to the user.

3. **AI Plan Generator**  
   Sends a request to the OpenAI GPT-4 API, combining the user profile and fitness data, to generate a personalized fitness, nutrition, and mindfulness plan.

4. **Extract AI Plan**  
   Extracts and formats the AI-generated plan from the API response.

5. **Create Plan File**  
   Writes the personalized plan into a plain text file (`personalized_fitness_plan.txt`).

6. **Send Plan Email**  
   Sends the plan file as an email attachment to the user's specified email address.

---

## Nodes Breakdown

### 1. User Input (`Set` node)
- **Function:** Allows entry of the user's profile data.
- **Parameters:**  
  - `userInput` (string): User profile details such as fitness goals, current fitness level, dietary preferences, etc.
- **Position:** Initiates the workflow.

### 2. Read Fitness Data (`Read Binary File` node)
- **Function:** Reads fitness data from a local JSON file `fitness_data.json`.
- **Details:** The data is loaded in binary format and later decoded in the AI request.
- **Position:** Connected after user input.

### 3. AI Plan Generator (`HTTP Request` node)
- **Function:** Calls OpenAI’s GPT-4 API to generate a personalized plan.
- **Request Details:**  
  - **Method:** POST  
  - **Endpoint:** `https://api.openai.com/v1/chat/completions`  
  - **Body:**  
    - Model: `gpt-4`  
    - Messages: System prompt establishing the AI as a fitness and wellness assistant, followed by the combined user profile and fitness data.  
    - Temperature: 0.7 (for creativity)  
- **Authentication:** Uses stored OpenAI API key credentials.
- **Position:** After reading fitness data.

### 4. Extract AI Plan (`Function` node)
- **Function:** Parses the AI response JSON to extract the personalized plan content.
- **Code:** Extracts the `content` field from the first choice message and trims whitespace.
- **Output:** Outputs the personalized plan as string data.
- **Position:** After AI Plan Generator.

### 5. Create Plan File (`Write Binary File` node)
- **Function:** Writes the personalized plan into a text file named `personalized_fitness_plan.txt`.
- **Output:** Creates a binary file attachment used downstream.
- **Position:** After extraction of AI plan.

### 6. Send Plan Email (`Email Send` node)
- **Function:** Sends an email with the personalized plan attached.
- **From:** `fitnessbot@example.com` (can be customized)  
- **To:** The user's email, dynamically referenced from input data (`userEmail`).  
- **Subject:** "Your Personalized Fitness and Wellness Plan"  
- **Body:** Friendly message encouraging fitness and wellness adherence.  
- **Attachment:** `personalized_fitness_plan.txt` file generated in previous step.  
- **Credentials:** SMTP credentials configured for sending email.
- **Position:** Final step in workflow.

---

## Prerequisites

- **n8n Automation Tool** installed and configured.
- **OpenAI API Key** with GPT-4 access, saved as credentials in n8n.
- **SMTP Email Credentials** configured for sending emails.
- **fitness_data.json** file placed in the appropriate path accessible by the workflow.
- User inputs structured as expected in the `User Input` node.

---

## Usage Instructions

1. Open n8n and import this workflow.
2. Configure credentials:  
   - Add OpenAI API Key with access to GPT-4.  
   - Add SMTP email credentials.
3. Place your `fitness_data.json` file in the path specified (`fitness_data.json`).
4. Fill in the `User Input` node with the user profile details and ensure `userEmail` is included in the input JSON.
5. Activate and execute the workflow.
6. The user will receive an email with a detailed personalized fitness and wellness plan attached.

---

## Data Format Expectations

- **User Input:** A structured JSON string containing user details such as:
  - Fitness goals (e.g., weight loss, muscle gain)
  - Dietary preferences and restrictions
  - Mindfulness practices or interests
  - Current fitness level and limitations
  - Email address (`userEmail`) for sending the plan

- **Fitness Data (`fitness_data.json`):** JSON formatted data containing the user's historical or current fitness tracking information, biometrics, or related health stats.

---

## Summary

This workflow automates the generation and delivery of a fully personalized fitness and wellness program by integrating user data, fitness data, and advanced AI capabilities. It simplifies user engagement by producing actionable recommendations on workouts, nutrition, and mindfulness tailored for each individual and delivering them conveniently via email.