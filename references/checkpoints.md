# checkpoints

Use this file to determine the current interview phase.

## Phase definitions

### `start`
Use `start` when any of the following is true:
- there is no existing interview framework
- the user has only provided an initial idea and no structured elicitation has begun
- the previous turn explicitly asked to start requirements elicitation from scratch

### `runtime`
Use `runtime` when:
- an interview framework already exists
- the interview is ongoing
- there are still unresolved required topics or material ambiguities

### `complete`
Use `complete` only when the framework is sufficiently mature and one of these conditions holds:
- all required high priority topics have enough information for a useful requirements summary
- the remaining unknowns are minor and clearly marked as open questions
- the user explicitly asks to stop and summarize
- the user signals that the current understanding is enough for drafting requirements

## Completion checklist (Qualitative Gate)

Before choosing `complete`, verify all of the following qualitative criteria are met:
1. **Core Elements Captured**: Product goal, target users/stakeholders, core workflow/usage path, and main functional expectations are clearly defined (relevant slots are `filled`).
2. **Constraints Known**: Major constraints, assumptions, or non-goals are captured or explicitly noted as unknown.
3. **No Critical Ambiguities**: Any major ambiguity that changes scope is either clarified or clearly listed in `open_questions`.
4. **No High-Impact Contradictions**: There are **zero** unresolved high-impact contradictions (no slots with `status="conflicted"` and `contradiction_severity="high"`).

If two or more items above are still weak or missing, remain in `runtime`.
If item 4 fails, remain in `runtime` even when the user asks for a summary.

## Runtime subcases

Within `runtime`, detect whether the next action should emphasize:
- structural update (adding/removing topics or slots)
- slot filling
- topic switching
- clarification of contradictions
- summarization before continuation

## Notes

- Do not mark `complete` just because the conversation is long.
- Do not remain in `runtime` forever if the user wants to stop and the remaining gaps are non-critical.
- When stopping early by user request, keep unresolved contradictions and major assumptions explicit in open questions and risk section.

## Semantic alignment requirements

This file follows the authoritative state semantics mapping.
Before `complete`, verify these additional consistency rules:
- no slot uses invalid `status`/`confidence` pair (for example `filled + open`, `empty + confirmed`)
- `open_question` slots are treated as unresolved and must not be counted as fully converged evidence
