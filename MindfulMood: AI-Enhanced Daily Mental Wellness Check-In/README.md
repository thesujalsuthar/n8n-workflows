# AI-Powered Mental Wellness Daily Sentiment Check-In

This workflow enables a daily mental wellness check-in by capturing user input, analyzing sentiment using AI, tracking trends over a week, and delivering personalized supportive messages via email. It leverages n8n automation with OpenAI and a PostgreSQL database to empower users to monitor and improve their mental health.

---

## Workflow Overview

1. **User Input - Daily Entry**  
   A manual trigger prompts the user to share how they are feeling today in a few sentences.

2. **AI Sentiment Analysis**  
   The user’s input text is sent to OpenAI’s text analysis API to detect the sentiment (e.g., positive, negative, neutral).

3. **Set Date, Entry, and Sentiment**  
   Separate nodes extract and assign the current date, original user entry, and sentiment analysis results for easy reference and storage.

4. **Store Daily Entry**  
   The date, user entry, and detected sentiment are upserted into a PostgreSQL table named `mental_wellness_entries`. This ensures one entry per day is maintained or updated.

5. **Fetch Last 7 Days Sentiment**  
   Retrieves sentiment data from the past week from the database to analyze the user's mental wellness trend.

6. **Analyze Mental Health Trend**  
   This function processes the last 7 days' sentiment data, assigning numeric scores (positive = 1, negative = -1, neutral = 0), and calculates an average score to determine the trend:  
   - Improving (average > 0.3)  
   - Declining (average < -0.3)  
   - Neutral (otherwise)

7. **Generate Personalized Resource**  
   Based on the calculated trend, this node generates tailored advice and resources to support the user’s mental wellness.

8. **Generate Supportive Message**  
   Using OpenAI GPT-4 chat completion, the workflow builds a compassionate, empathetic summary message. It includes the user’s entry, sentiment, weekly trend, and personalized recommendations to encourage the user (approx. 100 words).

9. **Send Daily Email Summary**  
   The supportive daily message is sent via email using SMTP, providing the user with ongoing motivation and mental wellness insights.

---

## Nodes Detailed Description

### 1. User Input - Daily Entry (Manual Trigger)  
- Prompts user with:  
  `"Welcome to your daily mental wellness check-in! Please share how you're feeling today in a few sentences:"`

### 2. AI Sentiment Analysis (OpenAI)  
- Uses OpenAI API to analyze text sentiment of the user's daily entry.

### 3. Set Date (Set Node)  
- Extracts current date (`YYYY-MM-DD`) to associate with the entry.

### 4. Set Entry (Set Node)  
- Captures the exact user input to store in the database.

### 5. Set Sentiment (Set Node)  
- Extracts the sentiment result from the AI analysis response.

### 6. Store Daily Entry (PostgreSQL)  
- Upserts the record into `mental_wellness_entries` using date as the unique key, storing entry and sentiment.

### 7. Fetch Last 7 Days Sentiment (PostgreSQL)  
- Queries all entries from the last 7 days, ordered by date ascending.

### 8. Analyze Mental Health Trend (Function)  
- Maps sentiment strings to numeric scores, calculates average score, and determines overall trend.

### 9. Generate Personalized Resource (Function)  
- Generates a customized recommendation based on the identified trend: improving, declining, or neutral.

### 10. Generate Supportive Message (OpenAI GPT-4 Chat)  
- Composes an empathetic and uplifting summary message, referencing user input, sentiment, trend, and recommendations.

### 11. Send Daily Email Summary (Email Send)  
- Sends the generated supportive message to the user’s email address. Default or dynamic user email can be set.

---

## Credentials Required

- **OpenAI API** for text sentiment analysis and chat completion.  
- **PostgreSQL Database** for storing and querying user entries.  
- **SMTP Email Server** for sending daily summary emails.

---

## Database Schema Example

```sql
CREATE TABLE mental_wellness_entries (
  date DATE PRIMARY KEY,
  entry TEXT NOT NULL,
  sentiment VARCHAR(20) NOT NULL
);
```

---

## Usage Notes

- Ensure the PostgreSQL database and `mental_wellness_entries` table are properly set up.  
- Configure OpenAI API credentials with appropriate permissions for text and chat completions.  
- Set up SMTP credentials and verify email sending capabilities.  
- User input is taken manually; can be adapted to other input triggers as needed.

---

This workflow provides a scalable, automated framework for daily mental wellness tracking, leveraging AI-driven sentiment analysis and compassionate messaging to promote emotional health.