# AI-Powered Personalized Book Recommendation and Reading Tracker

## Overview

This workflow is designed to deliver a highly personalized book recommendation experience, track your reading progress, and keep you motivated through weekly summaries. It collects your reading preferences, fetches tailored book suggestions from multiple APIs, logs daily or weekly reading activity, and sends progress summaries via your chosen communication channels.

---

## Workflow Steps

### 1. Collect User Preferences

- **Description:** Gathers essential information about your reading tastes and goals.
- **Inputs Collected:**
  - **Preferred Genres:** Your favorite book genres.
  - **Favorite Authors:** Authors you enjoy reading.
  - **Favorite Books:** Books you love.
  - **Reading Goals:**
    - Books to read per month.
    - Daily reading target (minutes).
  - **Communication Channels:** Preferred platforms for weekly updates (e.g., email, Telegram, WhatsApp).
  - **Contact Info:** Corresponding details for each chosen channel (email address, Telegram ID, WhatsApp number).

### 2. Fetch Personalized Recommendations

- **Description:** Contacts multiple book APIs in parallel to gather diverse suggestions based on your preferences.
- **APIs Used:**
  - **Google Books API**
  - **Open Library API**
  - **Goodreads API**
- **Parameters:** Queries include genres, authors, favorite book keywords, with a limit of 10 results per API.
- **Output:** Aggregated list of recommended books.

### 3. Aggregate and Filter Recommendations

- **Description:** Combines results from all APIs, removes duplicates, and ranks books based on their relevance and popularity.

### 4. Present Recommendations to User

- **Description:** Displays the top-ranked recommended books along with their details.
- **User Action:** Select books to add to your personal reading list.

### 5. Log Reading Progress

- **Description:** Enables you to log your daily or weekly reading details.
- **Inputs Include:**
  - Book title
  - Pages read
  - Time spent reading (minutes)
  - Optional notes
  - Date of reading (defaults to current day)
- **Output:** Updated reading log entry.

### 6. Store Reading Log

- **Description:** Saves your logged reading progress into the database for future tracking.

### 7. Generate Weekly Summary (Scheduled Task)

- **Description:** Every Sunday at 18:00, compiles a summary of your reading activity over the past week.
- **Summary Includes:**
  - Books read
  - Progress compared to reading goals
  - Reading time
  - Notes and highlights

### 8. Send Weekly Summary

- **Description:** Sends the weekly reading summary through your preferred communication channels.
- **Supported Channels and Actions:**
  - **Email:** Sends a summary email.
  - **Telegram:** Sends a Telegram message.
  - **WhatsApp:** Sends a WhatsApp message.

---

## Data Models

### Book

| Field           | Type   | Description                                  |
|-----------------|--------|----------------------------------------------|
| title           | string | Book title                                   |
| author          | string | Book author                                  |
| genre           | string | Genre of the book                            |
| cover_image_url | string | URL for the book’s cover image               |
| description     | string | Summary or description of the book           |
| source_api      | string | Origin API of the book recommendation        |
| unique_id       | string | Unique identifier for the book                |

### Reading Log Entry

| Field            | Type        | Description                   |
|------------------|-------------|-------------------------------|
| book_title       | string      | Title of the book             |
| date             | string (ISO 8601) | Date of the reading session  |
| pages_read       | integer     | Number of pages read          |
| time_spent_minutes| integer     | Minutes spent reading         |
| notes            | string      | Optional notes and comments   |

### User Preferences

| Field                  | Type               | Description                          |
|------------------------|--------------------|-------------------------------------|
| preferred_genres       | list of strings    | Favorite book genres                |
| favorite_authors       | list of strings    | Preferred authors                   |
| favorite_books         | list of strings    | Favorite books                     |
| reading_goals          | object             | User set goals                     |
|  ├ books_per_month     | integer            | Monthly reading target             |
|  └ daily_reading_minutes| integer           | Daily reading duration goal        |
| communication_channels | list of strings    | Preferred notification channels    |
| contact_info           | object             | Contact details per channel         |
|  ├ email               | string             | Email address                     |
|  ├ telegram_id         | string             | Telegram user ID                  |
|  └ whatsapp_number     | string             | WhatsApp phone number             |

---

## Summary

This workflow provides an end-to-end reading companion experience by:

- Understanding and incorporating your unique reading preferences.
- Leveraging multiple APIs for rich, diverse book recommendations.
- Maintaining a detailed reading log to monitor progress.
- Delivering timely, personalized weekly reading summaries via your chosen communication channels.

Enjoy a smarter, more engaging reading journey!