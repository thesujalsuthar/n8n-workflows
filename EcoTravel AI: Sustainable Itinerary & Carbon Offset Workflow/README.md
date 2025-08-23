# AI-Powered Sustainable Travel Itinerary Planner and Carbon Offset Tracker

This workflow leverages AI and external APIs to generate a sustainable travel itinerary, estimate its carbon footprint, and facilitate carbon offset purchases. It helps users plan eco-friendly trips by suggesting sustainable transportation, accommodations, and activities, while also promoting carbon offsetting to minimize environmental impact.

---

## Workflow Overview

The workflow consists of the following key steps:

1. **Start**  
   The entry point of the workflow.

2. **Set Travel Input**  
   Accepts user input including travel destination, dates, preferences (`travelDetails`), and user information (`userInfo`).

3. **Generate Sustainable Itinerary (OpenAI GPT-4)**  
   Uses OpenAI GPT-4 to generate a detailed itinerary in JSON format featuring:
   - Transport options (including sustainability notes)  
   - Eco-friendly accommodations  
   - Sustainable activities  
   
   The prompt instructs the model to return the itinerary as a JSON object with `transport`, `accommodation`, and `activities` sections, each including `name`, `description`, and `sustainability notes`.

4. **Parse Itinerary JSON**  
   Converts the text output from the OpenAI node into a JSON object to enable further processing.

5. **Calculate Carbon Footprint**  
   Sends the itinerary JSON (transport, accommodation, activities) to an external carbon footprint API via HTTP POST request to estimate total CO2 emissions (in kg).

6. **Set Carbon Footprint in Data**  
   Extracts the carbon footprint value (`carbon_kg_co2`) from the API response and sets it within the workflow data for downstream use.

7. **Generate Sustainability Summary and Offset CTA (OpenAI GPT-4)**  
   Creates a user-friendly sustainability summary and call-to-action (CTA) for purchasing carbon offsets, using:  
   - The itinerary details  
   - The calculated carbon footprint estimate  
   
   The cost to offset is calculated at $10 per 100 kg CO2. The output is plain text intended for user communication.

8. **Purchase Carbon Offset**  
   Sends a request to a carbon offset API to purchase offsets corresponding to the carbon footprint, including:
   - Offset kilograms (`offset_kg`)  
   - Calculated amount in USD (`amount_usd`)  
   - User information (`user_info`)  
   
   This API call finalizes the carbon offset purchase.

---

## Node Breakdown

### 1. Start
- Type: `start`  
- Role: Initializes the workflow.

### 2. Set Travel Input
- Type: `set`  
- Parameters:  
  - `travelDetails` (string): JSON string with user travel input (destination, dates, preferences).  
  - `userInfo` (string): User information for offset purchase.

### 3. Generate Sustainable Itinerary (OpenAI)
- Type: `openAi` (GPT-4)  
- Operation: Text completion with a crafted prompt to generate a sustainable itinerary as a JSON string based on user input (`travelDetails`).  
- Settings: temperature 0.7, max tokens 800.

### 4. Parse Itinerary JSON
- Type: `set`  
- Action: Parses the OpenAI text response into structured JSON using the expression:  
  ```js
  ={{$json["choices"][0]["message"]["content"]}}
  ```
  
### 5. Calculate Carbon Footprint
- Type: `httpRequest` (POST)  
- URL: `https://api.example.carbonfootprint.io/v1/calculate`  
- Body: Sends the structured JSON with transport, accommodation, and activities.  
- Auth: HTTP Basic Auth with user and password credentials.

### 6. Set Carbon Footprint in Data
- Type: `set`  
- Extracts carbon footprint numeric value from HTTP response JSON property:  
  ```js
  ={{$json.body.carbon_kg_co2}}
  ```  
- Stores as `carbonFootprint`.

### 7. Generate Sustainability Summary and Offset CTA (OpenAI)
- Type: `openAi` (GPT-4)  
- Operation: Text completion that synthesizes:  
  - The itinerary details,  
  - Carbon footprint estimate, and  
  - Offset cost calculation (`$10 per 100 kg`).  
- Output is plain text explaining sustainability and prompting offset purchase.

### 8. Purchase Carbon Offset
- Type: `httpRequest` (POST)  
- URL: `https://api.carbonoffsets.example.com/v1/purchase`  
- Body includes:  
  - `offset_kg` (carbon footprint from previous step)  
  - `amount_usd` (calculated offset cost)  
  - `user_info` (user details)  
- Auth: API key authentication.

---

## Inputs

- **travelDetails**: JSON string describing destination, travel dates, and user preferences related to sustainability.  
- **userInfo**: JSON string containing user identification and contact details required for the carbon offset purchase.

---

## Outputs

- Sustainable travel itinerary as a structured JSON with details on transport, accommodation, and activities including sustainability notes.  
- Carbon footprint estimate in kilograms of CO2.  
- User-friendly summary explaining the sustainability impact and encouraging carbon offset purchase.  
- Confirmation of carbon offset purchase through the carbon offset API.

---

## Credentials Required

- **OpenAI API**: For GPT-4 completion nodes (Itinerary generation and summary creation).  
- **Carbon Footprint API credentials**: HTTP Basic Auth (user/password) for footprint calculation.  
- **Carbon Offset API key**: API key authentication for offset purchase.

---

## Pricing Assumption for Carbon Offsets

- $10 USD per 100 kg CO2 offset.  
- Cost calculated by:  
  ``` 
  (carbonFootprint / 100) * 10
  ```

---

## How to Use

1. Provide `travelDetails` and `userInfo` as JSON inputs in the **Set Travel Input** node.  
2. Run the workflow to:  
   - Generate a sustainable itinerary.  
   - Calculate and set carbon footprint.  
   - Produce a sustainability summary and CTA.  
   - Optionally purchase carbon offsets.  
3. Connect further nodes or UI elements as needed to display the itinerary and sustainability information to the end user.

---

This workflow streamlines sustainable travel planning by combining AI-powered itinerary generation with real-time environmental impact assessment and carbon offsetting options.