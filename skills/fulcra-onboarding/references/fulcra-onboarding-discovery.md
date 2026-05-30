---
name: fulcra-onboarding-discovery
description: "Handles the initial discovery and authentication phase for new Fulcra users. Guides the user through login, uncovers their core intent, and suggests concrete use cases."
---

# Fulcra Onboarding: Discovery

**Tone Reminder:** Keep the energy high! Use emojis when suggesting ideas, and maintain a conversational, exciting vibe that hints at the vast possibilities of what they can build.

This skill handles the first phase of the Fulcra onboarding process (Step 1). Its primary goals are to ensure the user is authenticated and to rapidly identify how Fulcra can be most useful to them right now.

## Workflow

1. **Intent Discovery (PRE-AUTH):**
   - *Before* asking the user to authenticate or click any links, engage them to uncover their core intent. What brought them to Fulcra? What do they want to track, remember, or build?
   - **Crucial: Grounded Brainstorming:** Never ask open-ended questions like "What do you want to track?" without providing immediate inspiration. Always seed the conversation with 2-3 specific, tailored examples of what Fulcra can track (e.g., "We could build a tracker for your daily deep work hours, how many coffees you've had, or a log of the books you're reading."). If you have long-term memory or context about this specific user, use it to make these examples highly personalized. 
   - **Keep it frictionless:** Keep your messages extremely short and punchy. Assume the user has a low attention span. Do not send walls of text. 
   - **Quick Start Fallback:** If the user is still unsure or gives a vague answer (e.g., "just trying it out"), offer a brief "multiple choice" menu to spark ideas. Tailor these to the user if you have prior context, or use these default archetypes:
     - *Productivity:* Tracking daily mood or deep work sessions.
     - *Health:* Logging coffee intake, water, or sleep quality.
     - *Media:* Keeping a log of books read or movies watched.

2. **Authentication:**
   - **Check current auth status (no consent needed — read-only):**

     ```bash
     uv tool run fulcra-api user-info
     ```

     If it exits 0 and returns JSON, the user is already authenticated → skip
     to step 3. If it fails (any non-zero exit), they are not authenticated;
     proceed to login.

   - **Login (this requires the user to click a URL in their browser).** Run
     **exactly** the command below. The `timeout`, `2>&1`, and `|| true` are
     all required: they cause the device-flow command to print the URL and
     code to stdout and *return*, instead of hanging in the background where
     most agents cannot reliably capture stdout:

     ```bash
     timeout 12 uv tool run fulcra-api auth login 2>&1 || true
     ```

     The output will contain two pieces you **MUST** relay to the user
     immediately and verbatim:
       - an **authorization URL** (e.g. `https://fulcra.us.auth0.com/activate?user_code=XXXX-YYYY`)
       - a **device code** (e.g. `XXXX-YYYY`)

     Present them like this (do **not** summarize, shorten, or paraphrase
     the URL or code):

     > 🔐 To connect to Fulcra, open this URL in your browser to sign in or
     > create your account:
     >
     > **`<URL>`**
     >
     > Confirm the code shown on that page matches: **`<CODE>`**
     >
     > Reply "done" when you've finished and I'll continue.

     **DO NOT** run a bare `uv tool run fulcra-api auth login` (without
     `timeout`/`2>&1`) — it blocks indefinitely and the URL never reaches the
     user. **DO NOT** spawn it as a background process and poll its log; the
     stdout of a hanging child process is not reliably readable from many
     agent runtimes.

   - **After the user replies "done", verify the login completed:**

     ```bash
     uv tool run fulcra-api user-info
     ```

     If it's still a 401, retry once or twice with a short pause — there can
     be a brief gap between completing browser auth and the token becoming
     available.

   - **Security note:** the authorization URL and device code grant access
     to the user's Fulcra account. In a public or group channel, warn the
     user about the sensitivity and advise them to treat the link as a
     secret (don't paste it into shared chats, don't share it with others).

3. **Proactive Suggestions:**
   - Suggest simple, concrete examples of how they could use Fulcra (e.g., specific Annotations to track).
   - Tailor these suggestions using your existing memory and knowledge of the user.
   - Keep the pace brisk to reach the "wow" factor quickly.

## Handoff

Once the user is authenticated and you have clearly identified 2-3 specific custom data types or streams (Annotations) they want to track, hand control back to the main `fulcra-onboarding` flow to proceed with Data Modeling.
