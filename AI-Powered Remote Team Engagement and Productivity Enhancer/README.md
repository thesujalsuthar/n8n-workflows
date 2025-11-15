# AI-Driven Remote Team Engagement and Productivity Booster

This workflow leverages AI to analyze team communication and task completion data from multiple platforms (Slack, Microsoft Teams, Trello, and Asana) to generate actionable suggestions and motivational messages. The goal is to boost remote team engagement, mental well-being, and productivity by recommending context-aware micro-break activities and encouraging messages distributed through team communication channels.

---

## Workflow Overview

### Key Functionalities:
- Fetch recent channels and messages from Slack and Microsoft Teams.
- Perform sentiment analysis on team messages using OpenAI's AI models.
- Retrieve task data from Trello and Asana to calculate task completion rates.
- Generate tailored micro-break activity suggestions based on sentiment and task completion metrics.
- Compose motivational messages to uplift the team spirit.
- Post summarized insights, suggestions, and motivation daily to designated Slack and Microsoft Teams channels.

---

## Nodes and Process Flow

### 1. Slack Data Collection
- **Slack Get Channels**  
  Retrieves a list of channels from the specified Slack workspace (`teamId` required). Limits to 50 channels.
  
- **Slack Get Messages**  
  For each retrieved Slack channel, fetches up to 100 recent messages.
  
- **AI Sentiment Analysis**  
  Analyzes the sentiment of Slack messages using OpenAI's text analysis capabilities.

### 2. Trello Task Data
- **Trello Get Tasks**  
  Fetches tasks from a specified Trello board (`boardId` required).
  
- **Calculate Task Completion**  
  Filters and calculates the task completion rate based on Trello task statuses (`dueComplete` or `"done"` status considered complete).

### 3. Suggestion and Motivation for Slack
- **Suggest Activity & Motivation**  
  Combines Slack sentiment results and Trello task completion data to select an appropriate micro-break activity and motivational message.  
  - Negative sentiment or low completion rate (<50%): calming and refreshing activities.  
  - Neutral sentiment: light physical and mental break suggestions.  
  - Positive sentiment: celebratory and social suggestions.

- **Post Message to Slack Channel**  
  Sends a formatted daily summary containing sentiment analysis, task completion rate, suggested activity, and motivation to a predefined Slack channel (`team-updates`).

---

### 4. Microsoft Teams Data Collection
- **MS Teams Get Channels**  
  Retrieves all channels from a specified Microsoft Teams team (`teamId` required).

- **MS Teams Get Messages**  
  Retrieves up to 100 recent messages per channel from Microsoft Teams.

- **AI Sentiment Analysis MS Teams** (assumed node, similar to Slack AI Sentiment Analysis)  
  Performs sentiment analysis on Microsoft Teams messages.

### 5. Asana Task Data
- **Asana Get Tasks**  
  Retrieves tasks from a specified Asana project (`projectId` required).

- **Calculate Asana Task Completion**  
  Calculates Asana tasks' completion rate based on the `completed` property.

### 6. Consolidation and Suggestion for Microsoft Teams
- **Consolidate Suggestions and Motivation**  
  Combines sentiment analysis results from Microsoft Teams and average completion rates from Trello and Asana. Uses the same logic as Slack for activity and motivational message suggestions.
  
- **Post Message to MS Teams Channel**  
  Posts the daily engagement summary including sentiment, average completion rate, suggested micro-break activity, and motivational message to a specified Microsoft Teams channel (`general`).

---

## Required Credentials and Setup

- **Slack API Credential**  
  For accessing Slack workspace, channels, and messages.

- **OpenAI API Credential**  
  Required to perform sentiment analysis on team messages using OpenAI models.

- **Trello API Credential**  
  To pull task data from Trello boards.

- **Microsoft Teams API Credential**  
  To fetch channels and messages from Microsoft Teams.

- **Asana API Credential**  
  To retrieve and analyze task completion from Asana projects.

---

## Configuration Notes

- Replace placeholders such as:
  - `your-slack-team-id`
  - `your-trello-board-id`
  - `your-microsoft-teams-team-id`
  - `your-asana-project-id`
  
  with your actual workspace, board, team, and project identifiers.

- Slack and Microsoft Teams channels for posting messages (`team-updates` and `general`) should exist and allow bot posting.

- The OpenAI sentiment analysis nodes expect the message text structured correctly to generate meaningful results.

---

## How It Works

1. **Data Extraction**  
   Channels and recent messages are retrieved from Slack and Microsoft Teams. Simultaneously, task data is collected from Trello and Asana.

2. **Data Processing**  
   AI analyzes communication sentiment. Task data is processed to calculate the percentage of completed tasks.

3. **Generating Suggestions**  
   Based on the sentiment and task completion metrics, the workflow dynamically selects micro-break activities and motivational messages that fit the current team mood and workload.

4. **Daily Team Engagement Message**  
   The aggregated data and recommendations are posted back to Slack and Microsoft Teams channels to encourage wellness, productivity, and positive morale within remote teams.

---

## Benefits

- **Improves Remote Team Engagement:** By using AI to understand team sentiment and workload, it provides personalized, evidence-based recommendations for breaks and motivation.
- **Supports Mental Well-being:** Promotes mindfulness, stretching, hydration, gratitude, and social connection depending on real-time data insights.
- **Boosts Productivity:** Encourages balanced work-rest patterns aligned with current project progress and team mood.
- **Cross-Platform Compatibility:** Collects and consolidates data from multiple collaboration and task management platforms, suitable for diverse remote work setups.

---

## Troubleshooting

- Confirm all API credentials have the necessary permissions.
- Ensure proper access to all relevant channels and boards.
- Review node-specific error messages and validate limits on messages/tasks.
- Adjust limits or time windows as necessary for team size and activity level.

---

## License

This workflow and associated scripts are provided as-is for personal or organizational use. Modify and extend as needed to fit your team's specific communication and project management tooling.

---

End of README