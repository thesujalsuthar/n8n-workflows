# AI-Driven Community SkillSwap Scheduler & Automated Matchmaking System

## Overview
This workflow automates the process of managing a community-driven SkillSwap system. It starts by verifying if a specific topic exists in a GitHub repository. If the topic does not exist, it proceeds to register users' skills, match users based on their offered and desired skills within the same location, and schedule SkillSwap sessions accordingly.

---

## Workflow Nodes Description

### 1. Start
- The entry point of the workflow.

### 2. Check GitHub Repo Topics
- **Type:** GitHub node
- **Operation:** Lists the topics of a specified GitHub repository.
- **Parameters:**
  - Authentication: OAuth2
  - Resource: Repository
  - Operation: List
  - Owner: YourGitHubUsername (replace with your GitHub username)
  - Repository: YourRepoName (replace with your repository name)
  - Query: `"AI-Driven Community SkillSwap Scheduler & Automated Matchmaking System"`
- **Purpose:** To check if the repository already has the specific topic attached.

### 3. Check Topic Existence
- **Type:** Function
- **Function:** Checks if the topic `"AI-Driven Community SkillSwap Scheduler & Automated Matchmaking System"` exists in the repository topics.
- **Output:** Returns `{ topicExists: true }` if the topic is found, otherwise `{ topicExists: false }`.

### 4. Stop if Topic Exists
- **Type:** Function
- **Function:** Halts the workflow by throwing an error if the topic already exists, preventing duplicate processing.

### 5. Register User Skills
- **Type:** HTTP Request
- **Function:** Sends user data to an external SkillSwap registration API.
- **Method:** POST
- **URL:** `https://api.example.com/skillswap/registerUser`
- **Body:** JSON containing user `name`, `email`, `skillsOffered`, `skillsToLearn`, and `location`.
- **Authentication:** Generic credential type (replace with actual credential).

### 6. Match Users
- **Type:** Function
- **Function:** Processes the list of registered users and performs matchmaking based on:
  - Matching `skillsOffered` and `skillsToLearn`
  - Matching locations (exact match)
- **Logic:**
  - For each pair of users, checks if each can teach the other's desired skills at the same location.
- **Output:** An array of matched user pairs including matched skills and location.

### 7. Date & Time
- **Type:** Set
- **Purpose:** Sets the current date and time (`preferredTime`) in ISO string format to be used as the preferred session time.

### 8. Schedule SkillSwap Session
- **Type:** HTTP Request
- **Function:** Sends match information to an external API to schedule a SkillSwap session.
- **Method:** POST
- **URL:** `https://api.example.com/skillswap/scheduleSession`
- **Body:** JSON containing matched users' names, location, matched skills, and preferred time.
- **Authentication:** Generic credential type (replace with actual credential).

---

## Workflow Execution Flow

1. **Start** node activates the workflow.
2. **Check GitHub Repo Topics** retrieves repository topics.
3. **Check Topic Existence** inspects if the specific topic exists.
4. **Stop if Topic Exists** halts the workflow if the topic is present.
5. If topic is not found, **Register User Skills** sends user skill data to the external API.
6. **Match Users** runs matchmaking based on skills and location.
7. **Date & Time** sets the preferred session time.
8. **Schedule SkillSwap Session** posts the scheduled session details to the external API.

---

## Prerequisites
- GitHub OAuth2 credentials configured in n8n.
- Access to the external SkillSwap API with credentials.
- Replace placeholders (`YourGitHubUsername`, `YourRepoName`, API URLs) with your actual values.
- Input user data must include fields: `name`, `email`, `skillsOffered`, `skillsToLearn`, and `location`.

---

## Notes
- Location matching currently requires exact string matches; consider enhancing location proximity for real-world use.
- The workflow assumes an existing external SkillSwap API handling user registration and session scheduling.
- Errors during GitHub topic checking halt the process early to avoid redundancy.

---

## License
This workflow and its components are provided as-is without warranty. Modify and adapt it according to your project needs.