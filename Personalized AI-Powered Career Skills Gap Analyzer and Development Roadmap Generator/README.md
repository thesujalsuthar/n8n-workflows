# Personalized AI-Powered Career Skills Gap Analyzer and Development Roadmap Generator

This workflow is designed to analyze an individual's current skills against the requirements of a desired job role, identify skills gaps, and generate a personalized development roadmap with recommended learning courses. The entire process is automated using a series of API integrations and custom logic, delivering actionable insights directly to the user.

---

## Workflow Overview

The workflow consists of the following key steps:

1. **User Input**  
   Manually triggered node receives input data containing the user's current skills and the desired job role.

2. **Parse User Input**  
   Extracts and formats the required fields (`currentSkills` and `desiredJobRole`) from the user input JSON.

3. **Get Job Market Trends**  
   Retrieves job market trends related to the desired job role from an external Job Market Trends API.

4. **Get Required Skills**  
   Fetches the official list of required skills for the desired job role from a Skills Database API.

5. **Analyze Skill Gaps**  
   Compares the user's current skills with the required skills and identifies skills that the user needs to acquire.

6. **Get Courses**  
   Queries an Online Learning Platforms API for relevant courses targeting the identified skill gaps, prioritizing top recommendations.

7. **Generate Roadmap**  
   Builds a development roadmap that maps each gap skill to recommended courses, creating a clear upskilling path.

8. **Send Result**  
   Sends the personalized skills gap analysis and development roadmap to the user via Telegram.

---

## Detailed Node Descriptions

### 1. User Input (`User Input` node)

- **Type:** Manual Trigger  
- **Function:** Starts the workflow with user-provided input.  
- **Expected Input Format:**
  ```json
  {
    "currentSkills": ["skill1", "skill2", ...],
    "desiredJobRole": "string"
  }
  ```

### 2. Parse User Input (`Parse User Input` node)

- **Type:** Function Node  
- **Function:**  
  - Parses the input JSON.  
  - Ensures `currentSkills` is an array and `desiredJobRole` is a string.

### 3. Get Job Market Trends (`Get Job Market Trends` node)

- **Type:** HTTP Request  
- **Function:** Fetches data about market trends for the specified job role.  
- **Endpoint:** `https://api.jobmarkettrends.example.com/job-roles/{{desiredJobRole}}`  
- **Authentication:** Bearer token via header `Authorization: Bearer YOUR_JOB_MARKET_TRENDS_API_KEY`  
- **Response:** JSON format with job market insights.

### 4. Get Required Skills (`Get Required Skills` node)

- **Type:** HTTP Request  
- **Function:** Retrieves required skills for the desired job role.  
- **Endpoint:** `https://api.skillsdb.example.com/skills/job/{{desiredJobRole}}`  
- **Authentication:** API key via header `x-api-key: YOUR_SKILLS_DB_API_KEY`  
- **Response:** JSON including a list of skills relevant to the job role.

### 5. Analyze Skill Gaps (`Analyze Skill Gaps` node)

- **Type:** Function Node  
- **Function:**  
  - Compares `currentSkills` with `requiredSkills`.  
  - Outputs the identified `gapSkills` (skills required but missing).  
  - Passes along `desiredJobRole`.

### 6. Get Courses (`Get Courses` node)

- **Type:** HTTP Request  
- **Function:** Fetches relevant courses to fill the skill gaps from an Online Learning Platforms API.  
- **Endpoint:** `https://api.onlinelearningplatforms.example.com/courses`  
- **Query Parameters:**  
  - `skills` - comma-separated list of `gapSkills`  
  - `limit` - max number of courses to return (default 5)  
- **Authentication:** Bearer token via header `Authorization: Bearer YOUR_LEARNING_PLATFORM_API_KEY`  
- **Response:** JSON list of courses tagged with relevant skills.

### 7. Generate Roadmap (`Generate Roadmap` node)

- **Type:** Function Node  
- **Function:**  
  - Maps each skill gap to a list of up to 3 recommended courses.  
  - Constructs a detailed roadmap object with skill-course mappings.

### 8. Send Result (`Send Result` node)

- **Type:** Telegram Node  
- **Function:** Sends the personalized skill gap analysis and roadmap to the user via Telegram message.  
- **Message Format:**  
  ```
  Your personalized skills gap analysis and development roadmap for the role '<desiredJobRole>':
  <formatted JSON roadmap>
  ```  
- **Configuration:** The chat ID can be supplied dynamically or use a default.

---

## Setup Instructions

1. **API Keys**  
   - Obtain API keys for:  
     - Job Market Trends API (`YOUR_JOB_MARKET_TRENDS_API_KEY`)  
     - Skills Database API (`YOUR_SKILLS_DB_API_KEY`)  
     - Online Learning Platforms API (`YOUR_LEARNING_PLATFORM_API_KEY`)  
   - Replace the placeholders in the respective HTTP Request nodes.

2. **Telegram Integration**  
   - Configure the Telegram node with your bot token and valid `chatId`.  
   - Adjust if sending messages dynamically based on user data.

3. **Input Data**  
   - Trigger the workflow manually and provide user data in the expected JSON format (`currentSkills` and `desiredJobRole`).

---

## Input JSON Example

```json
{
  "currentSkills": ["Python", "Data Analysis", "SQL"],
  "desiredJobRole": "Data Scientist"
}
```

---

## Output Example

Upon completion, the user receives a Telegram message with content similar to:

```
Your personalized skills gap analysis and development roadmap for the role 'Data Scientist':
[
  {
    "skill": "Machine Learning",
    "recommendedCourses": [
      {
        "title": "Intro to Machine Learning",
        "provider": "ExamplePlatform",
        "url": "https://example.com/course/ml"
      },
      ...
    ]
  },
  ...
]
```

---

## Notes

- Ensure all API endpoints, authentication tokens, and chat IDs are correctly configured before running the workflow.
- The workflow is modular and can be extended to include more detailed analysis or alternative communication channels.
- Error handling is recommended when integrating APIs to ensure resilience.

---

This workflow provides a powerful automation tool for personalized career development, enabling users to identify their skill gaps and receive actionable learning recommendations tailored to their goals.