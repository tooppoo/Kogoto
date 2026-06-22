# kogoto Commands

This document defines the provisional CLI surface for kogoto.

kogoto is not designed as a collection of small commands for manually operating GitHub Issues.

The primary command is:

```sh
kogoto refine <issue-number>
```

This command starts an interactive Issue refinement session.

Other operations, such as posting comments, creating child Issues, suggesting ADRs, or updating parent Issues, are performed inside the refinement session after explicit user approval.

## Command design principles

### 1. Narrow CLI surface

kogoto should expose as few user-facing commands as possible.

The CLI should start and configure refinement sessions.
It should not force users to leave the session to run separate commands for each action.

### 2. Issue-first

The primary unit is a GitHub Issue.

```sh
kogoto refine 123
```

kogoto reads and refines that Issue.

### 3. Chat-driven refinement

Issue refinement happens inside a conversation.

The user can answer questions, approve actions, reject suggestions, or revise interpretations without running additional commands.

### 4. Human-approved side effects

Persistent actions require explicit approval.

Examples:

- posting an Issue comment
- creating child Issues
- updating Issue bodies
- linking Issues
- preparing ADR drafts

### 5. No implementation loop

kogoto commands should not implement code, run tests, manage worktrees, create pull requests, or orchestrate coding agents.

Those responsibilities belong to other tools.

## MVP command

## `kogoto refine <issue-number>`

Start an interactive Issue refinement session.

```sh
kogoto refine <issue-number>
```

Example:

```sh
kogoto refine 123
```

### Responsibilities

`kogoto refine` may:

- read the target Issue
- read relevant Issue comments
- summarize the current state
- identify accepted decisions
- identify rejected alternatives
- identify unresolved questions
- estimate likely implementation impact
- suggest split candidates
- suggest ADR candidates
- propose Issue comments
- propose child Issue creation
- propose parent Issue updates
- propose sibling Issue updates
- execute approved persistent actions

### Default behavior

By default, `kogoto refine` starts a session and performs no persistent GitHub changes without approval.

At session start, kogoto should produce an initial view such as:

```markdown
# Refinement Session

## Current understanding

## Open questions

## Accepted decisions

## Rejected alternatives

## Estimated impact

## Split candidates

## ADR candidates

## Suggested next step
```

The user can then respond in chat.

### Persistent action approval

When kogoto wants to perform a persistent action, it should show the proposed action and ask for approval.

Example:

```text
Proposed action:
Post the following refinement comment to Issue #123.

Proceed?
```

The action is executed only if the user approves.

### Examples of session actions

The following are actions inside a `kogoto refine` session.

They are not separate primary commands in the MVP.

#### Post a refinement comment

kogoto may propose posting a structured refinement comment.

Example:

```text
This summary is now stable enough to record. Should I post it to the Issue?
```

#### Post a decision note

kogoto may propose posting a decision note when the user makes a durable decision.

Example:

```text
This changes the command design. Should I record it as a decision comment?
```

#### Suggest child Issues

kogoto may propose child Issues when the current Issue is too broad.

Example:

```text
This Issue contains three separable concerns. Should I propose child Issues?
```

#### Create child Issues

kogoto may create child Issues only after approval.

Example:

```text
I can create the following two child Issues and link them from the parent. Proceed?
```

#### Update parent Issue

kogoto may propose updating the parent Issue when child Issue discussions change shared assumptions.

Example:

```text
This decision affects the parent Issue summary. Should I update the parent Issue?
```

#### Suggest ADR recording

kogoto may suggest ADR recording when a decision is architectural or durable.

Example:

```text
This looks like an ADR candidate. Should I prepare an ADR draft or post an ADR candidate comment?
```

## Options for `kogoto refine`

The MVP should keep options minimal.

### `--repo`

Specify the target repository.

```sh
kogoto refine 123 --repo tooppoo/kogoto
```

If omitted, kogoto may infer the repository from the current Git remote.

### `--no-post`

Disable persistent posting actions for the session.

```sh
kogoto refine 123 --no-post
```

When enabled, kogoto may still propose comments, but it must not execute GitHub write actions.

This is useful for local review or dry-run usage.

### `--format`

Select output format for non-interactive or exported summaries.

```sh
kogoto refine 123 --format markdown
```

MVP supported value:

```text
markdown
```

Future values may include:

```text
json
yaml
```

### `--non-interactive`

Run a non-interactive analysis and print the refinement report.

```sh
kogoto refine 123 --non-interactive
```

In non-interactive mode, kogoto must not perform persistent actions unless explicit write flags are introduced in the future.

For the MVP, non-interactive mode should be read-only.

## Internal session actions

The following names describe internal capabilities.

They are not primary user-facing CLI commands in the MVP.

- `post_refinement_comment`
- `post_decision_note`
- `post_question_note`
- `post_rejected_alternative`
- `create_child_issue`
- `update_parent_issue`
- `update_child_issue`
- `link_issues`
- `suggest_adr_candidate`
- `prepare_adr_draft`

These may exist internally as implementation units, but the user should normally encounter them as proposals inside a `kogoto refine` session.

## Commands intentionally not included in the MVP

The following commands should not be exposed as primary MVP commands.

```sh
kogoto split
kogoto adr
kogoto discuss
kogoto impact
kogoto sync
kogoto implement
kogoto test
kogoto pr
kogoto review
kogoto run
```

### Reason

These commands either duplicate session actions or move kogoto toward full development workflow orchestration.

The MVP should avoid this.

In particular:

- Issue splitting should happen as a proposal inside `kogoto refine`.
- ADR detection should happen inside `kogoto refine`.
- Discussion recording should happen inside `kogoto refine`.
- Impact estimation should happen inside `kogoto refine`.
- Parent-child synchronization should happen inside `kogoto refine` or future refinement sessions.
- Implementation and testing should remain outside kogoto.

## Future low-level commands

Future versions may introduce low-level commands for scripting or orchestration.

If introduced, they should be treated as advanced or internal commands, not as the primary user experience.

Examples:

```sh
kogoto issue post-comment
kogoto issue create-child
kogoto issue link
kogoto adr prepare
```

These commands should not be required for ordinary refinement work.

An orchestration tool may call such commands, but human users should be able to complete a refinement flow through `kogoto refine`.

## Exit behavior

Recommended exit codes:

```text
0  success
1  general failure
2  invalid arguments
3  GitHub access failure
4  Issue not found
5  refinement generation failure
6  persistent action rejected or cancelled
```

A rejected or cancelled persistent action is not necessarily a session failure.
The exact exit behavior may differ between interactive and non-interactive modes.

## Configuration

Configuration should be minimal in the MVP.

Possible future configuration:

```yaml
github:
  owner: tooppoo
  repo: kogoto

thresholds:
  max_expected_touched_files: 5
  max_expected_added_lines: 300
  max_open_questions: 5

adr:
  enabled: true
  directory: docs/adr

session:
  allow_posting: true
  allow_child_issue_creation: false
```

The MVP should not require configuration if the repository can be inferred.

## MVP summary

The first MVP should implement:

```sh
kogoto refine <issue-number>
```

It should support:

- reading an Issue
- reading comments
- starting a refinement session
- producing a structured understanding
- asking questions
- proposing comments
- posting comments after approval

It should not initially require:

- child Issue creation
- ADR file creation
- parent Issue updates
- sibling Issue synchronization
- implementation
- tests
- pull requests
- worktree management
- orchestration

The CLI surface should remain small.

kogoto should behave as an Issue refinement facilitator, not as a general development automation interface.
