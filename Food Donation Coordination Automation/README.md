# Food Donation Coordination Workflow

This workflow automates the process of managing food donations by fetching available donations, standardizing the data, filtering out expired items, matching donations to appropriate local nonprofits, notifying them, scheduling pickups and deliveries, and creating calendar events to streamline coordination.

---

## Workflow Overview

### Nodes Summary

1. **Get Food Availability**  
   - Method: GET  
   - Endpoint Path: `/food-availability`  
   - Retrieves a list of available food donations in JSON format from an external API.

2. **Standardize Food Data**  
   - Function Node  
   - Transforms raw donation data into a standardized format to unify various data sources and simplify further processing.  
   - Standardized fields include: `id`, `type`, `quantity`, `expiryDate`, `location`, and `donorContact`.

3. **Filter Expired Food**  
   - If Node  
   - Filters out donations that have already expired based on the current date and the donation’s expiry date.

4. **Get Local Nonprofits**  
   - Method: GET  
   - Endpoint Path: `/local-nonprofits`  
   - Fetches a list of local nonprofits eligible to receive food donations, including their preferences and service areas.

5. **Match Donations to Nonprofits**  
   - Function Node  
   - Matches available non-expired food donations to nonprofits that accept the food type and serve the donation’s location.  
   - Outputs matches containing donation and nonprofit details.

6. **Send Notification to Nonprofit**  
   - Email Send Node  
   - Sends an email notification to the matched nonprofit informing them about the available donation with relevant details and a request to arrange pickup.

7. **Schedule Pickup and Delivery**  
   - Function Node  
   - Automatically schedules a pickup time one day after the current date and sets the delivery two hours after pickup. This is placeholder logic and can be customized.

8. **Create Pickup Calendar Event**  
   - Google Calendar Node  
   - Creates a calendar event for the scheduled pickup with the donation and nonprofit details.  
   - Requires valid Google Calendar OAuth2 credentials.

9. **Create Delivery Calendar Event**  
   - Google Calendar Node  
   - Creates a calendar event for the scheduled delivery, starting two hours after the pickup event.  
   - Requires valid Google Calendar OAuth2 credentials.

---

## Detailed Workflow Steps

### 1. Get Food Availability
Fetches donation data using an HTTP GET request. The data typically consists of multiple donation entries with varying field structures.

### 2. Standardize Food Data
Transforms each donation entry into a normalized object with a consistent schema to handle variations in API responses such as:

```js
{
  id,
  type,
  quantity,
  expiryDate,
  location,
  donorContact
}
```

This ensures the downstream workflow works with predictable data.

### 3. Filter Expired Food
Checks the expiry date of each donation and only passes along those with expiry dates in the future.

### 4. Get Local Nonprofits
Fetches nonprofits from another service, which includes their `accepted_food_types`, `service_areas`, and contact info.

### 5. Match Donations to Nonprofits
For each filtered donation, this node iterates over all nonprofits and finds matches based on:

- Nonprofit accepts the food type (`accepted_food_types` includes donation `type`)
- Nonprofit serves the donation’s location (`service_areas` includes the donation’s location)

Matched pairs are packaged with relevant details.

### 6. Send Notification to Nonprofit
An email is sent to the nonprofit’s contact email informing them about the new donation available for pickup, including:

- Food type
- Quantity
- Instructions for pickup arrangement

The email sender is `donations@localfoodsystem.org`.

### 7. Schedule Pickup and Delivery
Generates scheduled timestamps:

- Pickup: 1 day from the current date/time
- Delivery: 2 hours after the pickup

This logic is a placeholder and can be enhanced with real scheduling algorithms and integration with logistical data.

### 8. Create Pickup Calendar Event
Adds a calendar event to the connected Google Calendar with:

- Event summary: `Food Donation Pickup: {donationId} for {nonprofitId}`
- Event time: From scheduled pickup start to 30 minutes later
- Description: Notes for coordination

Requires OAuth2 credentials with calendar access.

### 9. Create Delivery Calendar Event
Creates a second calendar event for delivery with similar parameters:

- Event summary: `Food Donation Delivery: {donationId} for {nonprofitId}`
- Event time: From scheduled delivery start to 60 minutes later
- Description: Delivery coordination notes

---

## Credentials Required

- **Google Calendar OAuth2 API credentials** to create calendar events.
- Email sending credentials configured within the email node.

---

## Notes

- The workflow assumes the external APIs for food availability and local nonprofits return JSON data in specified formats.
- Scheduling logic is basic and should be adapted for production use.
- Email and calendar integrations require proper setup of authentication and permissions.
- Modify and extend the matching logic based on specific business requirements.

---

## Contact

For further customization or troubleshooting, please reach out to the workflow maintainer or the team managing the local food system integrations.