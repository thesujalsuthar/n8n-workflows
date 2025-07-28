# AI-Powered Smart Resume Analyzer and Job Application Optimizer

This workflow leverages AI to analyze a given resume against a specific job description, provides detailed feedback with improvement suggestions, generates a tailored cover letter, and submits the optimized application via email and HTTP request to a job portal.

---

## Workflow Overview

1. **Read Resume File**  
   Reads the uploaded resume file in binary format.

2. **Read Job Description File**  
   Reads the uploaded job description file in binary format.

3. **Extract Text from Files**  
   Decodes the binary data from the resume and job description files into UTF-8 text.

4. **AI Analyze Resume vs Job Description**  
   Uses the GPT-4 model to analyze the resume relative to the job description. It produces detailed feedback discussing strengths, weaknesses, keyword optimization, skills alignment, and formatting suggestions.

5. **Generate Cover Letter**  
   Creates a customized cover letter based on the AI feedback and applicant’s name, emphasizing how skills and experiences relate to the job description.

6. **Send Application Email**  
   Sends an email containing the AI-generated feedback and improvement suggestions to the designated recipient.

7. **Submit Job Application**  
   Sends an HTTP POST request to the specified job portal URL, submitting applicant details, resume text, and the generated cover letter formatted as JSON.

---

## Nodes Description

### 1. Read Resume File (`readBinaryFile` node)  
- **Operation:** Reads binary data of the resume file.  
- **Binary Property:** `resumeFile`  
- **Purpose:** Imports the raw resume file for processing.

### 2. Read Job Description File (`readBinaryFile` node)  
- **Operation:** Reads binary data of the job description file.  
- **Binary Property:** `jobDescriptionFile`  
- **Purpose:** Imports the raw job description file for processing.

### 3. Extract Text from Files (`function` node)  
- **Code:**  
  ```js
  const resumeBuffer = $items("Read Resume File")[0].binary["resumeFile"].data;
  const jobDescBuffer = $items("Read Job Description File")[0].binary["jobDescriptionFile"].data;

  const resumeText = Buffer.from(resumeBuffer, 'base64').toString('utf-8');
  const jobDescriptionText = Buffer.from(jobDescBuffer, 'base64').toString('utf-8');

  return [{ json: { resumeText, jobDescriptionText } }];
  ```  
- **Purpose:** Converts binary file content into readable text strings for AI analysis.

### 4. AI Analyze Resume vs Job Description (`openAi` node)  
- **Model:** GPT-4  
- **Parameters:**  
  - Temperature: 0.3  
  - Prompt: Analyzes the resume alongside the job description, providing:  
    - Strengths & weaknesses of the resume relative to the job  
    - Specific improvement suggestions (keywords, skills, formatting)  
- **Input:** `resumeText` and `jobDescriptionText` from previous step.

### 5. Generate Cover Letter (`function` node)  
- **Code:**  
  ```js
  const aiContent = $json["choices"] && $json["choices"].length > 0 ? $json["choices"][0].message.content : "No feedback available.";
  const coverLetter = `Dear Hiring Manager,\n\nBased on the job description and my resume, I am excited to apply for this position. I have tailored my skills and experiences accordingly:\n\n${aiContent}\n\nThank you for considering my application.\n\nSincerely,\n${$json["applicantName"] || "Applicant"}`;

  return [{ json: { coverLetter } }];
  ```  
- **Purpose:** Generates a personalized cover letter integrating AI feedback to strengthen the application.

### 6. Send Application Email (`emailSend` node)  
- **From:** `user@example.com` (customize as needed)  
- **To:** `recipient@example.com` (customize as needed)  
- **Subject:** "Optimized Job Application Based on Your Resume and Job Description"  
- **Body:** Includes detailed AI feedback content.

### 7. Submit Job Application (`httpRequest` node)  
- **Authentication:** OAuth2  
- **URL:** Dynamically set by `jobPortalUrl` input  
- **Method:** POST  
- **Headers:** Content-Type: application/json  
- **Body Parameters:**  
  - applicantName  
  - applicantEmail  
  - resumeText  
  - coverLetter  
- **Purpose:** Submits the optimized application data to the employer’s job portal API endpoint.

---

## Input Data Requirements

- **Resume File:** Supported formats (e.g., PDF, DOCX converted to text) containing the applicant’s resume.  
- **Job Description File:** Text or document file containing detailed job description.  
- **Applicant Information:**  
  - `applicantName` (string)  
  - `applicantEmail` (string)  
  - `jobPortalUrl` (string): API endpoint URL for submitting application.

---

## How to Use

1. Upload your resume and the relevant job description files.  
2. Ensure the applicant name, email, and job portal URL are properly set in the workflow input or environment.  
3. Execute the workflow.  
4. Receive AI-generated resume feedback by email.  
5. Cover letter is generated and the optimized application is submitted directly to the job portal API.  

---

## Customization

- Modify email sender and recipient addresses in the **Send Application Email** node.  
- Adjust OpenAI model parameters or prompt in the **AI Analyze Resume vs Job Description** node for desired AI behavior.  
- Update HTTP request configuration to match the specific job portal API requirements.  
- Add support for additional input file formats or preprocessing as needed.  

---

## Notes

- The workflow assumes UTF-8 encoding for resume and job description files.  
- AI output depends on the OpenAI API availability and quota.  
- Ensure OAuth2 credentials and permissions are correctly configured for the job portal API.  

---

This workflow streamlines the job application process by integrating smart AI-powered resume analysis, actionable feedback, automatic cover letter generation, and direct job submission, providing a competitive edge to applicants.