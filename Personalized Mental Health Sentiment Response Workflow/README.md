# Mental Health Check-in Workflow

This workflow is designed to provide users with personalized mental health resources based on the sentiment of their submitted text. It uses an HTTP webhook to receive user input, analyzes the sentiment using OpenAI, interprets the results, recommends resources accordingly, and responds with those recommendations.

---

## Workflow Overview

1. **Webhook**  
   - Endpoint: `POST /mental-health-checkin`  
   - Receives user text input in JSON format, e.g., `{ "text": "I feel stressed lately." }`  
   - Waits for the workflow to complete and returns the final response.

2. **Sentiment Analysis**  
   - Uses the OpenAI node to analyze the sentiment of the received text.  
   - Passes the text from the webhook to OpenAI for analysis.

3. **Parse Sentiment**  
   - Processes the raw sentiment output from OpenAI.  
   - Extracts and normalizes the sentiment into one of three categories: `positive`, `neutral`, or `negative`.  
   - Supports both labeled sentiment responses and numeric sentiment scores.

4. **Recommend Resources**  
   - Provides a curated list of activities and resources based on the classified sentiment:  
     - **Positive:** Encouragement to maintain positive habits.  
     - **Neutral:** Suggests mindfulness and general wellness activities.  
     - **Negative:** Offers relaxation exercises, crisis hotlines, and therapy techniques.  
     - Defaults to general mental health resources if the sentiment is unrecognized.

5. **Respond with Resources**  
   - Sends the recommended resources back to the user in JSON format as the HTTP response.

---

## Detailed Node Description

### 1. Webhook  
- **Type:** HTTP Webhook  
- **Method:** POST  
- **Path:** `/mental-health-checkin`  
- **Response Mode:** Waits until the last node finishes before responding.

### 2. Sentiment Analysis  
- **Type:** OpenAI Text Analysis  
- **Operation:** Analyze sentiment of the text input received from the webhook.  
- **Input:** Text field populated from incoming webhook JSON payload.

### 3. Parse Sentiment  
- **Type:** Function  
- **Purpose:** Normalize OpenAI's output into `positive`, `neutral`, or `negative`.  
- **Logic:**  
  - Checks for text labels indicating sentiment.  
  - Parses numeric scores, assigning sentiment based on thresholds (>0.2 positive, < -0.2 negative, otherwise neutral).

### 4. Recommend Resources  
- **Type:** Function  
- **Purpose:** Selects mental health activities and resource recommendations according to parsed sentiment.  
- **Positive sentiment examples:**  
  - Gratitude journaling  
  - Maintaining a positive mindset  
- **Neutral sentiment examples:**  
  - Mindfulness meditation  
  - General mental wellness tips  
- **Negative sentiment examples:**  
  - Breathing exercises  
  - Crisis hotline: 1-800-273-8255  
  - Cognitive behavioral therapy (CBT) techniques  

### 5. Respond with Resources  
- **Type:** Respond to Webhook  
- **Mode:** JSON  
- **Output:** Returns the curated list of activities and resources as a JSON array in the HTTP response.

---

## Usage

1. **Deploy the workflow** in your n8n instance.  
2. **Configure OpenAI credentials** with your API key.  
3. Send a POST request to `/mental-health-checkin` with a JSON body containing:  
   ```json
   {
     "text": "Your mental health check-in text here."
   }
   ```  
4. Receive a JSON response with recommended mental health resources based on the input sentiment.

---

## Example

**Request:**

```http
POST /mental-health-checkin
Content-Type: application/json

{
  "text": "I have been feeling very anxious and overwhelmed."
}
```

**Response:**

```json
[
  {
    "type": "activity",
    "title": "Breathing exercises",
    "description": "Practice deep breathing for relaxation."
  },
  {
    "type": "resource",
    "title": "Crisis hotline",
    "description": "Call or chat with a mental health professional at 1-800-273-8255."
  },
  {
    "type": "resource",
    "title": "CBT techniques",
    "description": "Learn cognitive behavioral therapy strategies."
  }
]
```

---

## Notes

- Ensure the OpenAI API credentials are correctly set to enable sentiment analysis.  
- The sentiment parsing function includes simple heuristics; you can customize thresholds or labels to better fit your data.  
- This workflow can be extended with additional resources or integrated with other services for richer interaction.

---

Thank you for using this Mental Health Check-in workflow!