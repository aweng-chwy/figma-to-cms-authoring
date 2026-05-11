---
name: figma-to-cms-authoring
description: Use when turning Figma designs into CMS authoring work, including content-authoring-ui changes, optional content-authoring-backend schema/enum/dropdown/validation support, draft PR generation, Figma-to-authoring requirement mapping, or prompts like "Figma to CMS authoring PR" and "add a new CMS authoring field from Figma"; do not use for CMS record scheduling, placement creation, or publishing.
---

# Figma To CMS Authoring

Use this skill to convert a Figma design into authoring-product code changes and draft PRs. Keep CMS content mutation, scheduling, placements, publishing, and delivery-template changes out of scope unless the user explicitly expands the scope.

## Quick Start

1. Read `references/workflow.md` before implementation.
2. Intake Figma source, JIRA ticket, target repo set, authoring UI area/widget, backend contract expectation, design states, and acceptance criteria.
3. Classify the requested change before editing: form field/content mismatch, layout/spacing mismatch, preview/rendering mismatch, or backend contract support.
4. Produce a concise Figma-to-authoring mapping before editing code.
5. If a new stored field, enum value, dropdown option, validation rule, widget type, or API shape is needed, change `content-authoring-backend` first.
6. Change `content-authoring-ui` for forms, option lists, validation, preview/details, mocks, generated types, and tests.
7. Run focused verification in each changed repo.
8. Fill the relevant PR template from `references/pr-template-ui.md` and, if needed, `references/pr-template-backend.md`.
9. Show PR titles and bodies for confirmation before committing, pushing, or opening draft PRs.

## Non-Negotiable Gates

- Do not create, update, delete, schedule, or publish CMS records.
- Do not create content placements or page-type rollouts.
- Do not change `cms-template-library` unless the user separately approves delivery-template work.
- When website component rendering is approved or required, use `$cms-component-update` for the `cms-template-library` changes.
- Stop if the Figma requirement cannot map to a known or explicitly requested authoring contract.
- Preserve existing authoring chrome unless the user explicitly asks for visual styling changes. A prompt about wrong fields should not restyle popover headers, footers, padding, width, or buttons.
- Treat hidden Figma fields as not author-visible unless the user explicitly asks to expose them.
- Verify Chirp/KIB token names before using them; do not copy Figma-only names such as generic `label` into code.
- For responsive previews, isolate horizontal scrolling to the visual grid/list rather than the outer body container when vertical content must remain visible.
- Keep UI and backend PRs separate when both repos change, and cross-link them.
- Use repo commands for generated GraphQL/types; do not hand-edit generated artifacts.

## Repo Guidance

- `content-authoring-ui`: authoring config, forms, validation, preview/details, mocks, tests, generated GraphQL types.
- `content-authoring-backend`: GraphQL schema, inputs, outputs, enum values, dropdown option source, resolver/service support, validation, tests.
- `cms-template-library`: out of scope unless explicitly requested.

## References

- `references/workflow.md`: detailed execution workflow and prompt template.
- `references/pr-template-ui.md`: content-authoring-ui agent PR template.
- `references/pr-template-backend.md`: content-authoring-backend agent PR template.
