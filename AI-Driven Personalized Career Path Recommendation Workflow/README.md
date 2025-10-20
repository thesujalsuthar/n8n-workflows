# AI-Powered Personalized Career Path Recommendation Engine

This workflow provides a personalized career path recommendation based on user-submitted skills and career goals, leveraging the power of AI to analyze current market trends and suggest suitable roles, learning resources, and job opportunities.

---

## Workflow Overview

The workflow consists of the following main steps:

1. **Webhook - Receive User Data**  
   An HTTP POST webhook endpoint (`submitUserData`) receives the user's information, including their skills and career goals.

2. **Validate Input**  
   This node validates the incoming data to ensure the required fields (`skills` and `careerGoals`) are present. If any of these are missing, an error is thrown.

3. **AI - Generate Career Path Recommendation**  
   Utilizes OpenAI's GPT-4 model to analyze the user profile along with current market trends. The system message configures the AI as a career path recommendation engine. The user prompt passes the skills, career goals, and optionally market trends for generating a personalized recommendation.

4. **Format Output**  
   Extracts and formats the AI's response to prepare a clear JSON output containing the career path recommendation.

5. **Set Response**  
   Sends the formatted career path recommendation back as the HTTP response to the webhook request.

---

## Node Details

### 1. Webhook - Receive User Data
- **Method:** POST  
- **Path:** `/submitUserData`  
- **Purpose:** Accepts user profile data including `skills` and `careerGoals`.  

### 2. Validate Input
- **Type:** Function Node  
- **Purpose:** Validates the presence of mandatory fields:  
  - `skills`  
  - `careerGoals`  
- **Behavior:** Throws an error if any required field is missing, preventing further workflow execution.

### 3. AI - Generate Career Path Recommendation
- **Model:** GPT-4  
- **Parameters:**  
  - Temperature: 0.7 (balances creativity and relevance)  
  - Max tokens: 1500 (allows detailed responses)  
- **System Message:** Defines the AI as a career path recommendation engine analyzing user data and market trends.  
- **User Message:** Provides user inputs and market trends to generate a tailored career recommendation covering:  
  - Suitable roles  
  - Skill development roadmap  
  - Suggested learning resources  
  - Potential job opportunities  

### 4. Format Output
- **Type:** Function Node  
- **Purpose:** Extracts the main content from the AI's response and packages it into a structured JSON object under `careerPathRecommendation`.  

### 5. Set Response
- **Type:** Respond to Webhook  
- **Purpose:** Sends the final formatted recommendation back as the API response to the original POST request.

---

## Input Payload Example

The POST request to `/submitUserData` should contain a JSON body with the following structure:

```json
{
  "skills": ["JavaScript", "React", "Node.js"],
  "careerGoals": "Become a full-stack developer focusing on web applications",
  "marketTrends": "Increasing demand for cloud-native applications and AI integration"
}
```

- `skills`: An array or list of current skills the user possesses.
- `careerGoals`: A string outlining the user’s career aspirations.
- `marketTrends` (optional): Additional context about current market trends to tailor the recommendation.

---

## Output Response Example

The response returned from the webhook call will be a JSON structured like:

```json
{
  "careerPathRecommendation": "<AI generated detailed career path recommendation>"
}
```

Where `<AI generated detailed career path recommendation>` includes suggestions for roles, learning pathways, resources, and job opportunities crafted specifically for the user's profile.

---

## Usage Notes

- Ensure API credentials for OpenAI GPT-4 are properly configured in your environment to enable the AI node.
- Validate input data before sending to the webhook to reduce validation errors.
- Customize `marketTrends` input based on specific industry insights as needed.

---

## Summary

This workflow offers a seamless integration from user data submission through AI-driven career path generation, delivering personalized and actionable career guidance with minimal manual intervention.