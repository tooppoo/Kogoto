# kogoto

kogoto is a human-in-the-loop tool for refining GitHub Issues.

kogoto helps humans clarify, decompose, and govern Issues before implementation begins.
It does not implement code.
It does not run the full development workflow.
It starts a refinement conversation around an Issue and performs persistent GitHub actions only after human approval.

## Concept

Software development often starts from an Issue that is too large, vague, or unstable to implement safely.

A single Issue may contain multiple hidden concerns:

- What problem is actually being solved?
- Which behavior should change?
- Which files or modules may be affected?
- Which design decisions need to be recorded?
- Which parts should be split into separate Issues?
- Which decisions affect parent, child, or sibling Issues?

kogoto treats an Issue as a place for structured refinement.

The goal is not to turn an Issue into an autonomous implementation task.
The goal is to make the Issue small enough, explicit enough, and stable enough for humans to review with confidence.

## Core idea

kogoto is centered on one primary interaction:

```sh
kogoto refine <issue-number>
```

This command starts an Issue refinement session.

Inside the session, kogoto may:

- read the Issue body and comments
- summarize the current state
- ask clarification questions
- identify accepted decisions
- identify rejected alternatives
- identify unresolved questions
- estimate likely implementation impact
- suggest Issue splitting
- suggest ADR recording
- propose comments to post on GitHub
- propose child Issues to create
- propose parent or sibling Issue updates

Persistent actions are not performed silently.

kogoto may propose an action, but it must ask the user before posting comments, creating Issues, updating Issue bodies, or preparing ADR-related records.

## What kogoto solves

### 1. Issue refinement

kogoto helps refine vague or broad Issues.

It identifies:

- unclear goals
- ambiguous scope
- missing acceptance criteria
- unresolved questions
- hidden design decisions
- likely implementation impact
- possible split boundaries

kogoto does not decide the final scope by itself.
It helps humans notice where judgment is required.

### 2. Issue decomposition

kogoto helps detect when an Issue is too large.

It may suggest decomposition based on:

- number of independent concerns
- number of unresolved decisions
- likely affected files
- likely affected modules
- estimated diff size
- separability of acceptance criteria
- risk of mixing design, documentation, and implementation concerns

kogoto can propose child Issues during a refinement session.

Child Issue creation requires explicit user approval.

### 3. Discussion recording

kogoto helps preserve important reasoning on GitHub Issues.

The goal is not to record every chat message.
The goal is to record decision-relevant information.

Examples of information worth recording:

- accepted decisions
- rejected alternatives
- unresolved concerns
- answered questions
- split rationale
- ADR candidates
- parent-child Issue relationships
- sibling Issue dependencies

### 4. ADR candidate detection

When a discussion produces a persistent design decision, kogoto may suggest recording it as an ADR.

Examples:

- defining the scope of kogoto
- rejecting full workflow orchestration
- introducing a persistent data format
- defining Issue relationship semantics
- changing CLI behavior
- defining a compatibility policy

kogoto does not create or update ADRs silently.
It asks whether the decision should be recorded.

### 5. Parent-child Issue governance

Issue decomposition can make development easier to review, but it also introduces management cost.

kogoto helps reduce that cost by tracking:

- parent Issues
- child Issues
- sibling Issues
- inherited decisions
- local decisions
- cross-Issue effects
- outdated parent Issue summaries
- duplicated or conflicting sibling scopes

The purpose is to support fine-grained work without losing the overall reasoning structure.

## What kogoto does not solve

kogoto intentionally does not cover the full development workflow.

### Implementation

kogoto does not implement code changes.

Implementation should be handled by humans, coding agents, or other tools.

### Test execution and verification

kogoto does not own test execution, coverage analysis, or verification loops.

These belong to test tools, CI, or project-specific scripts.

### Git workflow management

kogoto does not manage worktrees, branches, commits, pull requests, or merge flows.

These belong to Git workflow tools.

### Repository-wide structural analysis

kogoto may inspect likely affected files for Issue refinement, but repository-wide consistency analysis is not its main responsibility.

A separate tool should handle broader repository structure review.

### Workflow orchestration

kogoto is not the orchestrator of the full development workflow.

A separate orchestration tool may combine kogoto, test tools, Git workflow tools, repository analysis tools, coding agents, and CI.

kogoto should remain focused on Issue refinement and decomposition governance.

## Human-in-the-loop principle

kogoto should keep humans in control.

It may analyze, summarize, question, and propose.
It may not silently make persistent project decisions.

The following actions require explicit user approval:

- posting an Issue comment
- creating a child Issue
- updating a parent Issue
- updating a child Issue
- linking Issues
- preparing an ADR draft
- updating ADR-related documentation
- marking an Issue as ready for implementation
- changing the stated scope of an Issue

kogoto should make judgment points visible rather than hiding them behind automation.

## MVP scope

The first MVP focuses on one command:

```sh
kogoto refine <issue-number>
```

The MVP should start an Issue refinement session that can:

1. Read a GitHub Issue.
2. Read relevant Issue comments.
3. Summarize the current Issue state.
4. Identify accepted decisions.
5. Identify rejected alternatives.
6. Identify unresolved questions.
7. Estimate likely implementation impact.
8. Suggest split candidates.
9. Suggest ADR candidates.
10. Propose a refinement comment.
11. Post the comment only after user approval.

The MVP does not need to:

- implement code
- run tests
- manage branches
- create pull requests
- run coding agents
- orchestrate external tools
- automatically create child Issues
- automatically create ADR files

Child Issue creation and ADR draft creation may be designed as future session actions, but they are not required for the first MVP.

## Project boundary

kogoto is not a general-purpose GitHub automation tool.

It is an Issue refinement tool.

Its core responsibility is to help humans move from:

```text
large, vague, unstable Issue
```

to:

```text
smaller, clearer, reviewable Issues with recorded reasoning
```

Implementation begins after kogoto has helped clarify the Issue structure.

## Guiding statement

kogoto exists to make Issue discussion smaller, more explicit, and easier to review.

It does not remove human judgment.
It creates more precise places for human judgment to happen.
