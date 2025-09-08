# AI-Powered Sustainable Travel Planner & Carbon Footprint Optimizer

A comprehensive workflow designed to assist users in planning sustainable travel itineraries by providing eco-friendly travel options, estimating carbon emissions, and recommending carbon offset programs. This workflow leverages HTTP triggers and HTTP requests integrated with custom functions to optimize users' travel plans for environmental impact.

---

## Workflow Overview

This workflow accepts travel preferences via an HTTP POST request and responds with optimized travel itineraries focused on sustainability, including carbon emission calculations and offset recommendations.

---

## Nodes Description

### 1. Start HTTP Trigger  
- **Type:** HTTP Trigger  
- **Method:** POST  
- **Path:** `/plan-itinerary`  
- **Purpose:** Entry point for the workflow that listens for incoming travel planning requests containing user preferences and itinerary details.

### 2. Parse Request  
- **Type:** Function  
- **Purpose:** Extracts and parses the user’s travel preferences and itinerary data from the incoming HTTP request body for further processing.

### 3. Fetch Travel Options  
- **Type:** HTTP Request (GET)  
- **Endpoint:** `/travel-options`  
- **Query Parameters:**  
  - `origin`: User's trip origin  
  - `destination`: User's trip destination  
  - `date`: Travel date  
  - `mode`: Returns all available travel modes  
- **Purpose:** Retrieves a list of available travel options including different transportation modes and details like distance.

### 4. Calculate Carbon Emissions  
- **Type:** Function  
- **Purpose:** Calculates the estimated carbon emissions for each travel option using predefined emission factors (in kg CO2 per passenger km) based on mode of transportation and distance.  
- **Emission Factors Used:**  
  - Flight: 0.255 kg CO2/km  
  - Train: 0.041 kg CO2/km  
  - Bus: 0.105 kg CO2/km  
  - Car: 0.192 kg CO2/km

### 5. Identify Best Eco-Friendly Option  
- **Type:** Function  
- **Purpose:** Sorts travel options by their carbon emissions and identifies the lowest emission option as the recommended itinerary. Also outputs all sorted options for reference.

### 6. Fetch Carbon Offset Programs  
- **Type:** HTTP Request (GET)  
- **Endpoint:** `/carbon-offset-programs`  
- **Purpose:** Retrieves a list of available carbon offset programs that users can contribute to in order to mitigate their travel emissions.

### 7. Generate Offset Recommendations  
- **Type:** Function  
- **Purpose:** Creates personalized recommendations for carbon offset contributions by scaling the user’s estimated emissions with each program’s offset factor. Provides program names, URLs, and suggested contribution amounts in kg CO2.

### 8. Prepare User Recommendations  
- **Type:** Function  
- **Purpose:**  
  - Compiles the best travel option and alternative options with slightly higher emissions but different travel modes.  
  - Adds useful sustainability tips to guide users towards greener travel habits.  
  - Combines the carbon offset recommendations into a comprehensive response.

### 9. Respond to User  
- **Type:** HTTP Response  
- **Status Code:** 200 OK  
- **Purpose:** Sends the final response back to the user containing:  
  - Best itinerary suggestion (lowest emissions)  
  - Alternative eco-friendly travel options  
  - Sustainability travel tips  
  - Recommended carbon offset programs and suggested contributions

---

## Data Flow Summary

1. User submits a travel plan request (origin, destination, date, etc.) via POST to `/plan-itinerary`.
2. The request data is parsed and passed to two branches simultaneously: 
   - Fetch travel options.  
   - Fetch carbon offset programs.
3. Travel options are annotated with estimated carbon emissions.  
4. The workflow identifies the lowest carbon emission travel option.  
5. Carbon offset programs are scaled based on emissions to generate contribution suggestions.  
6. Final recommendations are prepared, including itinerary suggestions, alternatives, sustainability tips, and offset programs.
7. The compiled response is returned to the user.

---

## Sustainability Tips Included
- Use public transportation whenever possible.  
- Pack light to reduce fuel consumption.  
- Choose non-stop flights if flying.  
- Consider traveling by train or bus for short to medium distances.  
- Offset your carbon emissions through verified programs.

---

## How to Use

- Make a POST request to the `/plan-itinerary` endpoint with a JSON body containing user travel preferences, such as:  
  ```json
  {
    "preferences": {
      "origin": "CityA",
      "destination": "CityB",
      "date": "YYYY-MM-DD"
    }
  }
  ```
- The response will include the best eco-friendly travel itinerary, alternative travel options, sustainability tips, and carbon offset program recommendations.

---

## Notes

- Emission calculations use general emission factors; customize factors as needed for accuracy.  
- Carbon offset programs and travel options endpoints must be accessible and return data in expected formats.  
- This workflow is designed to be modular and can be extended with more detailed analytics or integrations.

---

This workflow empowers users to make informed travel decisions that prioritize environmental responsibility while providing actionable recommendations to offset their carbon footprint effectively.