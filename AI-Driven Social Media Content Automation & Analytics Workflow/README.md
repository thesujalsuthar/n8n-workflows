# Auto-Publishing AI-Generated Social Media Content Calendar with Engagement Analytics

This n8n workflow automates the generation, scheduling, publishing, and performance tracking of social media posts based on trending topics and AI-generated content. The workflow utilizes OpenAI's GPT-4 for content generation and integrates with social media platforms and Google Sheets for analytics logging.

---

## Workflow Overview

1. **Generate Trending Topics**  
   Selects 3 random trending topics from a predefined list to focus post creation around current popular themes.

2. **Generate Social Media Post Ideas**  
   Uses GPT-4 to generate 3 creative and engaging social media post ideas per trending topic.

3. **Parse Post Ideas**  
   Parses the AI-generated output, handling JSON or plain text, converting it into individual social media post items.

4. **Create Post Schedule**  
   Schedules posts for three platforms (Twitter, Facebook, Instagram) starting at 9 AM on the current day, spacing posts by one-hour intervals per platform.

5. **Schedule Social Media Post**  
   Publishes or schedules posts on respective social media platforms based on the prepared schedule.

6. **Wait For Engagement**  
   Introduces a 1-hour waiting period post-publication to allow user engagement to accumulate.

7. **Get Engagement Analytics**  
   Retrieves engagement metrics (likes, comments, shares) for each post on their respective platforms from the time scheduled until one hour later.

8. **Calculate Engagement Score**  
   Calculates a weighted engagement score using:
   - Likes (weight 1)
   - Comments (weight 2)
   - Shares (weight 3)

9. **Log Analytics to Google Sheets**  
   Appends collected analytics data and engagement scores to a designated Google Sheets spreadsheet for record-keeping and analysis.

10. **End**  
    Terminates the workflow gracefully.

---

## Nodes Detailed Description

### Generate Trending Topics  
- **Type:** Function  
- **Purpose:** Randomly selects 3 topics from a list of trending subjects.

### Generate Social Media Post Ideas  
- **Type:** OpenAI GPT-4  
- **Prompt:** Requests 3 engaging post ideas based on each selected trending topic.  
- **Settings:** max_tokens=300, temperature=0.7

### Parse Post Ideas  
- **Type:** Function  
- **Purpose:** Parses AI-generated output, supports JSON array parsing with fallback to line-by-line splitting.

### Create Post Schedule  
- **Type:** Function  
- **Purpose:** Creates a scheduled posting timeline for Twitter, Facebook, and Instagram posts spaced evenly starting at 9 AM.

### Schedule Social Media Post  
- **Type:** Social Media Node  
- **Operation:** Posts or schedules content on specified platforms using post text and scheduled time.

### Wait For Engagement  
- **Type:** Wait  
- **Duration:** 1 hour pause to allow engagement metrics to accumulate.

### Get Engagement Analytics  
- **Type:** Social Media Analytics  
- **Operation:** Fetches likes, comments, shares from platform analytics between scheduled post time and one hour after.

### Calculate Engagement Score  
- **Type:** Function  
- **Formula:** `engagementScore = likes + (comments * 2) + (shares * 3)`

### Log Analytics to Google Sheets  
- **Type:** Google Sheets  
- **Operation:** Appends post data and engagement metrics to a specified sheet in user’s Google Sheets account.  
- **Sheet Details:**  
  - Sheet ID: Replace `"YOUR_GOOGLE_SHEET_ID"` with your actual Google Sheet ID  
  - Range: `"Engagement!A1"`  
  - Value Input Mode: `"USER_ENTERED"`

### End  
- **Type:** No Operation  
- **Purpose:** Marks completion of the workflow.

---

## Prerequisites and Setup

- **n8n Instance:** Running and accessible.  
- **OpenAI API:** API key configured for GPT-4 access.  
- **Social Media API Credentials:** Connected accounts for Twitter, Facebook, and Instagram with post and analytics permissions enabled.  
- **Google Sheets API:** Credentials with access to a Google Sheet to log engagement data. Replace `"YOUR_GOOGLE_SHEET_ID"` in the Google Sheets node with your actual sheet ID.

---

## Usage

1. Import this workflow JSON into your n8n instance.  
2. Update node parameters as needed (e.g., Google Sheets ID).  
3. Ensure all credentials are properly set up and connected.  
4. Activate the workflow to start automated topic-driven social media publishing and analytics logging.

---

## Notes

- The engagement waiting period (1 hour) can be adjusted based on the typical engagement timeline of your audience.  
- Trending topics list can be customized or enhanced by integrating a real-time trends API.  
- This workflow supports extending platforms or analytics by adding or modifying nodes accordingly.

---

## License

This workflow is provided as-is for automation and enhancement in social media content marketing using AI and analytics. Feel free to modify to fit your needs.