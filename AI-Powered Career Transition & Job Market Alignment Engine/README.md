# AI-Powered Personalized Career Transition Planner and Job Market Alignment System

This workflow leverages AI and multiple APIs to create a personalized career transition plan, align it with current job market opportunities, recommend upskilling courses, and schedule coaching sessions or interviews automatically in a calendar.

---

## Workflow Overview

1. **Start**  
   Entry point of the workflow.

2. **Analyze Skills and Interests**  
   Uses OpenAI's API to detect and analyze the user's combined skills and interests.

3. **Career Transition Planner (AI)**  
   An AI-based career coach powered by OpenAI generates a personalized transition plan based on the analyzed skills and interests. The AI suggests:
   - Ideal career paths
   - Key skill gaps
   - Recommended upskilling topics
   - Criteria for job opportunities  
   The response is expected in a JSON format with keys: `careerPaths`, `skillGaps`, `upskillTopics`, `jobKeywords`, and `preferredLocation`.

4. **Prepare Query Params**  
   A function node that prepares the query parameters for downstream API calls:
   - Extracts job keywords from the AI suggestions.
   - Determines preferred job location.
   - Gathers upskill topics for course recommendations.

5. **Fetch Job Opportunities**  
   Queries the Adzuna Jobs API with the prepared job keywords and location to retrieve up to 10 relevant job listings.

6. **Get Upskilling Courses**  
   Searches Udemy API for up to 5 courses matching the suggested upskill topics to support closing identified skill gaps.

7. **Generate Session Schedule**  
   Creates a suggested date and time for a follow-up coaching or interview session, defaulting to 3 days from the current date and using the first recommended career path as the session subject.

8. **Schedule Session in Calendar**  
   Adds an event to the user's Google Calendar scheduled for the suggested date/time with details of the specific career path or session purpose.

---

## Node Details

### 1. Analyze Skills and Interests (OpenAI)  
- **Operation:** `detect` entities from concatenated `skills` and `interests` input.  
- **Input:** `$json.skills + ' ' + $json.interests`  
- **Credentials:** OpenAI API

### 2. Career Transition Planner (AI)  
- **Prompt:** AI career coach instructing to create a transition plan based on user's skills/interests and labor market trends.  
- **Response:** JSON including career recommendations and skill gaps.  
- **Credentials:** OpenAI API

### 3. Prepare Query Params (Function)  
- Extracts parameters for jobs and courses search from AI's JSON response:
  - `job_keywords`
  - `location`
  - `upskill_topics`

### 4. Fetch Job Opportunities (HTTP Request)  
- **API:** Adzuna Jobs API (US)  
- **Parameters:**  
  - `app_id`, `app_key` from credentials  
  - `what` = job keywords  
  - `where` = preferred location  
  - `results_per_page` = 10  
- **Credentials:** Adzuna API (HTTP Basic Auth)

### 5. Get Upskilling Courses (Udemy API)  
- **Operation:** Search courses by topics from `upskill_topics`  
- **Limit:** 5 courses  
- **Credentials:** Udemy API

### 6. Generate Session Schedule (Function)  
- Creates `suggestedDateTime` set to 3 days from now.  
- Defines `careerPath` as the first recommended career path or a default string.

### 7. Schedule Session in Calendar (Google Calendar)  
- **Event:** "Interview / Coaching Session"  
- **Start/End:** Based on `suggestedDateTime` and 1-hour duration  
- **Description:** Contains selected career path  
- **TimeZone:** America/New_York  
- **Credentials:** Google Calendar OAuth2 API

---

## Required Credentials

- **OpenAI API**: For AI-based skill analysis and career planning.  
- **Adzuna API**: To fetch real-time job listings based on user criteria.  
- **Udemy API**: To recommend relevant upskilling courses.  
- **Google Calendar OAuth2 API**: To schedule coaching or interview sessions automatically.

---

## Input Data Structure

The workflow expects input JSON with at least:

```json
{
  "skills": ["skill1", "skill2", ...],
  "interests": ["interest1", "interest2", ...],
  "preferredLocation": "location_string"
}
```

---

## Output Highlights

- AI-generated personalized career transition plan.  
- List of relevant job opportunities in the desired location.  
- Recommended upskilling courses to bridge skill gaps.  
- Scheduled calendar event for coaching/interview follow-up.

---

## How to Use

1. Provide user's current skills, interests, and preferred job location as input.  
2. The workflow processes this input, engages AI and external APIs, and produces actionable career transition information.  
3. Review fetched job opportunities and recommended courses.  
4. Utilize the scheduled calendar event for timely coaching or interview sessions.

---

This workflow automates the integration of AI-driven career guidance with live job market data and continuous learning resources, enhancing job seekers’ chances of a successful career change and alignment.