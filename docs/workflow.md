# kogoto Issue Refinement Workflow

This document defines the MVP workflow for kogoto.

kogoto focuses on Issue refinement before implementation.
It helps humans discuss, clarify, split, and govern Issues.

## Workflow overview

The basic flow is:

1. Select a GitHub Issue.
2. Read the Issue body and relevant comments.
3. Extract the current discussion state.
4. Identify unresolved questions.
5. Identify accepted decisions.
6. Identify rejected alternatives.
7. Estimate implementation impact.
8. Detect whether the Issue should be split.
9. Detect ADR candidates.
10. Post a structured refinement comment.
11. Continue discussion.
12. Repeat refinement as needed.

kogoto is expected to be used repeatedly on the same Issue.

An Issue does not become "complete" just because kogoto has refined it once.
The refinement loop continues as long as discussion changes the scope, assumptions, or decisions.

## Core workflow

### Step 1: Select an Issue

The user selects a target Issue.

Example:

```sh
kogoto refine 123
```

kogoto reads:

* Issue title
* Issue body
* Issue comments
* labels
* linked Issues if available
* linked ADRs or documents if available

In the MVP, linked Issue and document support may be shallow or manual.

### Step 2: Summarize the current state

kogoto summarizes the current state of the Issue.

The summary should distinguish:

* stated goal
* current scope
* accepted decisions
* unresolved questions
* rejected alternatives
* known constraints
* likely affected files or modules
* related Issues
* ADR candidates

The summary must not invent decisions.
If something is inferred, it should be marked as an inference.

### Step 3: Identify unresolved questions

kogoto extracts questions that block further refinement.

Examples:

* What behavior should change?
* Which command owns this responsibility?
* Should this be documented as an ADR?
* Is compatibility required?
* Is this Issue too large?
* Which files are likely to change?
* Should this be split into separate Issues?

Questions should be specific enough for humans to answer.

Bad:

```text
Clarify the design.
```

Good:

```text
Should kogoto create child Issues automatically, or only suggest split candidates in a comment?
```

### Step 4: Identify decisions

kogoto identifies decisions that have already been made.

Each decision should include:

* decision
* reason
* source comment or discussion context, if available
* affected scope
* whether it may require ADR recording

Example:

```text
Decision:
kogoto should not own implementation loops.

Reason:
Implementation, verification, and PR workflow orchestration should be handled by other tools.

Affected scope:
README, workflow definition, command design.
```

### Step 5: Identify rejected alternatives

kogoto records rejected alternatives when they are relevant.

Rejected alternatives are important because they prevent repeated discussion.

Example:

```text
Rejected alternative:
Make kogoto the full development workflow orchestrator.

Reason:
This would blur its responsibility with testography, git-kura, Ortoxygen, coding agents, and CI.
```

### Step 6: Estimate issue size

kogoto estimates whether the Issue is too large.

The estimate may use:

* expected touched files
* expected new files
* expected deleted files
* expected line changes
* affected modules
* number of independent decisions
* number of acceptance criteria
* number of unresolved questions
* uncertainty level

The MVP should treat these as estimates, not facts.

Example:

```text
Estimated size:
- expected touched files: 6-9
- expected new files: 2
- expected diff size: medium
- uncertainty: high

Assessment:
This Issue likely exceeds the preferred MVP size and should be split.
```

### Step 7: Suggest split candidates

When an Issue appears too broad, kogoto proposes split candidates.

Each split candidate should include:

* title
* purpose
* included scope
* excluded scope
* dependency on parent or sibling Issues
* expected affected files
* reason for separation

Example:

```text
Split candidate:
Define kogoto MVP concept documents

Purpose:
Create README.md, docs/workflow.md, and docs/commands.md.

Excluded:
Implementation, GitHub API integration, child Issue creation.
```

kogoto should not create child Issues automatically in the MVP unless explicitly requested.

### Step 8: Detect ADR candidates

kogoto detects decisions that may deserve ADRs.

ADR candidates should be suggested when a decision is:

* architectural
* persistent
* cross-cutting
* likely to affect future Issues
* likely to be revisited
* important for tool boundaries
* important for data model or workflow design

Example:

```text
ADR candidate:
Limit kogoto scope to Issue refinement and decomposition governance.

Reason:
This defines the boundary between kogoto and orchestration tools.
```

kogoto should ask humans whether to record the decision as an ADR.

### Step 9: Post a refinement comment

kogoto posts a structured comment to the Issue.

The comment should use a stable format.

Recommended sections:

```markdown
# Refinement

## Current understanding

## Accepted decisions

## Rejected alternatives

## Open questions

## Estimated impact

## Split candidates

## ADR candidates

## Suggested next action
```

The comment should be concise enough to review.

If the discussion is long, kogoto should prioritize decision-relevant information over chat transcript completeness.

### Step 10: Continue the loop

After humans answer questions or make decisions, kogoto can run again.

Repeated refinement should:

* preserve previous decisions
* update outdated summaries
* mark resolved questions
* detect new questions
* update split candidates
* identify parent or sibling Issue impact

## Parent-child Issue governance

When an Issue is split, kogoto should maintain relationships between Issues.

### Parent Issue responsibilities

The parent Issue should retain:

* original motivation
* overall scope
* list of child Issues
* decisions shared by all child Issues
* unresolved cross-cutting questions
* final integration status, if relevant

### Child Issue responsibilities

A child Issue should contain:

* narrow purpose
* inherited context from parent
* local scope
* local acceptance criteria
* local unresolved questions
* links back to parent
* known sibling dependencies

### Sibling Issue responsibilities

Sibling Issues should avoid:

* overlapping scope
* conflicting decisions
* duplicate acceptance criteria
* inconsistent terminology
* hidden dependencies

kogoto should flag these risks when detected.

## Human-in-the-loop rules

kogoto should not silently make persistent decisions.

The following actions require explicit human confirmation:

* creating a child Issue
* updating a parent Issue
* posting an ADR plan
* creating or updating an ADR file
* changing the stated scope of an Issue
* marking an unresolved question as resolved
* declaring an Issue ready for implementation

kogoto may propose these actions, but should not treat proposals as decisions.

## MVP output format

The MVP refinement comment should be plain Markdown.

Example:

```markdown
# Refinement

## Current understanding

This Issue redefines kogoto as an Issue refinement tool rather than a full development workflow tool.

## Accepted decisions

- kogoto focuses on Issue refinement and decomposition.
- implementation loops are out of scope.
- orchestration belongs to a separate tool.

## Open questions

- Should the MVP only post refinement comments, or also create child Issues?
- What thresholds should trigger split suggestions?

## Estimated impact

- README.md: create or replace
- docs/workflow.md: create
- docs/commands.md: create

Estimated size: small to medium.

## Split candidates

No split required for the initial documentation-only MVP.

## ADR candidates

- Scope pivot: kogoto as Issue refinement / decomposition governance tool.

## Suggested next action

Create the initial documentation files and review whether the command design matches the new scope.
```

## Non-goals

This workflow does not define:

* code implementation flow
* test execution flow
* pull request flow
* CI behavior
* worktree management
* release flow
* full multi-tool orchestration

Those are intentionally outside kogoto's core workflow.
