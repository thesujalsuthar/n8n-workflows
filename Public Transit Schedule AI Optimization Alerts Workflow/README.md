# Public Transit AI Optimization Workflow

This workflow automates the process of fetching public transit schedule data, optimizing routes using an AI model, preparing commuter alerts, and sending these alerts via email and Telegram.

---

## Workflow Overview

The workflow consists of the following nodes connected sequentially:

1. **Fetch Transit Data**  
   Retrieves the latest public transit route schedules via an HTTP GET request from a public API (`https://api.publictransitdata.com/routeschedules`). The response is expected in JSON format.

2. **Process Transit Data**  
   A Function node that processes the fetched transit data. It extracts each route's ID, stops, and current schedule to prepare the data structure required for the AI optimization.

3. **AI Optimization**  
   A Python node simulating an AI model that optimizes the transit schedules. Currently, it acts as a placeholder that copies existing schedules but sets the structure for future AI-based time optimizations.

4. **Prepare Commuter Alerts**  
   A Function node that generates alert messages based on the optimized routes. These alerts contain route IDs and the updated departure times formatted into user-friendly messages.

5. **Send Email Alerts**  
   Uses the Email Send node to email the prepared commuter alerts. The recipient email is dynamically set with a fallback default (`commuter@example.com`). Subject lines and message bodies include route-specific information.

6. **Send Telegram Alerts**  
   Sends the commuter alerts via Telegram messaging to a predefined chat ID (`-123456789`) for real-time commuter notifications.

---

## Node Details

### 1. Fetch Transit Data
- **Type:** HTTP Request  
- **Method:** GET  
- **URL:** `https://api.publictransitdata.com/routeschedules`  
- **Response Format:** JSON  
- **Purpose:** Obtain the current timetable and stops for each transit route.

### 2. Process Transit Data
- **Type:** Function (JavaScript)  
- **Purpose:** Extracts relevant data fields (`id`, `stops`, `schedule`) from the API response to simplify inputs for optimization.

```js
const transitData = items[0].json;

const processedData = {
  routes: transitData.routes.map(route => ({
    id: route.id,
    stops: route.stops,
    schedule: route.schedule
  }))
};

return [{ json: processedData }];
```

### 3. AI Optimization
- **Type:** Python  
- **Purpose:** Simulates an AI algorithm to optimize route schedules. Placeholder logic currently replicates input schedules unchanged.

```python
import json

input_data = json.loads(items[0]['json'].get('routes', '[]'))

optimized_routes = []
for route in input_data:
    optimized_schedule = []
    for time in route['schedule']:
        optimized_time = time  # placeholder for AI-based time adjustments
        optimized_schedule.append(optimized_time)
    optimized_routes.append({
        'id': route['id'],
        'optimized_schedule': optimized_schedule
    })

return [
    {
        'json': {
            'optimizedRoutes': optimized_routes
        }
    }
]
```

### 4. Prepare Commuter Alerts
- **Type:** Function (JavaScript)  
- **Purpose:** Creates alert messages for each route with optimized schedule details.

```js
const optimizedRoutes = items[0].json.optimizedRoutes;

const alerts = optimizedRoutes.map(route => {
  return {
    routeId: route.id,
    message: `Route ${route.id} schedule optimized. Next departure times: ${route.optimized_schedule.join(", ")}`
  };
});

return alerts.map(alert => ({ json: alert }));
```

### 5. Send Email Alerts
- **Type:** Email Send  
- **To:** Dynamic, defaults to `commuter@example.com`  
- **Subject:** Includes route ID (e.g., "Public Transport Update for Route X")  
- **Message:** Commuter alert message generated in the previous node.

### 6. Send Telegram Alerts
- **Type:** Telegram  
- **Chat ID:** `-123456789` (predefined group or channel)  
- **Message:** Same commuter alert message sent via Telegram for instant notification.

---

## Connections Flow

`Fetch Transit Data` → `Process Transit Data` → `AI Optimization` → `Prepare Commuter Alerts` → { `Send Email Alerts`, `Send Telegram Alerts` }

---

## Usage Instructions

1. Import this workflow into your n8n environment.
2. Configure email credentials and Telegram bot credentials in n8n settings as required.
3. Adjust the Telegram `chatId` and fallback email address if needed.
4. Activate the workflow to start periodic or manual executions.
5. Monitor sent alerts to ensure commuters receive real-time schedule optimizations.

---

## Notes

- Currently, the AI optimization node contains placeholder logic. Replace it with real AI models or algorithms as required.
- Ensure network access to the transit data API and external email/Telegram services is configured properly.
- Customize alert message templates to match your organization's communication style.

---