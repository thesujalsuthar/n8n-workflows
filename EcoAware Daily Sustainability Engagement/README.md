# EcoAware: Daily Personalized Sustainability Tips & Motivation

## Overview
**EcoAware** is an automated daily email workflow designed to send personalized sustainability tips and motivational quotes to users, encouraging eco-friendly habits tailored to their preferences and experience levels.

---

## Workflow Breakdown

### 1. Daily Trigger
- **Node:** `Daily Trigger`
- **Type:** Schedule Trigger
- **Description:** Executes the workflow every day at 8:00 AM.
- **Purpose:** Starts the process by triggering subsequent nodes on a daily basis to ensure users receive fresh tips every morning.

---

### 2. Get Users
- **Node:** `Get Users`
- **Type:** Function
- **Description:** Retrieves the list of users with their personalized preferences.
- **Details:**
  - Users are hardcoded with properties:
    - `id` (email address)
    - `name`
    - `preferences`:
      - `focusAreas` (array of interests like energy, water, waste, transport, food)
      - `habitLevel` (experience level such as beginner or intermediate)
- **Example Users:**
  - Alice (energy, water, waste; beginner)
  - Bob (transport, food; intermediate)

---

### 3. Generate Tips & Motivation
- **Node:** `Generate Tips & Motivation`
- **Type:** Function
- **Description:** Produces tailored sustainability tips and a motivational quote for each user.
- **Functionality:**
  - Contains a set of sustainability tips categorized by focus areas and habit levels.
  - Randomly selects one tip per focus area according to the user's habit level.
  - Selects a random motivational quote from a predefined list.
- **Output:**
  - User's ID and name
  - Array of personalized tips (`area` and `tip`)
  - Motivation quote

---

### 4. Send Email
- **Node:** `Send Email`
- **Type:** Email Send
- **Description:** Sends a personalized email to each user with their tips and motivational message.
- **Email Details:**
  - **From:** ecoaware@yourdomain.com
  - **To:** User's email (`userId`)
  - **Subject:** "Your Daily Sustainability Tips & Motivation"
  - **Body Content:**
    ```
    Hello [User's Name],

    Here are your personalized sustainability tips for today:

    - [Area] Tip 1
    - [Area] Tip 2
    ...

    Motivation: [Motivational Quote]

    Thank you for helping the planet!

    - EcoAware
    ```

---

## How It Works
1. The workflow triggers at 8:00 AM daily.
2. It fetches the users and their preferences.
3. For each user, generates sustainability tips based on their chosen focus areas and habit levels.
4. Selects a motivational quote.
5. Sends the compiled tips and motivation via email.

---

## Customization
- **Users List:** Modify the `Get Users` node function code to update or add users with their preferences.
- **Tips:** Adjust or expand sustainability tips inside the `Generate Tips & Motivation` node to suit your message.
- **Motivational Quotes:** Update or add quotes in the `Generate Tips & Motivation` node for more variety.
- **Email Settings:** Change the sender address and customize email format in the `Send Email` node accordingly.

---

## Requirements
- n8n automation platform.
- Email sending credentials configured in the `Send Email` node.
- Access to n8n's Function and Schedule nodes.

---

## License
This workflow is open for personal and commercial adaptation. Please credit the original design where applicable.

---

Thank you for using **EcoAware** to support environmental sustainability every day!