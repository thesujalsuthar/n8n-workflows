# Sustainable Travel Carbon Footprint and Offset Planner

This workflow helps users estimate the carbon footprint of their travel plans based on selected parameters, suggests carbon offset options with estimated costs, provides personalized sustainability tips, processes user-selected carbon offset purchases, and generates a summary report saved to a file.

---

## Workflow Overview

The workflow consists of the following main steps:

1. **User Input** - Collect travel details from the user.
2. **Calculate Carbon Footprint** - Compute total CO2 emissions based on travel details.
3. **Suggest Carbon Offset Options** - Provide offset choices with pricing.
4. **Provide Sustainability Tips** - Offer personalized travel sustainability recommendations.
5. **Send User Summary** - Summarize calculated emissions, offset options, and tips to the user.
6. **Parse User Offset Selection** - Process user’s selected carbon offset option.
7. **Purchase Carbon Offset** - Execute the purchase via an external API.
8. **Generate Summary Report** - Create a detailed travel and offset summary.
9. **Save Report to File** - Append the summary report to a JSON file.

---

## Node Details

### 1. Start
- Initialized manually to trigger the workflow.

### 2. User Input (Manual Trigger)
- Collects user input with the following parameters:
  - **Trip Distance (km)**: Numeric input of travel distance.
  - **Transport Mode** (dropdown options):
    - Car (petrol)
    - Car (diesel)
    - Bus
    - Train
    - Domestic flight
    - International flight
    - Bicycle
    - Walking
  - **Trip Type** (dropdown options):
    - One-way
    - Return (round trip)
  - **Number of Travelers**: Numeric input for total travelers.

### 3. Calculate Carbon Footprint (Function Node)
- Calculates total emissions (kg CO2) using predefined emission factors (kg CO2/km) per transport mode:

  | Transport Mode          | Emission Factor (kg CO2/km) |
  |------------------------|-----------------------------|
  | Car (petrol)           | 0.192                       |
  | Car (diesel)           | 0.171                       |
  | Bus                    | 0.105                       |
  | Train                  | 0.041                       |
  | Domestic flight        | 0.254                       |
  | International flight   | 0.195                       |
  | Bicycle                | 0.0                         |
  | Walking                | 0.0                         |

- Factors in trip type (one-way or return) and number of travelers.
- Outputs total emissions and related variables.

### 4. Suggest Carbon Offset Options (Function Node)
- Provides a set of sample carbon offset choices:
  - **Plant Trees:** $10 per ton CO2
  - **Renewable Energy Credits:** $15 per ton CO2
  - **Methane Capture:** $20 per ton CO2

- Converts emissions from kg to tons and calculates the cost for each offset option.
- Outputs offset options with pricing for user consideration.

### 5. Provide Sustainability Tips (Function Node)
- Gives personalized sustainability tips tailored to the selected transport mode.
- Examples include carpooling, using fuel-efficient vehicles, supporting public transit, choosing direct flights, and safe bicycle use.
- Output is a list of tips based on transport mode.

### 6. Send User Summary (Set Node)
- Formats a textual summary message including:
  - Estimated carbon footprint (kg CO2)
  - Suggested offset options with prices
  - Personalized sustainability tips
- Designed to be sent to the user for decision-making.

### 7. Parse User Offset Selection (Function Node)
- Accepts user's selected carbon offset product ID.
- Matches the selection with available offset options and extracts relevant information such as price and product details.
- Throws error if selected product ID is invalid.
- Outputs selected offset details and confirmation message.

### 8. Purchase Carbon Offset (HTTP Request Node)
- Sends a POST request to a carbon offset provider API endpoint:
  - URL: `https://api.carbonoffsetprovider.com/purchase`
  - Payload includes:
    - `product_id`: Selected product ID
    - `amount_ton_co2`: Total emissions in tons
    - `payment_method`: Fixed as `"credit_card"`
    - `user_details`: Includes user name and email (default if not provided)
  - Authenticates via header authentication credentials.

- Handles the actual offset purchase process.

### 9. Generate Summary Report (Function Node)
- Creates a JSON report containing:
  - Current date/time
  - Transport mode
  - Number of travelers
  - Total emissions (kg CO2)
  - Selected offset option name
  - Offset cost (USD)
  - Summary message describing emissions and offset applied

### 10. Save Report to File (Write Binary File Node)
- Appends the generated summary report to a local JSON file named `carbon_footprint_reports.json`.
- Enables persistent logging of travel emission and offset records for future reference.

---

## How to Use

1. Trigger the workflow manually.
2. Provide trip details:
   - Distance in kilometers
   - Transportation mode
   - Trip type (one-way or return)
   - Number of travelers
3. Review the carbon footprint estimate and suggested offset options.
4. Receive tailored sustainability tips.
5. Choose a carbon offset option to purchase.
6. Confirm purchase through the connected carbon offset API.
7. Review the generated summary report saved locally.

---

## Requirements

- n8n environment with access to:
  - Function nodes for calculations and logic.
  - HTTP Request node with header authentication credentials for the carbon offset API.
  - Write binary file node with write access permissions.
- User input interface capable of collecting required trip data.

---

## Notes

- Emission factors are average values and may vary by region and vehicle specifics.
- Carbon offset prices are sample values and should be confirmed with actual providers.
- User details sent to the offset provider default to generic values if not supplied.
- The summary report is appended to a JSON file to keep a historical record of trips and offsets.

---

## License

This workflow template is provided as is without warranty. Modify and extend according to your needs.