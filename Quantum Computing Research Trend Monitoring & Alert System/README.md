# Quantum Computing Research Trend Analysis and Alert System

## Overview
This workflow automates the process of tracking the latest research papers in the field of quantum computing, identifying trending topics using AI-based entity analysis, and sending email alerts based on user-defined topic interests.

---

## Workflow Steps and Nodes

### 1. User Settings
- **Type**: Function
- **Purpose**: Defines user-specific settings such as the email address to receive alerts.
- **Details**: Returns the user's email address as `user@example.com` (can be customized).

```js
return [{ json: { email: 'user@example.com' } }];
```

---

### 2. Fetch arXiv Quantum Papers
- **Type**: HTTP Request
- **Purpose**: Retrieves the 20 most recent research papers from the arXiv API in the `quant-ph` (quantum physics) category.
- **URL**: `http://export.arxiv.org/api/query?search_query=cat:quant-ph&start=0&max_results=20&sortBy=submittedDate&sortOrder=descending`
- **Response Format**: XML as a string

---

### 3. Parse arXiv XML
- **Type**: Function
- **Purpose**: Parses the XML response from arXiv and extracts key metadata from each research paper.
- **Extracted Fields**:
  - `title`: Paper title
  - `summary`: Abstract of the paper
  - `published`: Publication date
  - `authors`: List of authors
  - `link`: URL to the paper

```js
const parseString = require('xml2js').parseStringPromise;

const xmlData = items[0].json.response;

return parseString(xmlData).then(result => {
  const entries = result.feed.entry || [];
  return entries.map(entry => ({
    title: entry.title[0].trim(),
    summary: entry.summary[0].trim(),
    published: entry.published[0],
    authors: entry.author.map(a => a.name[0]),
    link: entry.id[0]
  }));
});
```

---

### 4. Extract Entities with AI
- **Type**: Google Cloud Natural Language API
- **Purpose**: Analyzes the combined paper summary and title to extract named entities.
- **Input**: Concatenated string of paper summary and title.
- **Operation**: `analyzeEntities`
- **Output**: Detected entities in the document to identify important concepts and names.

---

### 5. Detect Trending Topics
- **Type**: Function
- **Purpose**: Aggregates entities recognized from all papers to identify the top trending quantum computing-related topics.
- **Filtering Criteria**: Considers entities of types `OTHER`, `CONSUMER_GOOD`, `ORGANIZATION`, `EVENT`, and `WORK_OF_ART`.
- **Output**: Top 10 trending topics sorted by frequency.

```js
const topics = [];
const entities = $items().map(item => item.json.entities || []);
entities.flat().forEach(entity => {
  if (entity.type === 'OTHER' || entity.type === 'CONSUMER_GOOD' || entity.type === 'ORGANIZATION' || entity.type === 'EVENT' || entity.type === 'WORK_OF_ART' || entity.type === 'CONSUMER_GOOD') {
    topics.push(entity.name.toLowerCase());
  }
});

const topicCounts = topics.reduce((acc, topic) => {
  acc[topic] = (acc[topic] || 0) + 1;
  return acc;
}, {});

const sortedTopics = Object.entries(topicCounts).sort((a, b) => b[1] - a[1]);

return [{ json: { trendingTopics: sortedTopics.slice(0, 10) }}];
```

---

### 6. Check Alerts Based on Interests
- **Type**: Function
- **Purpose**: Compares detected trending topics with predefined user interests and determines if an alert needs to be triggered.
- **User Alert Topics**:
  - "quantum computing"
  - "quantum algorithms"
  - "quantum error correction"
- **Output**: Whether an alert is triggered and which matched topics caused it.

```js
const userPreferences = {
  alertTopics: ["quantum computing", "quantum algorithms", "quantum error correction"]
};

const trending = items[0].json.trendingTopics.map(t => t[0]);

const matches = trending.filter(topic => userPreferences.alertTopics.some(pref => topic.includes(pref)));

return [{ json: { alertTriggered: matches.length > 0, matchedTopics: matches } }];
```

---

### 7. Prepare Alert Summary
- **Type**: Function
- **Purpose**: Generates a summary message containing which trending topics matched the user's interests.
- **Behavior**: If no alert is triggered, no message is generated.

```js
if (items[0].json.alertTriggered) {
  const summary = `Trending topics matched your interests: ${items[0].json.matchedTopics.join(", ")}`;
  return [{ json: { summary } }];
}
return [];
```

---

### 8. Send Email Alert
- **Type**: Email Send
- **Purpose**: Sends an email alert to the user with the summary of matched trending topics.
- **From Email**: `alertsystem@example.com`
- **To Email**: User's email from the "User Settings" node
- **Subject**: "Quantum Computing Research Alert: Trending Topics Detected"
- **Body**: Summary of matched topics in both plain text and HTML formats.

---

## Connections Flow
1. **User Settings** → 2. **Fetch arXiv Quantum Papers**
2. **Fetch arXiv Quantum Papers** → 3. **Parse arXiv XML**
3. **Parse arXiv XML** → 4. **Extract Entities with AI**
4. **Extract Entities with AI** → 5. **Detect Trending Topics**
5. **Detect Trending Topics** → 6. **Check Alerts Based on Interests**
6. **Check Alerts Based on Interests** → 7. **Prepare Alert Summary**
7. **Prepare Alert Summary** → 8. **Send Email Alert**

---

## Summary
This automated workflow streamlines monitoring of the latest quantum computing research by:

- Fetching the newest papers from arXiv.
- Parsing and analyzing their contents with Cloud Natural Language AI to extract relevant entities.
- Determining trending topics across recent publications.
- Checking those topics against user preferences.
- Sending timely email alerts when relevant trends are detected.

This assists researchers and enthusiasts in staying updated on key developments in quantum computing without manual effort.