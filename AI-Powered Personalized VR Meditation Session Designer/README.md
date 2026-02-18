# Adaptive AI-Driven VR Meditation Environment Designer

## Overview

This workflow creates a personalized VR meditation environment by adapting to user preferences and previous session data using an AI-driven recommendation engine. It allows users to select meditation themes, ambient sounds, and session durations, while logging session data to enable continuous improvement of recommendations.

---

## Workflow Components

### 1. User Input  
- Triggered manually to start the workflow.  
- Initiates the selection process for the meditation environment.

### 2. Select Meditation Theme  
- Offers a predefined list of meditation themes:  
  - Zen Garden  
  - Forest  
  - Beach  
  - Mountain  
  - Space  

### 3. Select Ambient Sound  
- Allows selection of ambient sound options:  
  - Rain  
  - Ocean Waves  
  - Birdsong  
  - Wind Chimes  
  - Ambient Drone  

### 4. Select Duration (minutes)  
- Lets users define the meditation session duration between 5 and 120 minutes in 5-minute increments, defaulting to 15 minutes.

### 5. Fetch Previous Sessions  
- Queries the MongoDB `meditation_sessions` collection to fetch the last 50 meditation session records.  
- Used as input data for the AI recommendation.

### 6. Prepare Session Data for AI  
- Extracts theme, ambient sound, and duration data from fetched sessions to prepare for AI analysis.

### 7. AI Recommendation Engine  
- Analyzes past session data to determine the most frequently used meditation theme and ambient sound.  
- Provides recommended defaults if no prior data is found (`Zen Garden` theme and `Rain` sound).  
- Helps customize the session when no explicit user input is provided.

### 8. Generate VR Session  
- Creates a meditation session object using user input or AI recommendations.  
- Assigns a unique `sessionId` and a start timestamp.

### 9. Session Start Trigger  
- Listens for custom `session.start` events (e.g., external VR app notifications).  
- Connects back to session logging.

### 10. Session End Trigger  
- Listens for custom `session.end` events signaling session completion.  
- Triggers update of session data with end time and status.

### 11. Log Session Start  
- Inserts a new document in the `meditation_sessions` MongoDB collection with session metadata and `status: started`.  
- Fields logged: `theme`, `ambientSound`, `duration`, `sessionId`, `startTime`, and `status`.

### 12. Log Session End  
- Updates the existing session document by `sessionId` once the meditation session concludes.  
- Adds `endTime`, updates `status` to `completed`, and logs the actual `durationCompleted`.

---

## Data Storage

- All session data is stored in a MongoDB collection named `meditation_sessions`.
- Session records include theme, ambient sound, duration, start and end timestamps, session identifiers, and status.

---

## Credentials

- MongoDB connection configured with credentials named **MongoDB Account**.

---

## Usage Flow

1. The user triggers the workflow manually (`User Input` node).
2. The user selects their preferred meditation theme, ambient sound, and session duration.
3. The workflow fetches past sessions and AI analyzes preferences to provide recommendations.
4. A meditation session is generated using either user choices or AI recommendations.
5. Session start events log the beginning of the session in MongoDB.
6. Session end events log session completion and session stats.
7. Over time, the AI recommendation engine refines suggestions based on accumulated user session data.

---

## Notes

- The AI recommendation engine uses simple frequency analysis on past sessions to personalize theme and sound selections.
- Session triggers can also be externally sent to control session logging asynchronously.
- Durations, themes, and ambient sounds are configurable in the respective nodes.
- The workflow ensures data persistence and tracking for continuous user experience enhancement.