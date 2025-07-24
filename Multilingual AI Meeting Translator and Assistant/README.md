# AI-Driven Real-Time Language Translator and Multilingual Meeting Assistant

This workflow enables real-time speech-to-text transcription, multilingual translation, live caption broadcasting via WebSockets, and AI-powered meeting summaries and action item extraction. It is designed to facilitate multilingual meetings by providing translated captions and intelligent meeting insights instantly.

---

## Workflow Overview

### 1. Live Captions WebSocket (Trigger)
- **Type**: WebSocket Trigger
- **Path**: `/live-captions`
- **Function**: Listens for incoming live audio stream data or spoken language captions in English (en-US).
- **Mode**: Continuous listening to stream real-time speech data.

### 2. Speech To Text
- **Type**: Speech-to-Text Node
- **Language**: English (en-US)
- **Function**: Converts incoming speech data into live text transcripts continuously.

### 3. Translate Text
- **Type**: Google Translate Node
- **Source Language**: English (en)
- **Target Languages**:
  - Spanish (es)
  - French (fr)
  - German (de)
  - Chinese (zh)
  - Japanese (ja)
- **Function**: Translates the live English transcript into multiple target languages.

### 4. Aggregate Translations
- **Type**: Function Node
- **Function**: Aggregates separate translated strings from multiple languages into a single cohesive object to simplify broadcasting and processing downstream.

### 5. Multilingual Summary
- **Type**: OpenAI GPT-4o-mini Node
- **Prompt**: Summarizes the entire meeting dialogue in each target language separately based on aggregated translated content.
- **Output**: Multilingual summaries that can be used for review or distribution.

### 6. Action Item Extraction
- **Type**: OpenAI GPT-4o-mini Node
- **Prompt**: Extracts actionable tasks or follow-ups from the English transcript.
- **Output**: List of concise meeting action items derived from live dialogue.

### 7. Format Captions
- **Type**: Function Node
- **Notes**: Formats a combined view of the original English live captions alongside their translated versions, suitable for displaying during meetings.

### 8. Translated Captions WebSocket
- **Type**: WebSocket Node
- **Path**: `/translated-captions`
- **Function**: Broadcasts the formatted multilingual live captions to clients in real time.

---

## Detailed Node Connections

- **Live Captions WebSocket** feeds audio/text data to → **Speech To Text**
- **Speech To Text** output streams to two places simultaneously:
  - **Translate Text** (for multilingual translation)
  - **Action Item Extraction** (for identifying action points)
- **Translate Text** passes all translations to → **Aggregate Translations**
- **Aggregate Translations** sends data to:
  - **Multilingual Summary** (for AI-generated multilingual meeting summaries)
  - **Format Captions** (to prepare data for broadcasting)
- **Format Captions** outputs formatted captions and sends them via → **Translated Captions WebSocket**

---

## Usage Instructions

1. **Connect Live Audio Source:** Establish a WebSocket client that streams live meeting audio or recognized text data into the `/live-captions` endpoint.
2. **Real-Time Transcription and Translation:** The workflow continuously processes incoming audio to provide live English transcriptions and translations in Spanish, French, German, Chinese, and Japanese.
3. **Broadcast Multilingual Captions:** Clients connected to the `/translated-captions` WebSocket path receive live formatted captions combining both original and translated text.
4. **Receive Meeting Insights:** Post or during the meeting, access AI-generated multilingual summaries and extracted action items for efficient follow-up and documentation.

---

## Prerequisites

- n8n installation with required nodes:
  - Speech To Text
  - Google Translate
  - OpenAI integration (GPT-4o-mini model)
  - WebSocket Trigger and WebSocket nodes
- API credentials for:
  - Speech-to-Text provider
  - Google Translate API
  - OpenAI API

---

## Summary

This workflow leverages real-time speech recognition, multilingual translation, and AI-powered summarization to deliver a comprehensive multilingual meeting assistant. It optimizes communication and collaboration in diverse language environments by providing live captions, summaries, and actionable insights seamlessly.