# Job Listings Scraper Workflow

This workflow automates the process of scraping job listings from popular job boards and storing new jobs in a Google Sheet. It filters jobs based on specified criteria such as keywords, location, and minimum salary, ensuring only relevant listings are added for tracking.

---

## Workflow Overview

1. **Generate Job Board URLs**  
   Generates search URLs for job listings on Indeed and LinkedIn based on provided keywords.

2. **HTTP Request - Scrape**  
   Performs HTTP requests to fetch the raw HTML from the generated job board URLs.

3. **Parse and Filter Listings**  
   Parses the HTML response using the `cheerio` library to extract job details, then filters jobs by location and minimum salary.

4. **Google Sheets - Check Existing**  
   Checks if the job URL already exists in the Google Sheet to avoid duplicates.

5. **IF - New Job?**  
   Conditional check determines if the job is new (i.e., not found in the sheet).

6. **Google Sheets - Append New Job**  
   Appends new jobs to the Google Sheet with details like job title, location, salary, job URL, source, and status information.

---

## Node Details

### 1. Generate Job Board URLs

- **Type:** Function  
- **Purpose:**  
  Creates search URLs for Indeed and LinkedIn based on the `keywords` array (default: `['developer', 'engineer']`).

- **Output:**  
  An array of objects with job board URLs and their source names.

---

### 2. HTTP Request - Scrape

- **Type:** HTTP Request  
- **Purpose:**  
  Sends GET requests to the URLs generated to fetch the latest job listings in HTML format.

- **Headers:**  
  Sets a `User-Agent` header mimicking a modern web browser to reduce risk of blocks.

---

### 3. Parse and Filter Listings

- **Type:** Function  
- **Purpose:**  
  Parses the fetched HTML using `cheerio` to extract individual job postings from Indeed and LinkedIn.

- **Filtering Criteria:**  
  - Location: Defaults to `"Remote"` if not provided.  
  - Minimum Salary: Defaults to `$60,000` if not provided.  
  - Jobs not matching location or below the salary threshold are excluded.

- **Extracted Data:**  
  - Job Title  
  - Location  
  - Salary (parsed if available, else 0 for LinkedIn)  
  - Job URL  
  - Source (Indeed or LinkedIn)

---

### 4. Google Sheets - Check Existing

- **Type:** Google Sheets  
- **Operation:** Lookup  
- **Purpose:**  
  Checks the Google Sheet for existing entries with the same Job URL to prevent duplicates.

- **Lookup Column:** `"Job URL"`  
- **Credentials Required:** Google Sheets OAuth2

---

### 5. IF - New Job?

- **Type:** IF Condition  
- **Purpose:**  
  Routes the workflow based on whether the job already exists in the Google Sheet.

---

### 6. Google Sheets - Append New Job

- **Type:** Google Sheets  
- **Operation:** Append  
- **Purpose:**  
  Adds new job listings to the specified Google Sheet.

- **Sheet Specifications:**  
  - Replace `YOUR_GOOGLE_SHEET_ID` with your actual Google Sheet ID.  
  - Appends columns:  
    - Job Title  
    - Location  
    - Salary  
    - Job URL  
    - Source (Indeed/LinkedIn)  
    - Application Status (default: "Not Applied")  
    - Last Follow Up (blank initially)  
    - Next Follow Up Reminder (blank initially)

- **Credentials Required:** Google Sheets OAuth2

---

## Configuration & Usage

- **Keywords:** Set the job search keywords by editing the `keywords` array in the **Generate Job Board URLs** node's function code.  
- **Location and Minimum Salary:** Optional criteria can be provided in the JSON input to the **Parse and Filter Listings** node (`location` and `minSalary`).  
- **Google Sheets Setup:**  
  - Create a Google Sheet with columns matching the appended fields.  
  - Provide Google Sheets OAuth2 credentials.  
  - Replace the placeholder `YOUR_GOOGLE_SHEET_ID` in the append node with your actual Sheet ID.

---

## Notes

- The workflow currently supports Indeed and LinkedIn. Additional job boards can be added by expanding the URL generation and parsing logic.  
- LinkedIn salary data is typically unavailable; jobs from LinkedIn are filtered only by location.  
- Make sure to comply with the terms of service of each job board when scraping data.  
- Running this workflow regularly helps keep your job tracking sheet up-to-date with the latest relevant listings.