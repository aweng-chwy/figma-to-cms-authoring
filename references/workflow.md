# Figma To CMS Authoring Workflow

## Inputs

- Figma URL with file, page, and node IDs, or exported design JSON/assets.
- JIRA ticket, branch name, base branch, and target repo set.
- Target authoring UI area, widget type, or current route.
- Backend contract expectation, if any.
- Design states: create, edit, preview, validation error, loading, and empty.
- Copy, image requirements, alt text, links, CTA labels, analytics tags, and legal copy.
- Acceptance criteria for what authors must configure.

## Intake Output

```markdown
## Figma Intake
- Source:
- Frames:
- Authoring UI area:
- Required author-configurable fields:
- Backend contract changes:
- Missing UI capabilities:
- Assets and copy:
- Accessibility notes:
```

## Decision Table

| Finding | Path |
| --- | --- |
| Existing authoring UI already supports the design | No code change unless docs/examples/tests were requested. |
| Existing widget type exists but UI lacks a field or state | Update `content-authoring-ui` config, forms, validation, preview/details, and tests. |
| UI can collect the field but preview/details are incomplete | Update preview/details and focused tests. |
| Figma adds a backend-backed dropdown option | Update `content-authoring-backend` option/enum support first, then UI types/options/forms/tests. |
| Figma adds a new stored widget field | Update backend schema/resolver/validation first, then UI generated types/form handling/preview/tests. |
| Website component cannot render the requested content | Stop and document the gap, or use `$cms-component-update` if the user approves `cms-template-library` work. |
| Backend persistence or ownership is unclear | Stop and document the backend/schema gap before UI work. |

## Implementation Order

1. Map Figma fields to authoring contract:
   - widget type or authoring route
   - labels, helper text, defaults, required/optional state
   - validation rules
   - backend field names, enum values, GraphQL fields, resolvers, service behavior
   - preview/details expectations
   - error, loading, empty, and disabled states
2. Implement backend support only when required:
   - schema/input/output/enum changes
   - resolver/service persistence
   - validation and backward compatibility
   - generated artifacts through repo commands
   - focused backend tests
3. Implement UI support:
   - config constants and types
   - Chirp/KIB controls following local patterns
   - form state and validation
   - preview/details behavior
   - mock data/stories/examples only when useful
   - React Testing Library coverage
4. Verify:
   - backend unit/schema/build checks when backend changed
   - relevant UI tests
   - lint/typecheck when risk warrants it
   - Storybook/local preview evidence when visual behavior changed
5. Prepare PRs:
   - use `references/pr-template-ui.md` for UI PRs
   - use `references/pr-template-backend.md` for backend PRs
   - show titles and bodies before commit/push/PR creation

## Prompt Template

```text
Use $figma-to-cms-authoring to run the Figma to CMS authoring workflow.

Figma source:
JIRA ticket:
Target repos: content-authoring-ui, and content-authoring-backend if needed
Base branch:
Target authoring UI area / widget:
Backend contract expectation:
Design states:
Acceptance criteria:

Rules:
- Do not mutate CMS records.
- Do not schedule or publish content.
- Backend changes are allowed only when needed for new authoring fields or options.
- Do not change delivery-template code unless separately approved.
```
