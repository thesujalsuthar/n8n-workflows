# Career Path Recommendation Workflow

This workflow generates personalized career path recommendations based on the user's skills, interests, and current job market trends by leveraging external job trend data and OpenAI's language model.

---

## Workflow Overview

The workflow consists of the following nodes:

1. **Start**  
   The initial trigger of the workflow.

2. **Set User Input**  
   Defines the user's skills and interests as input parameters.  
   - Skills: `"Software Development, Data Analysis"`  
   - Interests: `"Artificial Intelligence, Cloud Computing"`

3. **Fetch Job Market Trends**  
   Makes an HTTP GET request to the Job Market Data API endpoint to retrieve the latest job market trends across all categories.  
   - URL: `https://api.jobmarketdata.com/v1/trends?categories=all`  
   - Response Format: JSON

4. **Analyze Trends Data**  
   Processes the API response to extract and filter top trending job titles based on a minimum trend score threshold (score >= 75).  
   - Outputs an array of objects containing `title` and `score` of top job trends.

5. **Combine All Data**  
   Aggregates user input (skills and interests) and the filtered top job trends into a single data structure.  
   - Splits the skills and interests strings into arrays for better processing.  
   - Packages the combined data as JSON for downstream use.

6. **Generate Career Recommendations**  
   Uses the OpenAI node to prompt a language model with the user's skills, interests, and top job trends to generate personalized career path recommendations.  
   - The prompt instructs the model to output a concise bullet list of recommended career paths tailored to the user profile and market trends.  
   - Requires OpenAI API credentials.

---

## Node Details

### 1. Set User Input
- **Type:** Set  
- **Description:** Sets the static user input fields: `skills` and `interests`.
- **Fields:**  
  - `skills`: `"Software Development, Data Analysis"`  
  - `interests`: `"Artificial Intelligence, Cloud Computing"`

### 2. Fetch Job Market Trends
- **Type:** HTTP Request  
- **Method:** GET  
- **Endpoint:** `https://api.jobmarketdata.com/v1/trends?categories=all`  
- **Response Type:** JSON  
- **Purpose:** Retrieves current job market trends data for all job categories.

### 3. Analyze Trends Data
- **Type:** Function  
- **Purpose:** Filters the trend data received from the API to identify top job trends.  
- **Logic:**  
  - Parses the `trends` array from the API response.  
  - Filters trends with scores greater than or equal to 75.  
  - Maps filtered trends to an array with `title` and `score` properties.

### 4. Combine All Data
- **Type:** Function  
- **Purpose:** Combines the processed user input and top job trend data into a unified JSON object.  
- **Logic:**  
  - Extracts skills and interests from the "Set User Input" node and converts them into arrays.  
  - Includes the top trending jobs from the previous node.

### 5. Generate Career Recommendations
- **Type:** OpenAI  
- **Purpose:** Generates personalized career path recommendations using AI.  
- **Prompt Template:**  
  ```
  User Skills: {{$json["skills"]}}
  User Interests: {{$json["interests"]}}
  Current Top Job Trends: {{$json["topJobTrends"]}}

  Based on user's skills and interests, and the current job market trends, generate personalized career path recommendations in a short bullet list.
  ```
- **Requirements:**  
  - OpenAI API credentials must be configured for this node.

---

## Workflow Execution Flow

1. The workflow starts automatically (triggered by the Start node).
2. User data is set via the **Set User Input** node.
3. The workflow fetches the latest job trends using the **Fetch Job Market Trends** node.
4. Trend data is filtered and analyzed in the **Analyze Trends Data** node.
5. User input and top trends are combined in the **Combine All Data** node.
6. Final personalized career recommendations are generated and returned by **Generate Career Recommendations** node.

---

## Prerequisites

- Access to the Job Market Data API at `https://api.jobmarketdata.com/v1/trends`.
- Valid OpenAI API credentials configured in the workflow.
- n8n automation tool set up with appropriate HTTP Request and OpenAI nodes.

---

## Customization

- Modify the user's skills and interests in the **Set User Input** node to match any user profile.
- Adjust the trend score threshold in the **Analyze Trends Data** function node as needed.
- Customize the OpenAI prompt to tailor the style or depth of the recommendations.
- Extend to accept dynamic user input via triggers or forms for real-time recommendations.

---

## Output

The final output from the workflow is a short bullet list of career path recommendations personalized to the user's skills, interests, and current top job market trends. This can be used for decision support in career planning applications or services.