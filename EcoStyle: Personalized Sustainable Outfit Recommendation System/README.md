# Sustainable Outfit Recommendation Workflow

This workflow generates personalized sustainable outfit recommendations based on user wardrobe data, current weather conditions, and trending sustainable fashion styles. It then shares the recommendations on social media and emails them to the user.

---

## Workflow Overview

### 1. Start (Manual Trigger)
- The workflow begins when a user uploads their wardrobe data.
- Supported formats: JSON or CSV containing clothing items with attributes such as:
  - Type (e.g., jacket, shirt, shorts)
  - Material (e.g., organic cotton, recycled fabric)
  - Color
  - Brand
- User also provides their city for weather data and email for recommendations.

### 2. Get Weather Data (HTTP Request)
- Retrieves current weather data for the user’s city using the OpenWeatherMap API.
- Required: `YOUR_OPENWEATHERMAP_API_KEY`
- Fetches temperature in Celsius to tailor clothing suggestions suitable for the weather.

### 3. Get Sustainable Fashion Trends (HTTP Request)
- Calls an external API endpoint to get the latest sustainable fashion trends.
- Required: `YOUR_FASHION_TRENDS_API_KEY`
- Receives data such as trending colors and styles that promote sustainability.

### 4. AI Outfit Recommender (Function)
- Processes inputs: user wardrobe, weather data, and fashion trends.
- Applies a custom algorithm with the following logic:
  - Filters wardrobe items for eco-friendly materials (e.g., organic, recycled, sustainable).
  - Selects items appropriate for current temperature (e.g., jackets for cold weather).
  - Incorporates trending colors by ranking recommended items featured in current sustainable fashion trends.
- Outputs the top 5 recommended sustainable outfits.

### 5. Share on Social Media (Facebook Graph API)
- Publishes a post on Facebook featuring:
  - An image of the top recommended outfit (if available).
  - Caption promoting sustainable fashion with hashtags.
- Requires Facebook API authentication and permissions.

### 6. Send Recommendations Email (Email Send)
- Sends an email to the user with personalized outfit recommendations.
- Email content includes a formatted list of recommended items specifying type, color, and material.
- Uses a no-reply sender email: `no-reply@sustainablefashion.com`

---

## Setup and Configuration

### Required API Keys
- **OpenWeatherMap API Key:** Replace `YOUR_OPENWEATHERMAP_API_KEY` in the "Get Weather Data" node URL.
- **Sustainable Fashion Trends API Key:** Replace `YOUR_FASHION_TRENDS_API_KEY` in the authorization header of the "Get Sustainable Fashion Trends" node.
- **Facebook Graph API:** Configure authentication for "Share on Social Media" node.

### User Input Data
- The uploaded wardrobe file should contain an array named `wardrobeItems`, with each item having:
  - `type` (string)
  - `material` (string)
  - `color` (string)
  - Optionally: `imageUrl` for social media sharing
- A `city` field specifying the user’s location (for weather).
- A user email (`userEmail`) to receive recommendations.

---

## Workflow Execution Flow

1. User manually triggers the workflow by uploading wardrobe data with city and email.
2. Simultaneously fetches weather and sustainable fashion trends.
3. Runs the "AI Outfit Recommender" to produce filtered, ranked outfit recommendations.
4. Shares top outfit on Facebook with a caption.
5. Sends the detailed recommended outfit list to the user’s email.

---

## Notes

- Modify and expand the mock recommendation algorithm in the "AI Outfit Recommender" node to fit specific business rules or a more complex AI model.
- Ensure all API keys and authentication methods are securely stored and configured.
- Adapt email sender and social media post settings according to your branding and platforms.

---

Enjoy providing your users with smart, fashionable, and eco-friendly outfit choices!