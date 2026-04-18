---
area: {{AREA-NAME}}
version: 0.1
status: draft
---

# Area: {{AREA-NAME}}

<!-- One file per Area (top-level Event Model grouping).
     status: draft | reviewed | finalized
     Only finalized areas feed FORGE. -->

## Workflow: {{WORKFLOW-NAME}}

<!-- A Workflow is a series of Flows for a larger process. -->

### Flow: {{FLOW-NAME}}

<!-- A Flow is a short sequence of slices for one focused interaction. -->

**Intent**: {{What this flow accomplishes — one sentence.}}

**Actors**: {{Who participates — e.g. "User (Human)", "PaymentGateway (ExternalSystem)"}}

#### CommandSlice: {{COMMAND-NAME}}

| Given | When | Then |
|---|---|---|
| {{prior state / events}} | {{CommandName}} `{ {{fields}} }` | {{EventName}} `{ {{fields}} }` |
| {{alternate precondition}} | {{CommandName}} `{ {{fields}} }` | Error: `"{{message}}"` |

#### ViewSlice: {{VIEW-NAME}}

| Given | When | Then |
|---|---|---|
| {{events that build the view}} | {{criteria / filter}} | {{ViewName}} `{ {{fields}} }` |
