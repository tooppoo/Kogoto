# kogoto Commands

This document defines provisional CLI commands for the kogoto MVP.

The command design is intentionally narrow.
kogoto focuses on Issue refinement, not implementation.

## Design principles

### 1. Issue-first

Most commands operate on a GitHub Issue.

```sh
kogoto refine 123
```

The Issue is the primary unit of discussion, refinement, and decomposition.

### 2. Human-in-the-loop

Commands should propose changes before making persistent changes.

Posting comments may be allowed with explicit flags.
Creating Issues, updating Issue bodies, or writing ADRs should require explicit confirmation.

### 3. No implementation loop

Commands should not implement code, run tests, manage worktrees, create PRs, or orchestrate coding agents.

### 4. Structured Markdown output

The MVP should produce human-readable Markdown.

Machine-readable output may be added later, but the first target is readable Issue comments.

## Command list

## `kogoto refine`

Analyze an Issue and produce a structured refinement report.

```sh
kogoto refine <issue-number>
```

Default behavior:

* read the Issue
* read relevant comments
* summarize current understanding
* list accepted decisions
* list rejected alternatives
* list open questions
* estimate expected impact
* suggest split candidates
* suggest ADR candidates
* print the result to stdout

Example:

```sh
kogoto refine 123
```

### Options

```sh
kogoto refine 123 --post
```

Post the refinement report as an Issue comment.

```sh
kogoto refine 123 --dry-run
```

Print the report without posting.
This should be the default behavior.

```sh
kogoto refine 123 --format markdown
```

Select output format.

MVP supported format:

* `markdown`

Future formats may include:

* `json`
* `yaml`

### MVP output sections

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

## `kogoto discuss`

Record a human discussion note on an Issue.

```sh
kogoto discuss <issue-number> --body "..."
```

This command posts a structured discussion note to the Issue.

The MVP may require `--body` explicitly.
If `--body` is missing, the command should fail rather than opening an editor.

Example:

```sh
kogoto discuss 123 --body "We decided that kogoto should not own implementation loops."
```

### Options

```sh
kogoto discuss 123 --body "..." --type decision
```

Possible discussion types:

* `note`
* `question`
* `answer`
* `decision`
* `rejection`
* `concern`

The discussion type is metadata for future refinement.

### Output format

A posted discussion comment may use:

```markdown
# Discussion Note

Type: decision

We decided that kogoto should not own implementation loops.
```

## `kogoto split`

Suggest child Issues for a broad Issue.

```sh
kogoto split <issue-number>
```

Default behavior:

* analyze the Issue
* detect separable concerns
* propose child Issue candidates
* print candidates to stdout
* do not create Issues

Example:

```sh
kogoto split 123
```

### Options

```sh
kogoto split 123 --post
```

Post split candidates as a comment on the parent Issue.

```sh
kogoto split 123 --create
```

Create child Issues.

`--create` should require explicit confirmation in interactive mode.
In non-interactive mode, it should require an additional confirmation flag.

Possible future option:

```sh
kogoto split 123 --create --yes
```

This should not be part of the first MVP unless necessary.

### Candidate format

Each split candidate should include:

* proposed title
* purpose
* included scope
* excluded scope
* dependency
* expected affected files
* reason for split

## `kogoto impact`

Estimate expected implementation impact for an Issue.

```sh
kogoto impact <issue-number>
```

Default behavior:

* infer likely affected files
* infer likely affected documents
* infer likely affected commands
* estimate size
* estimate uncertainty
* warn if thresholds may be exceeded

Example:

```sh
kogoto impact 123
```

### MVP metrics

The MVP may estimate:

* expected touched files
* expected new files
* expected deleted files
* expected diff size
* affected modules
* affected docs
* number of unresolved questions
* uncertainty level

These are estimates, not measured facts.

### Thresholds

Future configuration may define thresholds such as:

```yaml
max_expected_touched_files: 5
max_expected_added_lines: 300
max_open_questions: 5
```

If thresholds are exceeded, kogoto should suggest splitting the Issue.

## `kogoto adr`

Detect ADR candidates in an Issue.

```sh
kogoto adr <issue-number>
```

Default behavior:

* read Issue discussion
* detect design decisions
* list ADR candidates
* explain why each candidate may deserve ADR recording
* do not create ADR files

Example:

```sh
kogoto adr 123
```

### Options

```sh
kogoto adr 123 --post
```

Post ADR candidates as an Issue comment.

```sh
kogoto adr 123 --create
```

Create an ADR draft.

`--create` should be outside the first MVP unless the ADR location and template are already configured.

### ADR candidate format

```markdown
# ADR Candidates

## Candidate 1

Decision:
Limit kogoto scope to Issue refinement and decomposition governance.

Reason:
This defines the boundary between kogoto and orchestration tools.

Suggested action:
Ask whether this should be recorded as an ADR.
```

## `kogoto sync`

Check consistency between parent, child, and sibling Issues.

```sh
kogoto sync <issue-number>
```

This command is not required for the first MVP, but it represents an important future direction.

Default behavior:

* detect parent Issue
* detect child Issues
* detect sibling Issues
* compare decisions
* detect scope overlap
* detect outdated parent summaries
* detect decisions that should propagate

Example:

```sh
kogoto sync 123
```

### MVP status

`kogoto sync` should probably be documented as future work, not implemented first.

The first MVP should focus on `refine`.

## MVP command set

The first MVP should implement only:

```sh
kogoto refine <issue-number>
```

Optional MVP extension:

```sh
kogoto refine <issue-number> --post
```

The following commands may be documented as provisional but should not be implemented first:

* `kogoto discuss`
* `kogoto split`
* `kogoto impact`
* `kogoto adr`
* `kogoto sync`

## Recommended MVP behavior

For the first implementation, kogoto should:

1. Accept an Issue number.
2. Fetch the Issue body and comments.
3. Produce a Markdown refinement report.
4. Print the report to stdout.
5. Optionally post the report with `--post`.

It should not:

* create child Issues
* update Issue bodies
* create ADR files
* modify repository files
* run tests
* create branches
* create pull requests
* call implementation agents

## Exit behavior

Recommended exit behavior:

* `0`: command completed successfully
* `1`: general failure
* `2`: invalid arguments
* `3`: GitHub access failure
* `4`: Issue not found
* `5`: refinement generation failure

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
```

The MVP should not require configuration if repository context can be inferred.

## Non-goals

The CLI should not become a general development automation interface.

Avoid commands such as:

```sh
kogoto implement
kogoto test
kogoto pr
kogoto review
kogoto run
```

Those belong to implementation agents, test tools, Git workflow tools, or orchestration tools.

kogoto should remain focused on Issue refinement.
