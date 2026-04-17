---
name: requirements-elicitation-skill
description: >-
  Trigger when user wants to discuss a product idea, clarify software requirements, 
  or write a PRD. Conducts a guided, semi-structured interview to extract workflows, 
  constraints, priorities, and resolve contradictions step by step.
license: Proprietary
version: 3.0.0
schema_version: 3.0.0
---

# Requirements Elicitation Skill

Use this skill when the user provides an initial product idea, software concept, feature request, or vague requirement set and wants help turning it into a clearer, more structured requirements understanding through an interview.

This skill runs as a **stateful semi-structured interview loop**. Do not treat it as a one-shot questionnaire. You must maintain and evolve an interview framework in memory (and write it to a local JSON file) as the conversation develops.

## State Management (Lightweight)

You must maintain the interview state using a single JSON file.
- **State File**: `state/interview_framework.json` (relative to your current working directory).
- **Initialization**: If the file does not exist (at `start` phase), create it based on `assets/interview_framework_schema.json`.
- **Persistence**: After each turn, simply **overwrite** this file with the updated framework using your file write tool.
- **Output Discipline**: Keep the framework structure updated in your context, but **DO NOT output the entire JSON in your chat response** to the user.

## The Core Interview Loop (State Machine)

For every user reply, you MUST execute the following sequence internally before responding:

### Step 1: Check Phase & Completion
- Read `references/checkpoints.md` to evaluate the current state.
- **Branch A (`start`)**: If no framework exists, proceed to Step 2 to initialize it.
- **Branch B (`complete`)**: If the qualitative checklist is fully met (or the user forces a stop), transition to completion.
  - Load `assets/requirements_report_format.md` and `examples/summarize_example.md`.
  - Output the final structured requirements summary to the user.
  - End the interview.
- **Branch C (`runtime`)**: If gaps or ambiguities remain, proceed to Step 2.

### Step 2: Classify Intent
- Read `references/intent_routing.md`.
- Classify the user's newest input (e.g., is it an explicit requirement, an exploratory idea, or a constraint?).
- Decide whether to treat it as a confirmed fact or a tentative assumption that needs confirmation.

### Step 3: Update Framework (Memory & JSON)
- Read `references/update_framework.md`.
- **Structural Edit**: Add/remove topics or slots if the scope has fundamentally changed.
- **Slot Filling**: Fill slot values based on the classified intent. You MUST adhere to the strict Status-Confidence Validity Matrix.

### Step 4: Detect Contradictions
- Read `references/conflict_resolution.md`.
- Compare the new input against previously filled slots.
- If claims conflict, mark the affected slots as `conflicted` and register the contradiction.

### Step 5: Select Topic & Generate Question
- Read `references/select_current_topic.md` and `references/topic_dependency_map.md`.
- Choose ONE focused topic to drive the next turn. **Rule**: Always prioritize resolving high-impact contradictions before moving to empty topics.
- Read `references/generate_speak.md`.
- Generate exactly **ONE** focused question or confirmation for the user. Do not overwhelm the user with multiple questions at once.

### Step 6: Persist State
- Overwrite `state/interview_framework.json` with the latest state.
- Deliver your generated question (from Step 5) to the user.

## Reference Loading Matrix

To save context, load the following files *only* when their specific step in the loop requires it:

| Step / Runtime Need | Required File(s) | Optional Examples (Load if stuck) |
| :--- | :--- | :--- |
| **1. Phase Decision** | `references/checkpoints.md` | - |
| **2. Intent Classification** | `references/intent_routing.md` | `examples/intent_routing_example.md` |
| **3. Framework Update** | `references/update_framework.md` | `examples/new_framework_example.md`, `examples/fill_framework_example.md` |
| **4. Contradiction Handling**| `references/conflict_resolution.md` | `examples/contradiction_resolution_example.md` |
| **5a. Topic Selection** | `references/select_current_topic.md`, `references/topic_dependency_map.md` | `examples/select_current_topic_example.md` |
| **5b. Utterance Gen** | `references/generate_speak.md` | `examples/generate_speak_example.md` |
| **Completion** | `assets/requirements_report_format.md` | `examples/summarize_example.md` |

## Important Guardrails

- **One Question Per Turn**: Never ask a laundry list of questions. Keep it conversational and focused.
- **No Hallucination**: Do not invent requirements or silently upgrade assumptions into facts. If the user is vague, store it as an `open_question` or `supported_inference`, not `confirmed`.
- **Traceability**: Preserve exact excerpts from the user's input in the `evidence` field of the JSON.
- **Silent Maintenance**: Do not explain your framework updates or phase checks to the user unless explicitly asked. Keep your visible output focused on the interview conversation.