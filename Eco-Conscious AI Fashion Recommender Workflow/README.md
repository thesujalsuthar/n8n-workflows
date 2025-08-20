# AI-Powered Sustainable Fashion Outfit Recommender

This workflow leverages AI and external APIs to provide personalized, eco-friendly fashion outfit recommendations based on current fashion trends, sustainability data, and individual user preferences. The recommendations are delivered via email and Microsoft Teams notifications.

---

## Workflow Overview

1. **Start - Dummy User Input**  
   Initializes the workflow with a dummy user ID and email to simulate user input.

2. **Fetch Fashion Trends**  
   Retrieves the latest fashion trends using the Fashion Trends API.

3. **Fetch Sustainability Data**  
   Fetches sustainability indices for clothing brands from the Sustainability Index API.

4. **Fetch User Preferences**  
   Obtains the user's fashion style preferences from the User Profile Service API.

5. **Generate Recommendations**  
   Combines and processes data from the previous nodes to generate a list of eco-friendly outfit recommendations tailored to the user's style.

6. **Send Email Recommendations**  
   Sends an email to the user with the personalized, sustainable outfit recommendations.

7. **Send Messaging Platform Notification**  
   Sends a notification with the recommendations via Microsoft Teams.

---

## Node Details

### 1. Start - Dummy User Input
- **Type:** Function
- **Purpose:** Provides a dummy user ID (`12345`) and email (`user@example.com`) as inputs to initiate the workflow.
- **Output:** JSON containing userId and userEmail.

### 2. Fetch Fashion Trends
- **Type:** HTTP Request
- **API Endpoint:** `https://api.fashiontrends.com/v1/current-trends`
- **Authentication:** HTTP Basic Auth (Fashion API Credential)
- **Purpose:** Fetches the current trending fashion items including brand, style, and item details.

### 3. Fetch Sustainability Data
- **Type:** HTTP Request
- **API Endpoint:** `https://api.sustainabilityindex.com/v1/index`
- **Query Parameter:** `category=clothing`
- **Authentication:** HTTP Basic Auth (Sustainability API Credential)
- **Purpose:** Retrieves sustainability scores for clothing brands to assess eco-friendliness.

### 4. Fetch User Preferences
- **Type:** HTTP Request
- **API Endpoint:** `https://api.userprofileservice.com/v1/user/preferences`
- **Query Parameter:** `userId` (dynamically set from input)
- **Authentication:** HTTP Basic Auth (User Profile API Credential)
- **Purpose:** Fetches the user's preferred fashion style and related preferences.

### 5. Generate Recommendations
- **Type:** Function
- **Logic:** 
  - Retrieves data from Fashion Trends, Sustainability Data, and User Preferences nodes.
  - Filters trending items based on user's preferred style (if any).
  - Applies an eco-friendliness filter by checking if the brand's sustainability index is 7 or higher.
  - Outputs a list of recommended sustainable outfit options.

### 6. Send Email Recommendations
- **Type:** Email Send
- **From:** `no-reply@sustainablefashion.ai`
- **To:** User email (from input)
- **Subject:** "Your Eco-Friendly Outfit Recommendations"
- **Body:** Lists recommended items including their names and brands with a friendly message.
- **Authentication:** SMTP Credentials

### 7. Send Messaging Platform Notification
- **Type:** Microsoft Teams Message
- **Content:** Personalized message listing recommended sustainable fashion items.
- **Authentication:** Microsoft Teams OAuth2 API Credentials

---

## Connections Flow

- Workflow starts with **Start - Dummy User Input** node.
- Triggers three simultaneous HTTP Requests:
  - Fetch Fashion Trends
  - Fetch Sustainability Data
  - Fetch User Preferences
- The outputs of these three nodes feed into **Generate Recommendations**.
- The **Generate Recommendations** node outputs recommendations that are sent:
  - Via email through **Send Email Recommendations**
  - Via Microsoft Teams message through **Send Messaging Platform Notification**

---

## Credentials Required

- **Fashion API Credential:** For accessing fashion trends API.
- **Sustainability API Credential:** For fetching sustainability indices.
- **User Profile API Credential:** To get user preferences securely.
- **SMTP Credentials:** To send recommendation emails.
- **Microsoft Teams OAuth2 API Credentials:** To post notifications into Microsoft Teams channels or users.

---

## Customization Tips

- Replace dummy user input with real user data inputs or triggers.
- Adjust ecology score threshold in `Generate Recommendations` node (currently set to 7).
- Expand filtering logic to include other preferences like size, colors, or occasions.
- Integrate additional messaging platforms or delivery channels as needed.

---

## Summary

This workflow efficiently combines multiple data sources and user preferences to recommend sustainable and stylish outfits, fostering eco-conscious fashion choices delivered directly to users through personalized emails and messaging platforms.