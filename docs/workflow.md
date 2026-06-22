# kogoto Issue Refinement Workflow

This document defines the Issue refinement workflow for kogoto.

kogoto is centered on a chat-driven refinement session started by:

```sh
kogoto refine <issue-number>
```

After the session starts, the user should not need to leave the conversation to run separate commands for each refinement action.

kogoto proposes actions inside the session.
The user approves or rejects them inside the session.
Only approved persistent actions are executed.

## Workflow overview

The basic workflow is:

1. Start a refinement session with `kogoto refine <issue-number>`.
2. kogoto reads the target Issue.
3. kogoto summarizes the current state.
4. kogoto identifies missing information and unresolved questions.
5. The user answers, rejects, or revises the interpretation.
6. kogoto identifies decisions, rejected alternatives, split candidates, and ADR candidates.
7. kogoto proposes persistent actions when useful.
8. The user approves or rejects each action.
9. kogoto executes approved actions.
10. The session continues until the Issue is sufficiently refined.

The workflow is iterative.

An Issue is not considered refined merely because kogoto produced one report.
Refinement continues as long as the discussion changes the scope, assumptions, or decisions.

## Session start

The user starts with:

```sh
kogoto refine <issue-number>
```

Example:

```sh
kogoto refine 123
```

kogoto reads:

- Issue title
- Issue body
- Issue comments
- labels
- linked Issues, if available
- linked documents or ADRs, if available
- repository files, if needed for lightweight impact estimation

The first MVP may limit repository inspection and linked-resource traversal.

## Initial analysis

At the beginning of the session, kogoto presents an initial analysis.

The analysis should distinguish:

- directly stated information
- inferred information
- unresolved questions
- accepted decisions
- rejected alternatives
- likely affected files or modules
- possible split candidates
- possible ADR candidates

kogoto must not present inference as fact.

Example:

```markdown
## Current understanding

The Issue appears to redefine kogoto as an Issue refinement tool rather than a full development workflow tool.

This is inferred from the discussion, not yet recorded as a final project decision.

## Open questions

- Should `kogoto refine` be the only MVP user-facing command?
- Should child Issue creation be included in the first MVP, or only designed as a future session action?
```

## Clarification loop

kogoto asks specific questions when the Issue is underspecified.

Bad:

```text
Please clarify the design.
```

Good:

```text
Should kogoto create child Issues during the first MVP, or should it only propose split candidates in the refinement session?
```

The user may answer in ordinary chat.

kogoto should then update its working understanding and, when appropriate, propose recording the answer.

Example:

```text
This answer changes the command design. Should I post a decision note to the Issue?
```

If the user approves, kogoto posts the comment.

## Decision tracking

kogoto tracks decisions made during refinement.

Each decision should include:

- decision
- reason
- affected scope
- source context
- whether ADR recording may be appropriate

Example:

```markdown
## Decision

kogoto should use `kogoto refine <issue-number>` as the primary MVP entrypoint.

## Reason

Issue refinement should happen inside a chat session. Users should not need to leave the session to run separate commands for comment posting, Issue splitting, or ADR suggestion.

## Affected scope

- README.md
- docs/workflow.md
- docs/commands.md
```

kogoto may propose posting this as an Issue comment.

## Rejected alternatives

Rejected alternatives should be recorded when they prevent repeated discussion.

Example:

```markdown
## Rejected alternative

Expose `kogoto split`, `kogoto adr`, and `kogoto discuss` as primary MVP commands.

## Reason

This would make the user leave the refinement session for each action and would shift kogoto toward a generic GitHub Issue operation CLI.
```

Rejected alternatives are part of the project reasoning and should not be discarded.

## Impact estimation

kogoto estimates likely implementation impact during refinement.

The estimate is not a measured diff.

It may include:

- expected touched files
- expected new files
- expected deleted files
- expected affected modules
- expected documentation changes
- expected command changes
- number of unresolved questions
- uncertainty level
- split risk

Example:

```markdown
## Estimated impact

- README.md: replace or substantially revise
- docs/workflow.md: create
- docs/commands.md: create
- CLI implementation: not required for this documentation-only Issue

Estimated size: small to medium
Uncertainty: low
Split risk: low
```

If the estimate exceeds configured thresholds, kogoto should suggest splitting the Issue.

## Issue splitting

kogoto may suggest splitting when an Issue is too large or contains separable concerns.

Split candidates should include:

- proposed title
- purpose
- included scope
- excluded scope
- parent Issue relationship
- sibling Issue relationships
- expected affected files
- reason for separation

Example:

```markdown
## Split candidate

### Define MVP documentation

Purpose:
Create README.md, docs/workflow.md, and docs/commands.md for the zero-based MVP.

Included:
- concept
- workflow
- command design

Excluded:
- CLI implementation
- GitHub API integration
- child Issue creation
```

kogoto should ask before creating child Issues.

Example:

```text
This Issue can be split into two child Issues. Should I create them?
```

Only after approval should kogoto create child Issues.

## Parent-child Issue governance

When child Issues are created, kogoto should preserve the relationship between the parent and children.

### Parent Issue

The parent Issue should contain:

- original motivation
- overall direction
- child Issue list
- shared decisions
- cross-cutting unresolved questions
- integration notes, if needed

### Child Issue

Each child Issue should contain:

- narrow purpose
- inherited context from the parent
- included scope
- excluded scope
- acceptance criteria
- local unresolved questions
- link to the parent Issue
- known sibling dependencies

### Sibling Issues

Sibling Issues should be checked for:

- overlapping scope
- inconsistent terminology
- conflicting decisions
- duplicated acceptance criteria
- hidden dependencies

If a child Issue discussion changes assumptions shared by the parent or siblings, kogoto should propose updating the affected Issues.

## ADR candidate workflow

kogoto detects ADR candidates during refinement.

A decision may be an ADR candidate when it is:

- architectural
- persistent
- cross-cutting
- likely to affect future Issues
- likely to be revisited
- relevant to tool boundaries
- relevant to data model or workflow design

Example:

```markdown
## ADR candidate

Decision:
kogoto should be scoped to Issue refinement and decomposition governance.

Reason:
This defines the boundary between kogoto and implementation, testing, Git workflow, repository analysis, and orchestration tools.

Suggested action:
Ask whether this decision should be recorded as an ADR.
```

kogoto should ask before preparing or posting ADR-related content.

Example:

```text
This looks like an ADR candidate. Should I prepare an ADR draft?
```

## Persistent actions

Persistent actions are changes made outside the transient chat session.

Examples:

- post an Issue comment
- create a child Issue
- update a parent Issue
- update a child Issue
- link Issues
- post an ADR candidate comment
- create an ADR draft
- update documentation

Persistent actions require explicit user approval.

kogoto must distinguish proposal from execution.

Example:

```text
Proposed action:
Post the following refinement comment to Issue #123.

Proceed?
```

The action is executed only after the user approves.

## Refinement comment format

A refinement comment should be concise and structured.

Recommended format:

```markdown
# Refinement

## Current understanding

## Accepted decisions

## Rejected alternatives

## Open questions

## Estimated impact

## Split candidates

## ADR candidates

## Proposed next action
```

The exact sections may be omitted when empty.

## Example refinement comment

```markdown
# Refinement

## Current understanding

This Issue redefines kogoto as a tool for Issue refinement and decomposition governance.

The main scope is documentation for a zero-based MVP.

## Accepted decisions

- `kogoto refine <issue-number>` is the primary MVP entrypoint.
- Refinement proceeds inside a chat session.
- Comment posting and child Issue creation are session actions, not separate commands that users must manually invoke.
- Persistent actions require explicit user approval.

## Rejected alternatives

- Expose `kogoto split`, `kogoto adr`, and `kogoto discuss` as primary MVP commands.
  - Reason: this would push users out of the refinement session and make kogoto resemble a generic Issue operation CLI.

## Open questions

- Should child Issue creation be implemented in the first MVP or left as a future session action?
- What default thresholds should trigger split suggestions?

## Estimated impact

- README.md: revise
- docs/workflow.md: create
- docs/commands.md: create

Estimated size: small to medium
Uncertainty: low

## Split candidates

No split required for the initial documentation-only Issue.

## ADR candidates

- Scope pivot: kogoto as Issue refinement and decomposition governance tool.

## Proposed next action

Update the MVP documentation and review whether the command design matches the chat-driven refinement model.
```

## Human approval rules

The following actions require approval:

- posting a refinement comment
- posting a decision note
- creating child Issues
- updating parent or child Issues
- preparing ADR drafts
- updating documentation
- marking a question as resolved
- declaring an Issue ready for implementation

The following actions do not require approval:

- reading an Issue
- summarizing discussion
- asking questions
- suggesting split candidates
- suggesting ADR candidates
- estimating impact
- identifying unresolved concerns

## End of session

A refinement session may end when:

- the Issue scope is clear
- unresolved questions are recorded
- accepted decisions are recorded
- rejected alternatives are recorded
- split candidates are resolved
- ADR candidates are resolved
- the next action is clear

Ending a refinement session does not necessarily mean the Issue is ready for implementation.

kogoto may conclude that the next action is:

- answer remaining questions
- split the Issue
- record an ADR
- update the parent Issue
- create documentation
- proceed to implementation outside kogoto

## Non-goals

This workflow does not define:

- implementation loop
- test execution loop
- pull request loop
- CI behavior
- Git worktree management
- release flow
- full multi-tool orchestration

These are outside kogoto's core workflow.
