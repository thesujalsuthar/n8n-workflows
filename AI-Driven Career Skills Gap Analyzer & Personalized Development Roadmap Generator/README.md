# AI-Powered Personalized Career Skills Gap Analyzer and Development Roadmap Generator

This workflow leverages AI to analyze a user's current skills against their desired job roles, identify skill gaps, and generate a personalized skill development roadmap equipped with learning resources and estimated timelines.

---

## Workflow Overview

The workflow consists of the following steps:

1. **Extract User Input**  
   Extracts and prepares user data including:
   - `userId` (optional)
   - `currentSkills` (array of user's current skills)
   - `desiredJobRoles` (array of target job roles)

2. **Analyze Skills Gap**  
   Uses OpenAI's GPT-4 model to identify gaps between current skills and those required for desired roles. The AI returns:
   - `skillGaps`: List of missing or weak skills
   - `justification`: Explanation for why each skill is important or considered a gap

3. **Parse Skills Gap Analysis**  
   Converts the AI's JSON text response into structured JSON for further processing.

4. **Generate Development Roadmap**  
   Creates a personalized learning roadmap for each skill gap, including:
   - Skill difficulty level (`basic`, `intermediate`, `advanced`)
   - Estimated learning duration in days (14, 30, or 60 days respectively)
   - Learning resources with titles and URLs curated per skill
   - Start and end days for the learning timeline (sequenced)

5. **Combine Output**  
   Combines the skill gaps, justifications, and development roadmap into a single output JSON object for consumption.

---

## Nodes Detail

### 1. Extract User Input  
- **Type:** Function node  
- **Function:** Extracts `userId`, `currentSkills`, and `desiredJobRoles` from raw input JSON, ensuring defaults where necessary.

### 2. Analyze Skills Gap  
- **Type:** OpenAI GPT-4 node  
- **Prompt:**  
  Instructs the AI to compare current skills with required skills and return skill gap details and justifications as JSON:
  ```json
  {
    "skillGaps": [...],
    "justification": {
      "skill1": "reason1",
      "skill2": "reason2"
    }
  }
  ```
- **Parameters:**  
  Temperature: 0.6, Max tokens: 600, no penalty parameters  
- **Credentials:** OpenAI API key required

### 3. Parse Skills Gap Analysis  
- **Type:** Function node  
- **Function:** Parses AI-generated JSON string from the OpenAI response to extract `skillGaps` and `justification`.

### 4. Generate Development Roadmap  
- **Type:** Function node  
- **Function:**  
  - Maps each skill gap to a difficulty level (default is `intermediate` if unknown)  
  - Assigns estimated learning days based on difficulty  
  - Gathers curated learning resources per skill  
  - Constructs a sequential timeline roadmap with start and end days for learning each skill

- **Predefined Data:**
  - Difficulty to days:  
    - Basic: 14 days  
    - Intermediate: 30 days  
    - Advanced: 60 days  
  - Skill difficulty mapping (can be customized)  
  - Learning resources with titles and URLs for each skill

### 5. Combine Output  
- **Type:** Function node  
- **Function:** Combines skill gaps, justifications, and development roadmap into a final consolidated JSON.

---

## Input Format

```json
{
  "userId": "user123",                // Optional
  "currentSkills": ["Python", "Data Analysis"],
  "desiredJobRoles": ["Machine Learning Engineer", "Data Scientist"]
}
```

---

## Output Format

```json
{
  "skillGaps": ["Machine Learning", "Cloud Computing"],
  "justification": {
    "Machine Learning": "Essential for developing predictive models required in desired job roles.",
    "Cloud Computing": "Important for deploying machine learning models in production environments."
  },
  "developmentRoadmap": [
    {
      "skill": "Machine Learning",
      "difficulty": "advanced",
      "estimatedDays": 60,
      "startDay": 1,
      "endDay": 60,
      "learningResources": [
        {
          "title": "Machine Learning by Andrew Ng - Coursera",
          "url": "https://www.coursera.org/learn/machine-learning"
        },
        {
          "title": "Hands-On Machine Learning",
          "url": "https://www.oreilly.com/library/view/hands-on-machine-learning/9781492032632/"
        }
      ]
    },
    {
      "skill": "Cloud Computing",
      "difficulty": "advanced",
      "estimatedDays": 60,
      "startDay": 61,
      "endDay": 120,
      "learningResources": [
        {
          "title": "AWS Certified Solutions Architect",
          "url": "https://aws.amazon.com/certification/certified-solutions-architect-associate/"
        },
        {
          "title": "Google Cloud Training",
          "url": "https://cloud.google.com/training"
        }
      ]
    }
  ]
}
```

---

## Prerequisites

- n8n workflow automation environment
- OpenAI API key with access to GPT-4
- JSON input containing user skill and job role data

---

## How to Use

1. **Provide Input:** Supply user data with current skills and desired job roles to the workflow's first node.
2. **Run Workflow:** The workflow sequentially performs gap analysis, parses results, builds a development plan, and outputs combined results.
3. **Consume Output:** Use the final combined JSON for reporting, UI display, or further processing.

---

## Customization

- Update the `skillDifficulty` mapping with more skills or adjust difficulty levels.
- Expand or replace `learningResources` with data from external APIs or databases.
- Modify timelines or resource selection logic in the "Generate Development Roadmap" node as needed.
- Enhance the AI prompt in the "Analyze Skills Gap" node to refine output and format.

---

## Notes

- The skill difficulty and learning resource data are onboarded within the workflow for demonstration; real-world deployments should integrate dynamic and comprehensive data sources.
- The AI analysis depends on the quality and clarity of the user-inputted current skills and job roles.

---

## License

This workflow is provided as-is for educational and personal development purposes. Modify and extend according to your project needs.