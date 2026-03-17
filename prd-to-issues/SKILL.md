---
name: prd-to-issues
description: Break a PRD into a Jira Epic with independently-grabbable Stories using tracer-bullet vertical slices. Use when user wants to convert a PRD to issues, create implementation tickets, or break down a PRD into work items.
---

# PRD to Issues

Break a PRD into a Jira Epic with independently-grabbable Stories using vertical slices (tracer bullets).

## Process

### 1. Locate the PRD

Ask the user for the Confluence page URL or title containing the PRD.

If the PRD is not already in your context window, fetch it with:

```bash
acli confluence page get --space <SPACE_KEY> --title "<PRD Title>"
```

Ask the user for the Confluence space key if not already known.

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so to understand the current state of the code.

### 3. Draft vertical slices

Break the PRD into **tracer bullet** issues. Each issue is a thin vertical slice that cuts through ALL integration layers end-to-end, NOT a horizontal slice of one layer.

Slices may be 'HITL' or 'AFK'. HITL slices require human interaction, such as an architectural decision or a design review. AFK slices can be implemented and merged without human interaction. Prefer AFK over HITL where possible.

<vertical-slice-rules>
- Each slice delivers a narrow but COMPLETE path through every layer (schema, API, UI, tests)
- A completed slice is demoable or verifiable on its own
- Prefer many thin slices over few thick ones
</vertical-slice-rules>

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each slice, show:

- **Title**: short descriptive name
- **Type**: HITL / AFK
- **Blocked by**: which other slices (if any) must complete first
- **User stories covered**: which user stories from the PRD this addresses

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the dependency relationships correct?
- Should any slices be merged or split further?
- Are the correct slices marked as HITL and AFK?

Iterate until the user approves the breakdown.

### 5. Create the Jira Epic and Stories

First, create a Jira Epic for the PRD:

```bash
acli jira workitem create --project ENG --type Epic --summary "<PRD Title>" --description "Epic for PRD: <PRD Title>. See Confluence page: <page URL>"
```

Then, for each approved slice, create a Jira Story as a child of the Epic. Write each story body to a temporary file and create it:

```bash
acli jira workitem create --project ENG --type Story --parent <EPIC-KEY> --summary "<Slice title>" --description-file /tmp/story-body.md
```

Create stories in dependency order (blockers first) so you can reference real issue keys in the "Blocked by" field.

<issue-template>
## Parent PRD

Epic: <EPIC-KEY> | Confluence: <page URL>

## What to build

A concise description of this vertical slice. Describe the end-to-end behavior, not layer-by-layer implementation. Reference specific sections of the parent PRD rather than duplicating content.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Blocked by

- Blocked by <ISSUE-KEY> (if any)

Or "None - can start immediately" if no blockers.

## User stories addressed

Reference by number from the parent PRD:

- User story 3
- User story 7

</issue-template>

Do NOT modify the parent Epic or the Confluence PRD page.
