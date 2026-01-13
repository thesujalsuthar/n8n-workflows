# EcoJourney Workflow

This workflow automates the process of providing eco-friendly travel insights, sustainable accommodation suggestions, carbon footprint estimates, and personalized environmental tips to users based on their travel plans and destination.

---

## Workflow Overview

The workflow runs daily at 9:00 AM and performs the following steps:

1. **Trigger**: Initiates the workflow every day at 9:00 AM.
2. **Extract Location**: Extracts the travel destination from user input.
3. **Merge User Input**: Combines multiple user input parameters such as destination, travel dates, and preferences.
4. **Get Weather Forecast**: Retrieves current weather conditions for the destination.
5. **Fetch Environmental Data**: Collects ecological data related to the destination.
6. **Find Sustainable Accommodations**: Searches for eco-friendly lodging options matching the user's dates and location.
7. **Get Sustainable Activities**: Gathers suggestions for environmentally friendly activities at the destination.
8. **Calculate Carbon Footprint**: Estimates the carbon footprint based on travel distance.
9. **Generate Eco-Friendly Tips**: Creates personalized tips to minimize environmental impact tailored to the user's trip.
10. **Send User Update**: Sends a comprehensive summary message to the user via Telegram.

---

## Nodes Description

### 1. Daily Trigger  
- **Type**: Cron  
- **Schedule**: Every day at 9:00 AM  
- **Purpose**: Automatically starts the workflow.

### 2. Extract Location  
- **Type**: Function  
- **Purpose**: Extracts the destination from the incoming data to pass onto subsequent nodes.

### 3. Merge User Input  
- **Type**: Merge  
- **Operation**: Merge inputs by the key `location`  
- **Purpose**: Combines inputs such as destination, travel dates, and user preferences into a unified data object.

### 4. Get Weather Forecast  
- **Type**: OpenWeatherMap  
- **Operation**: Retrieves the current weather data based on destination.  
- **Outputs**: Weather description and temperature.

### 5. Fetch Environmental Data  
- **Type**: HTTP Request  
- **Purpose**: Calls an external ecological data API to fetch environmental information about the destination.  
- **Authentication**: Bearer token header (requires `YOUR_ENV_DATA_API_KEY`).

### 6. Find Sustainable Accommodations  
- **Type**: HTTP Request  
- **Purpose**: Searches for sustainable lodging options available at the travel location between specified check-in and check-out dates.  
- **Authentication**: API key header (`YOUR_SUSTAINABLE_LODGING_API_KEY`).

### 7. Get Sustainable Activities  
- **Type**: HTTP Request  
- **Purpose**: Retrieves suggested environmentally friendly activities for the destination on the start date.  
- **Authentication**: Bearer token header (`YOUR_GREEN_ACTIVITIES_API_KEY`).

### 8. Calculate Carbon Footprint  
- **Type**: Function  
- **Purpose**: Computes the estimated carbon footprint based on travel distance (default 10 km) and average emission factor (0.12 kg CO2/km for hiking).  
- **Output**: Carbon footprint value in kilograms of CO2.

### 9. Generate Eco-Friendly Tips  
- **Type**: Function  
- **Purpose**: Produces personalized eco-friendly travel tips based on:
  - Current weather conditions  
  - Carbon footprint estimate  
  - Availability of sustainable accommodations  
  - Availability of sustainable activities  

### 10. Send User Update  
- **Type**: Telegram  
- **Operation**: Sends a detailed message to the user’s Telegram chat containing:
  - Destination  
  - Weather conditions  
  - Estimated carbon footprint  
  - Recommended sustainable accommodations  
  - Recommended eco-friendly activities  
  - Custom tips to reduce environmental impact  
- **Authentication**: Telegram bot access (requires the user’s chat ID).

---

## Configuration

### Required API Keys and Tokens

- **Environmental Data API Key** (`YOUR_ENV_DATA_API_KEY`)  
  Needed in the *Fetch Environmental Data* node for authorization.
  
- **Sustainable Lodging API Key** (`YOUR_SUSTAINABLE_LODGING_API_KEY`)  
  Needed in the *Find Sustainable Accommodations* node.
  
- **Green Activities API Key** (`YOUR_GREEN_ACTIVITIES_API_KEY`)  
  Needed in the *Get Sustainable Activities* node.
  
- **Telegram Bot Token**  
  Set in the Telegram node to enable messaging.

### User Input Requirements

- `destination`: The target location for the journey (city, region, or coordinates).  
- `startDate` and `endDate`: Date range for travel to find accommodations and activities.  
- `preferences`: Optional user preferences related to eco-travel.

---

## How It Works

- Every day at 9:00 AM, the workflow triggers and extracts the user's travel destination.  
- User input data is merged for unified processing.  
- It fetches current weather, environmental information, lodging options, and activity recommendations.  
- The workflow estimates the carbon footprint based on the travel distance.  
- It then synthesizes eco-friendly travel tips tailored to the user’s trip conditions and preferences.  
- Finally, it sends a rich update message to the user's Telegram chat with all relevant information and sustainability advice.

---

## Customization

- **Scheduling**: Modify the *Daily Trigger* node timing as needed.  
- **API Endpoints and Keys**: Replace placeholder API keys with your actual credentials.  
- **Travel Distance**: The carbon footprint calculation distance can be adjusted in the *Calculate Carbon Footprint* node function.  
- **Tips Logic**: Modify or enhance the *Generate Eco-Friendly Tips* node to provide more personalized or location-specific suggestions.

---

## Summary

This EcoJourney workflow automates sustainable travel advisories, blending live data and personalized eco-tips, so you or your users can make environmentally responsible travel decisions daily.