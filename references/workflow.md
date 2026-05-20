# Figma To CMS Authoring Workflow

## Inputs

- Published Figma URL with file, page, and node IDs. Use exported design JSON/assets only when the user asks or MCP access is blocked and the fallback is approved.
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
- Figma access notes:
- Frames:
- Authoring UI area:
- Required author-configurable fields:
- Backend contract changes:
- Missing UI capabilities:
- Assets and copy:
- Accessibility notes:
```

## Repo Relationship Map

- `content-authoring-ui`: authoring forms, dialogs, widget selection, preview/details UI, mocks, tests, and generated GraphQL client types.
- `content-authoring-backend`: authoring GraphQL schema, inputs/outputs, resolvers, validation, widget persistence, image handling, and generated artifacts.
- `content-authoring-c`: APIs that `content-authoring-backend` requests. Inspect this repo when backend behavior depends on an upstream authoring API contract, response shape, or service behavior.
- `content-a`: runtime delivery API. Use it only as handoff context from authoring work; `$cms-component-update` treats it as the source of truth for rendered CMS component payloads.
- `cms-template-library`: CMS components that render `content-a` payloads. Do not edit from this skill unless the user separately approves CMS component work.

## Figma MCP Access Rules

- Prefer `chewy-figma` MCP data from the published Figma node. Do not reach for local exports when the user asks to use the published file.
- If a node fetch fails, try both Figma node ID formats: `115-20531` and `115:20531`.
- Distinguish access states clearly:
  - node returned with metadata: use it as the source of truth
  - file accessible but node missing: ask whether the node is published or changed
  - file/node inaccessible: report MCP/access failure before using any fallback
- Use `chewy_figma_to_metadata` for node structure and dimensions, `chewy_figma_to_raw_html` for HTML/source layout, `chewy_figma_component_instances` for library components, and `chewy_figma_variable_context` for token usage.
- Use `chewy-chirp` MCP before writing Chirp component or token code; do not infer Sass token names from Figma labels.

## Correction Classification

Before changing code, classify the prompt and keep edits within that category:

| User prompt pattern | Change scope |
| --- | --- |
| "This form is wrong", "field is wrong", "node X form" | Compare visible form body descendants only: labels, input type, order, required state, helper text, and hidden/visible fields. Preserve existing dialog chrome unless requested. |
| "Layout/spacing/width is wrong" | Compare component/container layout, spacing, widths, and responsive behavior. Do not change field semantics unless the Figma node also shows a field mismatch. |
| "Preview/rendering/tile sizing is wrong" | Compare preview output and responsive viewport samples. Keep authoring form fields unchanged unless requested. |
| "Add backend support", "new widget", "new field persists" | Update backend contract first, then UI generated types/forms/mutations/queries. |
| "Undo style changes" | Revert visual shell changes while preserving requested data/field/behavior fixes. |

If the user narrows a correction, follow that narrower scope even if adjacent issues are visible.

## Node Inspection Rules

- For each supplied node ID, extract visible descendants before editing. Capture field label, field component/type, required/optional state, helper text, visibility, layout direction, and key dimensions.
- Treat `visible: false` fields as not author-visible unless the user explicitly asks to expose hidden controls.
- Preserve existing authoring chrome for form-body corrections: popover header/footer, buttons, close behavior, width, padding, and shell styling should stay as-is unless the prompt is about those elements.
- Prefer existing repo controls and local form helpers. Only introduce a new component or wrapper when existing controls cannot represent the Figma requirement.
- Verify design tokens against Chirp/KIB docs or existing repo usage. Do not use Figma-only token names directly in Sass.

## Responsive Preview Rules

- For tile or card previews with multiple viewport nodes, build a small/medium/large matrix before CSS edits:
  - container width/height
  - tile count per group
  - tile x/y positions
  - tile width/height
  - image area dimensions
  - chrome-on and chrome-off state if authoring controls can hide edit labels/buttons
- Validate that toggles for authoring chrome do not clip body content. Avoid placing `overflow-x: auto` on an outer container that also owns vertical body content; isolate horizontal scrolling to the grid/list surface where possible.
- Keep preview fixes separate from form dialog fixes unless the user asks for both.

## Backend Support Checklist

When a new widget type or persisted shape is introduced, update `content-authoring-backend` before UI integration:

- Add or update GraphQL object/input types for widget fields and item fields.
- Add the widget variant constant/enum used by guards and resolver code.
- Update interface `__resolveType` handling for widgets and widget items.
- Update image variation keys and resolution mapping when the widget uses custom images.
- Add or update guards and focused tests for widget input/shape validation.
- Run repo generation commands for generated GraphQL/types and do not hand-edit generated artifacts.
- Run focused backend build/tests before updating UI generated types.

## New Widget Authoring Checklist

For a new widget, verify each authoring surface explicitly:

- Create widget sheet/list option, label, icon/category, and default item shape.
- Widget config dialog fields, field order, required state, helper text, validation, and defaults.
- Item/tile config dialogs, including repeated item add/remove/reorder behavior when applicable.
- Preview/details rendering for empty, loading, error, and populated states.
- Responsive preview controls when the Figma includes small/medium/large examples.
- Generated GraphQL/types after backend schema changes.
- Mock data, fixtures, and focused tests for the new widget type.

## Decision Table

| Finding | Path |
| --- | --- |
| Existing authoring UI already supports the design | No code change unless docs/examples/tests were requested. |
| Existing widget type exists but UI lacks a field or state | Update `content-authoring-ui` config, forms, validation, preview/details, and tests. |
| UI can collect the field but preview/details are incomplete | Update preview/details and focused tests. |
| Figma adds a backend-backed dropdown option | Update `content-authoring-backend` option/enum support first, then UI types/options/forms/tests. |
| Figma adds a new stored widget field | Update backend schema/resolver/validation first, then UI generated types/form handling/preview/tests. |
| Figma introduces a new widget type | Update backend schema, variant guards, interface resolvers, image handling, generated types, and tests before UI wiring. |
| CMS component cannot render the requested content | Stop and document the gap, or use `$cms-component-update` if the user approves `cms-template-library` work. |
| Backend persistence or ownership is unclear | Stop and document the backend/schema gap before UI work. |

## Implementation Order

1. Map Figma fields to authoring contract:
   - widget type or authoring route
   - correction category and explicit non-scope
   - published Figma access result and node IDs used
   - labels, helper text, defaults, required/optional state
   - visible versus hidden Figma fields
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
   - field-body changes without restyling dialog chrome unless requested
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
- Use published Figma MCP data first; do not use local exports unless requested or approved as fallback.
- Backend changes are allowed only when needed for new authoring fields or options.
- Do not change delivery-template code unless separately approved.
```

## Correction Examples

- "This form is wrong too, node ID: 116:8809" means inspect node `116:8809` and update only visible form fields and single-column/body structure shown in that node. Preserve existing popover shell styling unless the node mismatch is explicitly about the shell.
- "The tile sizing isn't correct across the different viewport samples" means inspect all viewport sample nodes and build a viewport matrix before changing preview CSS.
- "The checkbox ... is cutting off the body content" means inspect overflow and chrome-toggle layout behavior. Prefer moving horizontal overflow to the tile/grid surface over clipping the whole widget body.
- "Make changes in authoring-backend to support this new widget" means run the backend support checklist before UI generated type updates.
