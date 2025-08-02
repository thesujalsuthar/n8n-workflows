# AI-Powered Local Event Attendance Optimizer and Networking Facilitator

## Overview

This workflow is designed to help professionals efficiently plan their attendance at local events and facilitate meaningful networking opportunities by leveraging AI technology. It aggregates local event data, optimizes event schedules based on user location and timing constraints, and suggests the top relevant attendees to connect with during these events based on professional interests.

---

## Workflow Components

### 1. User Profile Data

- **Type:** Function Node
- **Function:** Simulates retrieval of user data including:
  - `userLocation`: Latitude and longitude of the user (example: New York City coordinates).
  - `professionalInterests`: User’s area of professional interest (e.g., technology, software development, AI).
  - `preferredDateRange`: The date range in which the user wants to attend events (e.g., June 1 - June 30, 2024).

### 2. Fetch Local Events

- **Type:** HTTP Request Node
- **Function:** Fetches events from an external event API (`https://api.eventservice.com/v1/events`) using:
  - `location`: Derived dynamically from the user's location.
  - `category`: Matched to the user's professional interests.
  - `dateRange`: Based on the user's preferred date range.
- **Output:** List of events relevant to the user’s criteria.

### 3. Optimize Event Schedule

- **Type:** Function Node
- **Function:** Optimizes the user’s event attendance schedule to maximize event attendance efficiently by:
  - Sorting events by start time.
  - Using the Haversine formula to calculate distance between user location and event locations.
  - Employing a simple greedy algorithm to select non-overlapping events that fit the user’s timeline.
- **Output:** An optimized list of events including distance from the user for efficient attendance planning.

### 4. Suggest Networking Attendees

- **Type:** OpenAI Node (GPT-4o-mini model)
- **Function:** AI-powered assistant that:
  - Analyzes event details alongside user professional interests.
  - Suggests the top 5 relevant attendees to connect with at the events.
  - Provides details including name, role, company, and reasons for recommendations.
- **Input:** Events list + user professional interests.
- **Output:** Networking suggestions formatted as a structured response.

### 5. Compile Final Output

- **Type:** Function Node
- **Function:** Combines the optimized event schedule with AI-generated networking suggestions into a consolidated output.
- **Output:** JSON object containing:
  - `optimizedSchedule`: The finalized list of events tailored for attendance.
  - `networkingSuggestions`: AI-curated list of top recommended connections.

---

## Workflow Execution Flow

1. **User Profile Data** node provides user-specific parameters.
2. Parameters are passed to the **Fetch Local Events** node, which calls the external event API.
3. The retrieved events are sent simultaneously to:
   - **Optimize Event Schedule** for scheduling optimization.
   - **Suggest Networking Attendees** for generating networking recommendations.
4. Outputs from both nodes are merged in the **Compile Final Output** node to produce the final combined result.

---

## Input/Output Details

### Inputs:

- User profile details (`userLocation`, `professionalInterests`, `preferredDateRange`) — simulated or fetched dynamically.
- Event data fetched via API related to user inputs.

### Outputs:

- Optimized event schedule with distance metrics.
- AI-generated top 5 attendee recommendations with professional details and matchmaking reasons.

---

## Use Case

This workflow is perfect for professionals attending multiple events within a given period who want to:

- Maximize their event attendance without time conflicts.
- Prioritize nearby events to reduce travel time.
- Get personalized, AI-driven suggestions on who to network with for career growth or business opportunities.

---

## Requirements

- Access to an event listing API endpoint.
- OpenAI API access with GPT-4o-mini or compatible model enabled.
- Deployment environment compatible with n8n workflows.

---

## Customization

- Modify the API endpoint and query parameters in **Fetch Local Events** for different event sources.
- Adjust the user profile node to pull live user data from external sources or credential storage.
- Tune AI prompt in **Suggest Networking Attendees** to adapt networking recommendation style or criteria.

---

## Final Notes

Ensure your API keys and sensitive credentials are securely stored and accessible for the HTTP Request and OpenAI nodes. This workflow can be enhanced by incorporating real-time user feedback or event priorities for dynamic optimization in future iterations.