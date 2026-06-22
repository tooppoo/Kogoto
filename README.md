# kogoto

kogoto is a human-in-the-loop tool for refining Issues.

It helps humans turn vague, broad, or unstable Issues into smaller, reviewable discussion units.
kogoto does not implement the Issue.
kogoto does not run the development workflow.
kogoto focuses on the feedback loop before implementation begins.

## Concept

Software development often starts from an Issue that is too large, too vague, or too unstable to implement safely.

A large Issue may contain several hidden decisions:

* What problem is actually being solved?
* Which behavior should change?
* Which files or modules may be affected?
* Which design decisions need to be recorded?
* Which parts should be split into separate Issues?
* Which decisions in a child Issue affect the parent Issue or sibling Issues?

kogoto treats an Issue as a place for structured refinement.

Its purpose is to help humans keep the Issue small enough to review with confidence, while preserving the reasoning process on GitHub.

## What kogoto solves

kogoto helps with the following problems.

### 1. Recording discussion on Issues

kogoto supports a workflow where humans discuss an Issue through chat, while the important points are recorded back to the Issue.

The recorded information should include:

* questions
* answers
* accepted decisions
* rejected alternatives
* unresolved concerns
* split candidates
* ADR candidates
* impact notes

The goal is not to preserve every chat message verbatim.
The goal is to preserve the reasoning that affects the Issue.

### 2. Refining vague Issues

kogoto helps identify when an Issue is not yet ready for implementation.

It may point out:

* unclear goals
* missing acceptance criteria
* unresolved design decisions
* ambiguous scope
* missing affected files or modules
* likely overlap with other Issues
* excessive expected change size

kogoto does not decide the final scope by itself.
It presents refinement questions and asks humans to decide.

### 3. Splitting large Issues

kogoto helps detect when an Issue should be split.

Split suggestions may be based on:

* number of expected touched files
* estimated diff size
* number of independent decisions
* number of affected modules
* separability of acceptance criteria
* dependency between subproblems
* risk of mixing design discussion and implementation work

kogoto should not split Issues silently.
Issue splitting is a human decision.

### 4. Tracking parent, child, and sibling Issue relationships

When an Issue is split, kogoto helps keep the resulting Issue set coherent.

It should track:

* parent Issue
* child Issues
* sibling Issues
* decisions inherited from the parent
* decisions made in a child Issue
* decisions that affect siblings
* decisions that should be reflected back to the parent

The goal is to make Issue decomposition manageable rather than letting it become a source of fragmentation.

### 5. Detecting ADR candidates

When a discussion produces a design decision, kogoto may suggest recording it as an ADR.

Examples:

* choosing a core concept
* rejecting an alternative architecture
* defining a boundary between tools
* changing a workflow principle
* introducing a persistent data format
* defining a compatibility policy

kogoto should ask before creating or updating ADR-related content.

## What kogoto does not solve

kogoto intentionally does not cover the whole development workflow.

The following are outside the core scope.

### Implementation

kogoto does not implement code changes.

Implementation should be handled by humans, coding agents, or other tools.

### Test execution and verification loops

kogoto does not own test execution, coverage analysis, or verification loops.

These may be handled by tools such as CI, or project-specific scripts.

### Git worktree and branch management

kogoto does not manage worktrees, branches, commits, or pull requests.

These may be handled by tools such as git-kura or other Git workflow tools.

### Repository-wide structural analysis

kogoto may inspect likely affected files for Issue refinement, but repository-wide consistency analysis is not its main responsibility.

A separate tool should handle broader repository structure review.

### Workflow orchestration

kogoto is not the orchestrator of the full development workflow.

A separate orchestration tool may combine kogoto, coding agents, and CI.

kogoto should remain a focused tool for Issue refinement.

## MVP scope

The first MVP focuses on producing structured refinement comments for GitHub Issues.

The MVP should support:

1. Reading an Issue and its relevant comments.
2. Summarizing the current state of discussion.
3. Extracting unresolved questions.
4. Extracting accepted decisions.
5. Extracting rejected alternatives.
6. Identifying ADR candidates.
7. Estimating whether the Issue is too large.
8. Suggesting split candidates.
9. Posting a refinement comment to the Issue.

The MVP does not need to:

* create child Issues automatically
* create ADR files automatically
* run implementation
* run tests
* open pull requests
* manage branches or worktrees
* orchestrate other tools

## Guiding principle

kogoto should keep humans in control.

It should make Issues smaller, clearer, and easier to review.
It should not hide decisions behind automation.
It should not turn vague Issues into implementation tasks without human judgment.

kogoto exists to preserve and structure the reasoning before implementation begins.
