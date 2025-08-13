# User Sustainability Workflow

This workflow collects and analyzes user habits to estimate their carbon footprint, generates personalized sustainability tips, tracks progress over time, and sends motivational messages via Telegram.

---

## Workflow Overview

### Nodes Description

1. **Fetch User Habits**  
   - **Type:** HTTP Request  
   - **Authentication:** OAuth2  
   - **Operation:** Get user data (all records)  
   - **Purpose:** Retrieves user habits and preferences from an external API or service.

2. **Analyze Habits**  
   - **Type:** Function  
   - **Purpose:**  
     - Extracts relevant habits and preferences from the fetched data.  
     - Calculates a simple carbon footprint proxy based on transportation, diet, and energy use habits.  
   - **Calculation Example:**  
     - Transportation: `car` = 10, else 3  
     - Diet: `meat` = 5, else 2  
     - Energy Use: `high` = 7, else 3

3. **Generate Tips**  
   - **Type:** Function  
   - **Purpose:** Provides personalized sustainability tips depending on the calculated carbon footprint value:  
     - High footprint (>15): Strong recommendations including using public transit, reducing meat, and lowering energy use.  
     - Medium footprint (>8): Moderate suggestions such as reducing car trips, vegetarian meals, and mindful energy consumption.  
     - Low footprint (≤8): Encouragement to maintain current eco-friendly habits.

4. **Send Recommendations**  
   - **Type:** Telegram Node  
   - **Credentials:** Telegram API  
   - **Purpose:** Sends generated sustainability tips as a Markdown-formatted message to the user's Telegram chat, based on their stored preferences (chat ID).

5. **Track Progress**  
   - **Type:** Function  
   - **Purpose:**  
     - Maintains a history of the user’s carbon footprint measurements by appending the current footprint with a timestamp into a `history` array within user data.  
     - Enables progress tracking over time.

6. **Generate Encouragement**  
   - **Type:** Function  
   - **Purpose:**  
     - Compares the latest carbon footprint to the previous one.  
     - Generates an encouraging message depending on whether the footprint has decreased, increased, or stayed the same.  
     - If no previous data, prompts the user to start their sustainability journey.

7. **Send Encouragement**  
   - **Type:** Telegram Node  
   - **Purpose:** Sends personalized encouragement messages as Markdown-formatted text to the same Telegram chat as above.

---

## Execution Flow

1. **Fetch User Habits** retrieves user data.  
2. The data flows into **Analyze Habits**, which extracts key details and computes the carbon footprint proxy.  
3. The output splits into two parallel processes:  
   - **Generate Tips**, which creates personalized sustainability advice, then sends it via **Send Recommendations**.  
   - **Track Progress**, which updates the user’s footprint history, then feeds into **Generate Encouragement** that formulates motivational messages, followed by **Send Encouragement** to deliver these messages on Telegram.

---

## Prerequisites

- Valid OAuth2 credentials configured for the HTTP request node.  
- Telegram API credentials set up in the Telegram node to enable sending messages.  
- User data API must provide habits and preferences including chat ID for Telegram messaging.

---

## Configurable Parameters

- **Chat ID**: Must be stored under `preferences.chat_id` to send Telegram messages correctly.  
- **Carbon Footprint Thresholds:** Can be adjusted inside the functions to tailor sustainability advice criteria.  
- **Message Formatting:** Markdown mode is enabled for rich text styling in Telegram messages.

---

## Notes

- The carbon footprint calculation is a simplified proxy and should be adapted for more accuracy if needed.  
- Habit data structure is expected to include keys such as `transportation`, `diet`, and `energy_use`.  
- The workflow stores a progress history inline within the user data for easy improvement tracking.

---

This workflow empowers users to better understand and reduce their environmental impact through data-driven insights and motivational support delivered directly to their Telegram chat.