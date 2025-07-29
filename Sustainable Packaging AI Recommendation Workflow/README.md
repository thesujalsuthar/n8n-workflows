# AI-Powered Sustainable Packaging Recommendation System

This workflow provides automated recommendations for sustainable packaging materials based on the product type and quantity provided by the user. It helps businesses select eco-friendly packaging options that balance environmental impact and cost-effectiveness, and delivers these recommendations via email.

---

## Workflow Overview

The workflow consists of the following nodes:

### 1. Cron Trigger
- **Type:** `cron`
- **Function:** Initiates the workflow on a scheduled basis.
- **Parameters:** Currently configured with no specific trigger times; manual or on-demand triggering is used.

### 2. Ask for Product Details
- **Type:** `manualTrigger`
- **Function:** Prompts the user to input critical details about the product.
- **Inputs:**  
  - **Product Type** (string): The category of the product (e.g., electronics, food, cosmetics, clothing, general).  
  - **Quantity** (number): The quantity of the product units (default: 1).

### 3. Recommend Packaging Materials
- **Type:** `function`
- **Function:** Processes the input product type and filters a predefined list of sustainable packaging materials suitable for that product.
  
  **Details:**  
  - Uses a hardcoded list of packaging materials containing:
    - Material name
    - Suitability for product types
    - Environmental impact score (1 = best, higher is worse)
    - Cost per unit (in USD)
  - Filters materials to those suitable for the given product type.
  - Ranks filtered materials using a weighted formula prioritizing environmental impact (70%) over cost (30%).
  - Returns the top 3 recommended materials.

### 4. Send Email
- **Type:** `emailSend`
- **Function:** Sends the packaging recommendations to the specified recipient via email.
- **Email Details:**  
  - **Recipient:** Uses `recipientEmail` from the incoming data or defaults to `business@example.com`.  
  - **Subject:** "Sustainable Packaging Recommendations"  
  - **Body:** Personalized email that lists the recommended packaging materials, including environmental impact scores and costs, along with a message highlighting sustainability benefits.

- **Credentials:** Requires valid SMTP credentials named `SMTP Credentials`.

---

## How It Works

1. The workflow triggers manually or on schedule (via Cron Trigger).
2. The user inputs the product type and quantity.
3. The function node analyzes the input and determines the top 3 packaging materials based on sustainability and cost.
4. An email is dispatched containing the recommendations, making it easy for the recipient to make informed packaging decisions.

---

## Supported Product Types and Packaging Materials

| Packaging Material     | Suitable Product Types            | Environmental Impact Score | Cost per Unit (USD) |
|-----------------------|---------------------------------|----------------------------|---------------------|
| Recycled Cardboard    | electronics, clothing, food, general | 2                          | 0.5                 |
| Biodegradable Plastic | food, cosmetics                 | 3                          | 0.8                 |
| Mushroom Packaging    | electronics, cosmetics           | 1                          | 1.2                 |
| Glass                 | cosmetics, food                 | 4                          | 1.5                 |
| Aluminum              | electronics                    | 3                          | 1.0                 |

---

## Setup Instructions

1. **Import the workflow** into your n8n instance.
2. **Configure SMTP Credentials:**
   - Create or import SMTP credentials in n8n.
   - Assign these credentials to the **Send Email** node under the “SMTP Credentials” identifier.
3. **Configure Trigger (Optional):**
   - Set desired schedule in the **Cron Trigger** node if automation is desired on a recurring basis.
4. **Run the workflow manually** or wait for the scheduled trigger.
5. **Provide product details** when prompted to receive packaging recommendations via email.

---

## Customization

- Modify the packaging materials list and criteria in the **Recommend Packaging Materials** function node to fit your specific use cases or add new materials.
- Adjust weighting factors in the scoring formula to emphasize cost or environmental impact differently.
- Update email templates in the **Send Email** node for personalized messaging or branding.

---

## Notes

- The current workflow defaults email recipient to `business@example.com` if `recipientEmail` is not provided in the input data.
- Quantity is collected but not currently factored into the recommendation ranking or output. This can be expanded upon for future enhancements.

---

Thank you for using the AI-Powered Sustainable Packaging Recommendation System to help your business make environmentally responsible packaging choices!