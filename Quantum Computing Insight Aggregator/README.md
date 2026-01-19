# Quantum Computing Daily Insight Digest

This workflow automates the daily aggregation, summarization, storage, and distribution of the latest insights related to quantum computing, including academic papers and news articles.

---

## Workflow Overview

The workflow fetches relevant content from RSS feeds, normalizes and merges it, creates a consolidated summary, saves the summary to Google Drive, and emails the digest.

---

## Workflow Nodes and Steps

### 1. Fetch Papers

- **Type**: RSS Feed Read
- **Resources**: `paper`, `news`, `insight`
- **Search Query**: `"quantum computing"`
- **Purpose**: Pulls all relevant papers, news, and insights about quantum computing from configured RSS sources.
- **Returns**: All available results.

### 2. Fetch News

- **Type**: RSS Feed Read
- **Resource**: `news`
- **Search Query**: `"quantum computing"`
- **Purpose**: Specifically fetches news related to quantum computing.
- **Returns**: All available results.

### 3. Normalize Items

- **Type**: Function
- **Code**:
  ```javascript
  const items = items.map(item => {
    const title = item.json.title || item.json.titleText || '';
    const link = item.json.link || item.json.url || '';
    const summary = item.json.contentSnippet || item.json.summary || item.json.description || '';
    return {
      json: {
        title,
        link,
        summary
      }
    };
  });
  return items;
  ```
- **Purpose**: Transforms fetched items into a standardized format containing `title`, `link`, and `summary` properties for consistent downstream processing.

### 4. Merge Data

- **Type**: Merge
- **Mode**: Append (pass through)
- **Purpose**: Combines the normalized items from "Fetch Papers" and "Fetch News" into a single collection.

### 5. Create Summary

- **Type**: Function
- **Code**:
  ```javascript
  const summary = items.map(item => `- ${item.json.title}\n  ${item.json.link}\n  ${item.json.summary}`).join('\n\n');
  return [{ json: { summary } }];
  ```
- **Purpose**: Generates a text summary digest combining the title, link, and summary of each merged item into a clean, readable format.

### 6. Create Folder If Not Exist

- **Type**: Google Drive
- **Operation**: Create folder named `Quantum Computing Insights`
- **Purpose**: Ensures a dedicated folder exists on Google Drive to store daily digests.

### 7. Save Summary to Google Drive

- **Type**: Google Drive
- **Operation**: Create file
- **File Name**: `Quantum_Computing_Digest_YYYY-MM-DD.txt` (current date formatted)
- **Folder**: `Quantum Computing Insights`
- **Content**: The summary text from previous node
- **MIME Type**: `text/plain`
- **Purpose**: Persistently saves the daily digest as a text file on Google Drive for archival and easy access.

### 8. Send Email

- **Type**: Email Send
- **From Email**: `your-email@example.com`
- **To Email**: `recipient@example.com`
- **Subject**: `Quantum Computing Daily Insight Digest - YYYY-MM-DD`
- **Content**:
  - Text: Raw summary text
  - HTML: Summary formatted with line breaks for email readability
- **Purpose**: Sends the daily digest to specified recipient(s) via email.

---

## Connections Flow

1. **Fetch Papers** and **Fetch News** both feed their results into **Normalize Items**.
2. The normalized results are merged through **Merge Data**.
3. The merged data is summarized by **Create Summary**.
4. The summary triggers two parallel actions:
    - Create the folder on Google Drive if it doesn’t exist.
    - Send the email digest.
5. After the folder is ready, the summary file is saved to Google Drive.

---

## Usage Notes

- Replace `your-email@example.com` and `recipient@example.com` with actual sender and recipient emails in the **Send Email** node.
- Adjust RSS feed sources in the **Fetch Papers** and **Fetch News** nodes as necessary.
- Ensure Google Drive credentials are properly configured and authorized in n8n.
- The digest filename uses the current date to create unique daily files.

---

## Summary

This workflow automates the discovery, consolidation, archiving, and dissemination of the latest quantum computing insights every day, enabling users to stay informed with minimal manual effort.