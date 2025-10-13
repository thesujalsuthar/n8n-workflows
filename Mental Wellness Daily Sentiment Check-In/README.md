# Mental Wellness Daily Check-In Workflow

This n8n workflow is designed to perform a daily mental wellness check-in with users, analyze their responses for sentiment and mood, generate personalized coping suggestions, and send supportive emails. It also escalates alerts when users indicate severe distress and securely stores a summary of the user’s input for further review.

---

## Workflow Overview

### Trigger

- **Daily Trigger**: Initiates the workflow every day at 9:00 AM (based on polling interval and trigger time settings).

### User Interaction

- **Mental Wellness Check-in Prompt**: Asks the user a series of 4 text-based questions to gauge their current mental wellness:
  1. How are you feeling today?
  2. Are you experiencing any stress or anxiety right now?
  3. On a scale from 1 to 10, how would you rate your current mood?
  4. Would you like some suggestions for coping strategies or self-care activities today?

### Data Processing

- **Combine Responses**: Aggregates all user responses into a single combined text block for analysis.
  
- **AI Sentiment Analysis**: Uses an OpenAI GPT-based model (`gpt-4o-mini`) to perform sentiment analysis on the combined user responses, returning scores for positive, negative, mixed sentiments, and an overall sentiment score.

- **Summarize User Input**: Generates a concise summary of the user's input text using the same AI model.

- **Extract Summary Text**: Extracts and formats the summary result from the AI response.

### Analysis & Output

- **Analyze & Generate Suggestions**: 
  - Interprets the sentiment analysis results.
  - Detects signs of stress, anxiety, or low mood based on sentiment thresholds.
  - Generates personalized coping suggestions accordingly:
    - Deep breathing, mindfulness meditation, walks for stress/anxiety.
    - Reach out to friends, hobbies, uplifting music for low mood.
    - Default encouragement to maintain self-care if no issues detected.
  - Flags if escalation is necessary for severe negative mood (overall score < -0.7).

### Notifications

- **Send Personalized Support Email**: Sends an email to the user with personalized coping suggestions based on their responses.

- **Check Escalation Needed**: Condition node that checks if the user’s sentiment analysis results require escalation.

- **Send Escalation Email**: If flagged, sends an urgent alert email to a designated support contact for follow-up.

### Data Storage

- **Store Data Securely**: Saves a summarized text of the user's daily mental wellness check-in responses into a JSON file (`mental-wellness-data.json`) to ensure secure record keeping.

---

## Node Details

### 1. Daily Trigger (`scheduleTrigger`)

- Triggers workflow once daily at 9:00 AM.
- Polling interval set to 86,400,000 ms (24 hours).

### 2. Mental Wellness Check-in Prompt (`conversationalPrompt`)

- Asks four mental health check-in questions.
- Collects free-text user input.

### 3. Combine Responses (`function`)

- Combines all answers into one continuous string called `combinedText`.

### 4. AI Sentiment Analysis (`openai`)

- Analyzes combined text for sentiment using GPT-4o-mini model.
- Outputs detailed sentiment scores.

### 5. Analyze & Generate Suggestions (`function`)

- Parses sentiment results.
- Determines coping suggestions based on detected feelings.
- Flags an escalation alert if the overall negative sentiment is severe.

### 6. Send Personalized Support Email (`smtp`)

- Sends the user an email summarizing their responses and personalized suggestions.
- Email address placeholder: `USER_EMAIL_PLACEHOLDER` (replace with actual user email).

### 7. Check Escalation Needed (`if`)

- Boolean check for escalation condition (`escalateAlert` flag).

### 8. Send Escalation Email (`smtp`)

- Sends an urgent alert email to `support_contact@example.com` if severe issues are detected.

### 9. Summarize User Input (`openai`)

- Generates a summary text of the user’s entire check-in input.

### 10. Extract Summary Text (`function`)

- Extracts the plain text summary from the AI response.

### 11. Store Data Securely (`writeBinaryFile`)

- Writes the summary data to `mental-wellness-data.json` for archival.

---

## Setup Instructions

- Replace `"USER_EMAIL_PLACEHOLDER"` with the actual user email in the **Send Personalized Support Email** node.
- Replace `"support_contact@example.com"` with your real support contact email in the **Send Escalation Email** node.
- Configure SMTP credentials in the email nodes for sending emails.
- Ensure the OpenAI API is properly configured for the **AI Sentiment Analysis** and **Summarize User Input** nodes.
- Adjust time zone settings if necessary to align the 9 AM trigger time with the user’s local timezone.

---

## Usage Notes

- This workflow provides daily emotional support through personalized prompts and AI-powered sentiment analysis.
- Escalation alerts help connect users in critical distress with human support rapidly.
- Data storage ensures privacy and a history of wellness entries for trusted review.
- Modify the prompts and coping suggestions based on clinical or organizational requirements.

---

## License

This workflow is provided "as-is" for mental health support use and should be implemented with proper ethical considerations and privacy compliance.