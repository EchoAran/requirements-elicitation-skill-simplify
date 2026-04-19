# Update Framework

Use this file to maintain the interview framework structure and write grounded information into it.

## 1. Structural Maintenance (Topics & Slots)

Before filling values, decide whether the framework needs structural change.

**At `start`:**
Initialize a compact topic set that is sufficient to start elicitation. Prefer 5 to 8 topics.
Load `examples\new_framework_example.md`, which is an example file. You can follow its example.
A good default set is:
- product objective
- target users and stakeholders
- user workflow and scenarios
- functional capabilities
- constraints and boundaries
- priorities and success criteria

**At `runtime`:**
Make a structural edit only when at least one of the following applies:
Load `examples\modify_framework_example.md`, which is an example file. 
- the user introduces a genuinely new requirement area
- an existing topic is too broad and blocks precise questioning
- two topics are overlapping and causing duplicate questions
- a slot is repeatedly needed but does not exist
- a topic or slot is clearly irrelevant to this project

## 2. Filling Values (Slot Filling)

Update the framework by writing newly grounded information into the appropriate slots.
Load `examples\fill_framework_example.md`, which is an example file. You can follow its example.

### Evidence rules
Record information using these confidence levels:
- `confirmed`: explicitly stated by the user
- `supported_inference`: strong inference from the user’s statement, marked as inference
- `open`: unresolved or explicitly unknown

Prefer `confirmed`. Use `supported_inference` sparingly.

### Evidence Payload
Store the user's direct quote in the `evidence` string array. Keep it brief but accurate.

### Status-Confidence Validity Matrix
You MUST apply these constraints when writing slots:
- `status: empty` -> `confidence` MUST be `open`
- `status: filled` -> `confidence` MUST be `confirmed` or `supported_inference` (NEVER `open`)
- `status: open_question` -> `confidence` should be `open` or `supported_inference`
- `status: conflicted` -> `confidence` MUST be `confirmed` or `supported_inference`, and `contradiction_severity` must be non-null

If incoming information would violate the matrix:
- downgrade to `open_question` when certainty is insufficient
- keep prior stable value and append an open question instead of forcing invalid state

## 3. General Filling Rules

- Map each new user statement to the smallest appropriate slot.
- Preserve wording fidelity for high impact facts.
- If the user corrects prior information, replace the old content and mark the latest content as authoritative.
- If evidence is weak, store it as an open question instead of a filled slot.
- Do not invent requirements.
- Do not silently upgrade assumptions into facts.
