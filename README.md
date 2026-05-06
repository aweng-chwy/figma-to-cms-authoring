# Figma To CMS Authoring

Codex skill for turning Figma design requirements into CMS authoring UI/backend changes and draft PRs.

## Use It

```text
Use $figma-to-cms-authoring to turn this Figma design into CMS authoring UI/backend PRs.

Figma source:
JIRA ticket:
Target repos: content-authoring-ui, and content-authoring-backend if needed
Base branch:
Target authoring UI area / widget:
Backend contract expectation:
Design states:
Acceptance criteria:
```

## What It Does

- Maps Figma designs to CMS authoring requirements.
- Updates `content-authoring-ui` for forms, options, validation, preview/details, mocks, generated types, and tests.
- Updates `content-authoring-backend` when a new stored field, enum value, dropdown option, validation rule, or API shape is required.
- Prepares UI/backend PR bodies using bundled agent PR templates.

## Guardrails

- Does not create, update, schedule, or publish CMS records.
- Does not create content placements or page-type rollouts.
- Does not change `cms-template-library` unless explicitly approved.
- Shows PR titles and bodies before commit, push, or PR creation.

## Files

- `SKILL.md`: trigger metadata and core operating instructions.
- `references/workflow.md`: detailed workflow and prompt template.
- `references/pr-template-ui.md`: content-authoring-ui PR body template.
- `references/pr-template-backend.md`: content-authoring-backend PR body template.
- `agents/openai.yaml`: UI metadata for Codex skill discovery.

## Install Location

This repo is intended to live under:

```text
~/.codex/skills/figma-to-cms-authoring
```
