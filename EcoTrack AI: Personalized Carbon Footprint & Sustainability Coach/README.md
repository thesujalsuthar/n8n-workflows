# AI-Driven Carbon Footprint Tracker and Sustainable Lifestyle Coach

This workflow leverages AI and real-time environmental data to calculate a user's carbon footprint, generate personalized sustainability advice, track user goals, and share updates on social media. It integrates multiple APIs and services, delivering actionable insights for a more sustainable lifestyle.

---

## Workflow Overview

### 1. Start
- The workflow initiates automatically or manually to begin data collection.

### 2. Collect User Data
- Gathers three primary categories of user input data:
  - **Personal Activity:** Daily habits and activities impacting carbon emissions.
  - **Transportation Data:** Modes and distances of travel.
  - **Consumption Patterns:** Consumption habits including energy use, diet, and shopping.
- Inputs are expected as JSON objects.

### 3. Calculate Carbon Footprint
- Sends user data to the Carbon Footprint API (`https://api.carbonfootprintapi.com/calculate`) via an HTTP POST request.
- The API returns a detailed calculated carbon footprint based on provided activities, transportation, and consumption.

### 4. Fetch Real-Time Environmental Data
- Retrieves current environmental impact data from `https://api.environmentalimpact.io/v1/real-time-data` using an HTTP GET request.
- This real-time contextual data includes relevant environmental metrics for enhanced advice personalization.

### 5. Prepare AI Prompt
- Combines the carbon footprint data and real-time environmental data into a single prompt.
- The prompt requests the AI to provide personalized, actionable sustainability advice and achievable goals based on the user’s footprint and environmental context.

### 6. Generate Sustainability Advice
- Uses OpenAI GPT-4 model to generate personalized advice.
- Model parameters:
  - Temperature: 0.7 (creative yet informative responses)
  - Max tokens: 500
  - Stop sequences: newline
- Input prompt is the combined data from the previous function node.

### 7. Store User Goals and Advice
- Upserts the data into a MongoDB collection named `userGoals`.
- Fields stored:
  - `userId`: Identifier for the user (defaults to `defaultUser` if none provided)
  - `carbonFootprint`: The carbon footprint results from the API
  - `sustainabilityAdvice`: AI-generated personalized advice
  - `lastUpdated`: Timestamp of the latest update

### 8. Post Update to Social Platform
- Publishes a summary post to a Twitter account using OAuth2 authentication.
- The post contains:
  - The user’s calculated total carbon footprint.
  - The AI-generated sustainability advice.
  - Relevant hashtags: #Sustainability #CarbonFootprint #EcoFriendly

---

## Node Details

| Node Name                      | Purpose                                               | Inputs                              | Outputs                             |
|-------------------------------|-------------------------------------------------------|-----------------------------------|-----------------------------------|
| Start                         | Begins the workflow                                   | None                              | Triggers user data collection     |
| Collect User Data              | Accepts and structures user inputs                    | userInputs JSON                   | Structured user data JSON         |
| Calculate Carbon Footprint     | Sends user data to external API for footprint calc. | User data JSON                   | Carbon footprint JSON             |
| Fetch Real-Time Environmental Data | Gets current environmental metrics                 | None                              | Real-time environmental data JSON |
| Prepare AI Prompt             | Creates AI input prompt combining footprint and environment data | Footprint & real-time data JSON   | Formatted prompt JSON             |
| Generate Sustainability Advice | Generates advice using OpenAI GPT-4                   | Prompt JSON                      | AI-generated sustainability advice |
| Store User Goals and Advice    | Saves results and advice to MongoDB                   | Advice & footprint JSON           | Confirmation of DB upsert         |
| Post Update to Social Platform | Shares post to Twitter                                | Advice JSON                      | Twitter status update confirmation |

---

## Prerequisites

- **n8n** workflow automation platform
- API access and keys for:
  - Carbon Footprint API (`https://api.carbonfootprintapi.com`)
  - Environmental Impact API (`https://api.environmentalimpact.io`)
  - OpenAI with GPT-4 model enabled
  - MongoDB instance with permissions to upsert data
  - Twitter API credentials with OAuth2 for posting updates

---

## Configuration Instructions

1. **User Data Input:**
   - Provide user-specific data via JSON inputs in the `Collect User Data` node.
   
2. **API Credentials:**
   - Configure relevant credentials for OpenAI, Twitter, and MongoDB in n8n.
   
3. **API Endpoints:**
   - Verify endpoints for Carbon Footprint and Environmental Impact APIs are active and accessible.

4. **Database Setup:**
   - Ensure MongoDB collection `userGoals` is created for storing user data.

---

## Workflow Execution

- On execution, the workflow collects user input data.
- Calculates the carbon footprint by invoking the relevant API.
- Fetches updated environmental data for context.
- Prepares a combined prompt to generate informed sustainability advice.
- Stores the results and advice in MongoDB for tracking.
- Posts a summary update on Twitter to encourage sustainable lifestyle awareness.

---

## Notes

- The workflow defaults to a user ID of `defaultUser` if no user identifier is provided.
- AI-generated advice aims to be actionable and tailored, utilizing real-time data to enhance relevance.
- Social media posting is customizable; other platforms can be integrated similarly.
- The workflow is designed for extension with additional data sources or personalized coaching features.

---

## License

This workflow is provided as-is. Ensure compliance with all API usage policies and data privacy regulations when deploying.