---
name: fulcra-onboarding-discovery
description: "Handles the initial discovery and authentication phase for new Fulcra users. Guides the user through login, uncovers their core intent, and suggests concrete use cases."
---

# Fulcra Onboarding: Discovery

This skill handles the first phase of the Fulcra onboarding process (Step 1). Its primary goals are to ensure the user is authenticated and to rapidly identify how Fulcra can be most useful to them right now.

## Workflow

1. **Authentication Check (STRICTLY ISOLATED):**
   - Verify if the user is currently authenticated with Fulcra.
   - If not authenticated, run `uv tool run fulcra-api auth login` and guide the user through the device code flow.
   - **CRITICAL:** Do *not* ask the user about their goals, intent, or what they want to track in the same message as the authentication instructions. Present the auth link and code, and wait for them to complete it. Combining tasks causes friction and user drop-off.

2. **Intent Discovery (POST-AUTH ONLY):**
   - Only *after* authentication is fully complete, engage the user to uncover their core intent. What do they want to track, remember, or build?
   - **Keep it frictionless:** Keep your messages extremely short and punchy. Assume the user has a low attention span. Do not send walls of text. Ask one simple question at a time.

3. **Proactive Suggestions:**
   - Suggest simple, concrete examples of how they could use Fulcra (e.g., specific Annotations to track).
   - Tailor these suggestions using your existing memory and knowledge of the user.
   - Keep the pace brisk to reach the "wow" factor quickly.

## Handoff

Once the user is authenticated and you have clearly identified 2-3 specific custom data types or streams (Annotations) they want to track, hand control back to the main `fulcra-onboarding` flow to proceed with Data Modeling.
