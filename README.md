<p align="center">
  <img src="./pic/poster.png" alt="Requirements Elicitation Skill Poster" width="100%" />
</p>

<h1 align="center">Requirements Elicitation Skill (Lightweight Edition)</h1>

<p align="center">
  <a href="./README-zh.md"><strong>Chinese Version</strong></a>
</p>

<p align="center">
  A lightweight, prompt-driven agent skill for conducting semi-structured requirements interviews.
  Turn vague ideas into traceable requirement artifacts without complex engineering overhead.
</p>

## What This Skill Does
- Runs adaptive multi-turn interviews instead of one-shot questionnaires.
- Maintains an interview framework with explicit confidence and evidence in memory/JSON.
- Supports dynamic topic updates as requirements evolve.
- Detects, records, and resolves requirement contradictions.
- Produces two final artifacts:
  - `final_interview_framework` (JSON)
  - `requirements_summary_report` (Markdown)

## Integration (Agent Environments)

This skill is designed to be purely prompt-driven and extremely lightweight. It relies on the Agent's long-context memory and basic file-writing capabilities.

You can easily integrate it into:
- **Cursor** (via `.cursor/rules/`)
- **Windsurf** (via `.windsurf/rules/`)
- **Claude Code** or any other file-capable CLI agents.

### Quick Start for Cursor/Windsurf
1. Clone this repository into your project's `.cursor/skills/` (or similar) folder:
   ```bash
   mkdir -p .cursor/skills
   cd .cursor/skills
   git clone https://github.com/EchoAran/requirements-elicitation-skill.git
   ```
2. Create an adapter rule (e.g., `.cursor/rules/requirements-elicitation.mdc`) pointing the agent to `SKILL.md`.
3. Ask your agent: *"Help me run a requirements interview for a campus marketplace app."*

## Repository Structure
```text
.
├── SKILL.md                                # The core prompt and execution loop
├── README.md                               # English documentation
├── README-zh.md                            # Chinese documentation
├── assets/
│   ├── interview_framework_schema.json     # The JSON structure the agent maintains
│   └── requirements_report_format.md       # Final report template
├── references/                             # Detailed strategy guides loaded on-demand
│   ├── checkpoints.md                      # Phase detection and completion checklist
│   ├── conflict_resolution.md              # How to handle contradictory requirements
│   ├── generate_speak.md                   # How to formulate the next interview question
│   ├── intent_routing.md                   # Classifying user inputs
│   ├── select_current_topic.md             # Deciding what to talk about next
│   ├── topic_dependency_map.md             # Topic prerequisites
│   └── update_framework.md                 # Rules for filling and modifying the JSON
└── examples/                               # Example interactions
    └── ...
```

## The Core Loop

At every turn, the agent silently executes:
1. **Check Phase**: Start, Runtime, or Complete.
2. **Classify Intent**: Understand what the user just said.
3. **Update Framework**: Maintain the JSON structure in `state/interview_framework.json`.
4. **Detect Contradictions**: Flag conflicts.
5. **Select Topic & Generate Question**: Output exactly one focused question.
6. **Persist State**: Overwrite the JSON file.

## Why Lightweight?
This edition strips away thousands of lines of complex Python state-management scripts (MVCC, atomic commits, rollback checkpoints) from the original design. It embraces the fact that modern AI Agents have robust context windows and can maintain state efficiently via simple file overwrites (`write_file`), making the skill drastically more reliable and easier to deploy.

## License
Proprietary (see `SKILL.md` frontmatter).
