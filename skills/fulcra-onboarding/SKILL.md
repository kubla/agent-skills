---
name: fulcra-onboarding
description: "Guides a new user or agent through the initial setup, configuration, and capabilities of the Fulcra environment."
---

# Fulcra Onboarding

Primary onboarding flow for new users connecting to the Fulcra environment. Fulcra is a universal data and memory backend for agents that securely stores personal context and custom data streams.

## Workflow

1. **Discovery:**
   - Have a brief conversation to capture the user's core intent and what they want to achieve with Fulcra.
2. **Data Modeling:**
   - Translate their intent into 2-3 specific custom data types/streams (e.g., "Mood Logs", "Focus Sessions", "Coffee Intake").
   - *Note: Agent will use Fulcra API tools to define these schemas.*
3. **First Ingestion:**
   - Prompt the user for an initial piece of data or use context from the conversation.
   - Record this data into at least one of the newly created data streams.
4. **Time-to-Wow (The Demonstration):**
   - Retrieve the recorded data from Fulcra.
   - Ask the user how they would like their dashboard themed. Suggest 2-3 creative options based on your sense of their personality (e.g., cyberpunk, fantasy, newspaper, modern fashion magazine, professional workplace tool).
   - Generate a custom, themed HTML dashboard/view using the Canvas system matching their chosen aesthetic. This dashboard must visually demonstrate their intent coming to life.
5. **Handoff & Next Steps:**
   - Suggest specific next actions based on the momentum:
     - Gather continuous background data via the iOS app.
     - Run the dashboard as an interactive local web app to iterate on it.
     - Ingest bulk historical data or integrate diverse external sources (e.g., Apple Health, Google Calendar) via other Fulcra skills.
