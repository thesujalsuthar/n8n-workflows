# AI-Driven Personalized Career Skills Gap Analyzer and Development Roadmap Generator

## Overview

This workflow enables a personalized career skills gap analysis and generates a tailored development roadmap for users based on their current career profile and target career goals. Leveraging the power of OpenAI's GPT-4 model, it compares the user's existing skills against industry standards for their desired role and provides actionable skill development plans with recommended learning resources.

---

## Workflow Components

### 1. Webhook: `analyze-career-skills`

- **Type:** HTTP POST Webhook
- **Purpose:** Entry point to the workflow where user inputs are sent.
- **Endpoint:** `/analyze-career-skills`
- **Input:** JSON payload containing the user's:
  - Current career title
  - Skills
  - Experiences
  - Target career goals

### 2. AI Skill Gap Analysis

- **Type:** OpenAI (GPT-4) node
- **Function:** Processes the user input and performs the following:
  - Identifies skill gaps by comparing the user's current skills to the industry standard for the target career goal.
  - Generates a detailed skill development roadmap.
- **Model Used:** GPT-4
- **Prompt Instructions:**

  - Act as a career coach AI.
  - Identify missing skills, current vs target proficiency levels, and provide gap descriptions.
  - Develop a structured development roadmap with learning milestones and curated resources (courses, books, articles).
  - Output the response strictly in a structured JSON format.

- **Input to the node:** The raw JSON body received from the webhook.
- **Output:** JSON detailing:
  - `skillsGap`: List of skill gaps with descriptions and proficiency levels.
  - `developmentRoadmap`: For each skill, milestones, and recommended resources.

### 3. Parse AI Response JSON

- **Type:** Function node
- **Purpose:** Parses the raw text response from OpenAI into valid JSON format for downstream use.
- **Operation:** Parses the `text` field from the AI node output and returns it as a JSON object.

### 4. Respond to User

- **Type:** HTTP Response node
- **Purpose:** Sends back the parsed JSON response to the user who triggered the webhook.
- **Response:** Pure JSON containing the skills gap analysis and the personalized development roadmap.

---

## Input Format

Send a POST request to `/analyze-career-skills` with the following JSON structure:

```json
{
  "currentTitle": "string",
  "currentSkills": [
    "string"
  ],
  "experiences": "string",
  "targetCareerGoals": "string"
}
```

Example:

```json
{
  "currentTitle": "Junior Software Developer",
  "currentSkills": ["JavaScript", "HTML", "CSS"],
  "experiences": "2 years of front-end development",
  "targetCareerGoals": "Full-stack Developer"
}
```

---

## Output Format

The workflow will respond with a JSON structured as:

```json
{
  "skillsGap": [
    {
      "skill": "string",
      "currentLevel": "string",
      "targetLevel": "string",
      "gapDescription": "string"
    }
  ],
  "developmentRoadmap": [
    {
      "skill": "string",
      "milestones": ["string"],
      "resources": [
        {
          "title": "string",
          "type": "e.g. course, book, article",
          "url": "string"
        }
      ]
    }
  ]
}
```

---

## Workflow Execution Flow

1. **Receive Input:** A user submits their current career data and goals via a POST request to the webhook `/analyze-career-skills`.
2. **Skill Gap Analysis:** The input is forwarded to the OpenAI GPT-4 node which analyzes gaps and creates a development roadmap.
3. **Parse Response:** The AI’s text output JSON string is parsed back into JSON format.
4. **Respond to User:** The processed response is returned to the user as a clean JSON payload containing skill gaps and development recommendations.

---

## Prerequisites

- n8n automation platform setup.
- Valid OpenAI API key with GPT-4 access.
- Publicly accessible webhook endpoint or tunneling to your local n8n instance.

---

## Notes

- The AI responds strictly in JSON format ensuring seamless parsing and integration.
- Ensure the input JSON structure sent to the webhook is properly formatted for best results.
- Adjust temperature and token limits within the AI node parameters to control creativity/detail.

---

## License

This workflow is provided as-is for personal and commercial use. Modify and extend the nodes and prompt to suit your specific use cases.