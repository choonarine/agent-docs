---
name: idonttest
description: >-
  Verify implementation units using tests independently authored from behavioral
  contracts. The implementation agent does not write or inspect generated tests,
  and the test-authoring agent does not inspect implementation bodies. Prefer
  contract-first verification and strong workspace isolation. Uses Orca
  orchestration or Paseo agents/workspaces when available.
user-invocable: true
argument-hint: "[unit, symbol, file, or current implementation task]"
---

# idonttest

You implement.

You do not test.

Another agent writes tests from the behavioral contract without reading your implementation.

The implementation agent MUST NOT write or inspect generated idonttest tests.

The Test Author MUST NOT inspect implementation bodies or implementation diffs.

Tests are derived from specified behavior, not observed implementation behavior.

## Core principle

Keep specification, implementation, and independent verification separated.

The Implementer derives behavior from requirements and the behavioral contract.

The Test Author derives expected behavior from the behavioral contract and permitted public API information.

The Runner is the only role that needs access to both the implementation and generated verification tests.

The Test Author must not discover expected behavior by observing the real implementation.

The Implementer must not adapt the implementation to hidden test details that are not justified by the behavioral contract.

Technical ability to access a file does not grant permission to inspect it.

These information boundaries are requirements of the skill.

## Preferred workflow

Prefer contract-first verification.

When requirements are available, create and freeze the behavioral contract before implementation begins.

After the contract is frozen, implementation and independent test authoring should both use that contract as their behavioral authority.

Writing the contract after implementation is permitted when:

- verifying existing code
- adopting idonttest in an existing project
- the unit was already implemented before idonttest was invoked

A contract derived after implementation may unintentionally reflect implementation choices.

When this happens, record the contract origin as `POST_IMPLEMENTATION` rather than claiming full contract-first independence.

## When to use

Use idonttest for independently verifying a small behavioral unit such as:

- function
- method
- class behavior
- endpoint
- parser rule
- serializer
- repository operation
- state transition
- small cohesive module boundary

Operate on the smallest meaningful behavioral unit.

Do not combine unrelated behaviors merely to reduce the number of agent calls.

## Orchestration backend

Use an installed orchestration system instead of implementing a custom subagent launcher.

Select one supported backend for the run.

### Orca

When Orca is available:

1. Load the `orca-cli` skill.
2. Load the `orchestration` skill.
3. Load the version-matched guides supplied by the installed Orca runtime.
4. Use Orca Runs, Tasks, supervised workers, messages, questions, and completion tracking.
5. Prefer isolated worktrees when they improve enforcement of the information boundary.

Use Orca orchestration for Test Authors and agent-based Runners.

Do not replace Orca orchestration with unrelated subagent mechanisms while Orca orchestration is active.

Do not hard-code Orca commands that may change between versions.

Follow the installed `orca-cli` and `orchestration` skills.

### Paseo

When Paseo is available:

1. Load the `paseo` skill.
2. Call `list_profiles`.
3. Read the configured profile notes before selecting an agent.
4. Prefer a fast or inexpensive coding profile for independent test authoring.
5. Use `create_workspace` when workspace isolation is required.
6. Use `create_agent` for the Test Author.
7. Use `send_agent_prompt` for clarification and test revision rounds.
8. Rely on Paseo completion notifications instead of repeatedly polling running agents.

Do not invent provider or model identifiers.

If no configured profile fits, follow Paseo's provider discovery procedure.

Do NOT use `paseo-handoff` for the iterative verification loop.

`paseo-handoff` transfers ownership and does not provide the supervised interaction required by idonttest.

Do NOT use `paseo-advisor` as the Test Author.

Advisor mode prohibits writing code.

### No supported backend

If neither Orca nor Paseo is available, report that independent-agent isolation cannot be guaranteed.

Do not silently use the current agent as both Implementer and Test Author.

## Isolation levels

Record the effective isolation level for each idonttest run.

### STRONG

Use `STRONG` when the Test Author cannot access the modified implementation body through its normal workspace or tooling.

Examples include:

- a separate worktree based on the pre-implementation revision
- generated declaration or interface files
- public headers without implementation
- API stubs
- a restricted exported API surface

Prefer strong isolation whenever practical.

### POLICY

Use `POLICY` when implementation files are technically reachable but the Test Author is explicitly forbidden from inspecting them.

Policy isolation may be necessary when repository layout or language tooling makes strong isolation impractical.

Do not describe policy isolation as strong isolation.

## Roles

### Implementer

The current primary agent is the Implementer.

The Implementer is responsible for:

- establishing or maintaining the behavioral contract
- implementing the unit
- classifying verification feedback
- fixing implementation defects
- revising specification when genuinely necessary
- deciding whether a failure belongs to implementation, specification, test assumptions, API design, or environment

The Implementer MUST NOT:

- author generated idonttest tests
- open generated idonttest test files
- inspect generated test diffs
- request generated test contents
- ask another agent to summarize hidden test source
- reconstruct hidden assertions from stack traces
- change behavior merely to satisfy an unspecified test expectation

### Test Author

Prefer a cheaper subordinate coding agent for the Test Author role.

The Test Author is responsible for:

- reading the behavioral contract
- reading permitted public API information
- understanding project testing conventions
- identifying specification ambiguity
- creating verification tests
- publishing tests as an opaque artifact
- revising affected tests when the contract changes

The Test Author MUST NOT:

- inspect implementation bodies
- inspect implementation diffs
- inspect private helpers introduced by the implementation
- derive expected behavior from runtime observations of the real implementation
- modify production implementation
- weaken tests because the current implementation fails them
- invent semantics when the contract is ambiguous

### Runner

The Runner combines the current implementation with the generated tests and executes verification.

Prefer deterministic build and test commands over another LLM whenever possible.

Examples include:

```text
ctest
cargo test
go test
pytest
npm test
gradle test
mvn test
dotnet test
```

Use an agent as Runner only when environment preparation, orchestration, or result interpretation genuinely requires one.

The Runner does not decide intended product semantics.

## Information boundaries

The Implementer may receive:

- requirements
- behavioral contract
- implementation
- public API information
- structured verification evidence
- specification questions

The Implementer must not receive generated test source.

The Test Author may receive:

- behavioral contract
- public API
- permitted repository context
- testing framework conventions
- fixtures and test helpers
- type information
- build metadata
- permitted LSP metadata

The Test Author must not receive modified implementation bodies or real implementation behavior.

The Runner may access:

- implementation
- generated tests
- build and test environment

The Runner should return only the minimum evidence required for adjudication.

## Behavioral contract

Create the behavioral contract before independent test authoring.

Prefer to create and freeze it before implementation.

Describe externally observable semantics only.

Use this form:

```text
Unit:
Purpose:

Public surface:
- module/import path
- symbol
- parameters
- return type
- externally visible types

Preconditions:

Behavior:
- ...

Postconditions:

Errors:
- ...

Side effects:
- ...

Invariants:
- ...

Boundary-visible dependencies:
- ...

Explicit non-behavior:
- behavior callers must not rely on
```

Be precise whenever different interpretations would produce different tests.

Bad:

```text
Cache entries expire after some time.
```

Good:

```text
An entry becomes expired exactly 300 seconds after insertion according to the injected Clock.
```

Do not include implementation details unless they are themselves part of the externally guaranteed contract.

Do not include:

- private helper names
- internal algorithms
- internal data structures
- control flow
- implementation-specific call sequences
- copied implementation code

## Contract freeze

Once implementation begins, treat the behavioral contract as frozen unless one of the following occurs:

- the original requirement was incomplete
- the contract was ambiguous
- the intended public behavior changed
- the API cannot express the intended semantics

Do not edit the contract merely because the implementation fails to satisfy it.

Every post-freeze contract revision must represent an explicit specification clarification or behavioral change.

Track contract revisions separately from implementation revisions.

## Public surface

The Test Author may receive only the structural information required to consume the unit.

Allowed information includes:

- public symbol names
- signatures
- public types
- package or module paths
- documented errors
- public constants when contractually relevant
- imports required to consume the API
- compiler and type diagnostics
- LSP type and symbol metadata

Forbidden information includes:

- function or method bodies
- private implementation symbols
- implementation diffs
- private call hierarchy
- implementation-specific references
- go-to-definition output that reveals modified implementation bodies

LSP is a structural aid.

LSP must not be used as a loophole for inspecting implementation.

If the language cannot naturally separate public API from implementation, create a temporary public-surface artifact such as:

- declaration file
- header
- interface
- type stub
- API description

The public-surface artifact must not contain implementation logic.

## Existing repository tests

The Test Author MAY inspect existing repository tests for project conventions.

Allowed uses include:

- test framework usage
- fixture construction
- test helper APIs
- naming conventions
- setup and teardown conventions
- directory structure
- mocking conventions

Existing tests MUST NOT be treated as authoritative behavioral specification unless the behavioral contract explicitly references them.

Expected behavior must come from the contract.

Existing repository tests and idonttest tests serve different purposes.

Repository tests provide ordinary project feedback.

idonttest tests provide independent contract-derived verification.

Passing existing tests does not count as independent idonttest verification.

## Workspace isolation

Maintain separate logical environments for implementation, test authoring, and execution whenever practical.

The implementation environment contains the behavioral contract and current implementation.

The Test Author environment should preferably use the pre-implementation project revision together with the behavioral contract, permitted public API surface, test infrastructure, and generated tests.

The Test Author environment should not contain the modified implementation body when strong isolation is possible.

The Runner environment contains both the current implementation revision and generated test artifact because execution requires both.

If the new public API did not exist before implementation, provide its structural surface separately rather than exposing the implementation commit to the Test Author.

## Test artifact

Generated tests should be transferred using an opaque artifact reference.

Examples include:

- commit SHA
- branch
- patch artifact
- archived test directory
- workspace artifact

The Implementer receives the artifact reference, not the artifact contents.

## Verification procedure

### Phase 0 — Establish the contract

Derive the behavioral contract from requirements.

Resolve obvious ambiguity before implementation whenever possible.

Freeze the contract.

When verifying pre-existing code, derive the most behavior-focused contract possible and record its origin as `POST_IMPLEMENTATION`.

### Phase 1 — Implement the unit

Implement the unit against the frozen behavioral contract.

Do not author independent idonttest tests yourself.

Existing project tests may still be executed during normal development.

They do not count as idonttest verification.

### Phase 2 — Create the Test Author

Create a Test Author through Orca or Paseo.

Provide:

- behavioral contract
- public API surface
- testing framework information
- permitted repository tests and helpers
- build metadata required for test authoring

Do not provide:

- implementation diff
- modified implementation body
- private implementation design notes
- runtime observations from the real implementation

Use instructions equivalent to:

```text
You are the idonttest Test Author.

Generate verification tests solely from the supplied behavioral contract and public API.

You may inspect existing repository tests only for testing conventions, fixtures,
helpers, framework usage, and structure.

Existing tests are not behavioral authority unless explicitly referenced by the contract.

You may use permitted type information, compiler diagnostics, imports,
and structural LSP metadata.

You MUST NOT inspect implementation bodies or implementation diffs.

Do not modify production code.

Do not run or inspect the real implementation to discover its behavior.

If the contract does not uniquely determine an expected result, do not guess.
Report the ambiguity.

Return only:
- opaque test artifact reference
- number of tests or cases created
- specification questions
- blocked cases
- tooling problems

Do not return test source.
Do not provide chain-of-thought.
Do not produce numeric confidence scores.
```

### Phase 3 — Validate the artifact

Verify mechanically that the Test Author did not modify production implementation files.

Prefer metadata-based checks such as:

- changed path list
- commit statistics
- artifact manifest
- allowed-directory checks

Do not inspect generated test contents merely to validate the artifact.

If production implementation files were modified, reject the artifact and request correction.

### Phase 4 — Execute verification

Combine the current implementation revision and opaque generated test artifact in the Runner environment.

Run the smallest relevant test target first.

Prefer deterministic execution.

The Runner should return:

```yaml
status: passed | failed | blocked | environment_error

unit: <unit>

summary:
  passed: <count>
  failed: <count>
  skipped: <count>

issues:
  - type: BEHAVIOR_MISMATCH | SPEC_AMBIGUITY | TEST_ASSUMPTION | API_DESIGN | ENVIRONMENT
    case: <stable opaque identifier>
    expected: <contract-level expectation>
    observed: <observable behavior>
    question: <only when clarification is required>
    evidence:
      - <minimal behavioral/compiler/runtime evidence>

test_artifact: <opaque reference>
```

Do not expose generated test source.

Do not quote assertion implementations.

Do not return complete test stack traces when a reduced behavioral description is sufficient.

Bad:

```text
tests/email_test.py:84:
assert normalize(user.email) == "foo@example.com"
```

Good:

```text
case: duplicate-email-case-insensitive
expected: duplicate addresses differing only by case are rejected
observed: second address was accepted
```

## Information flow after execution

Runtime observations from the real implementation belong only to the Runner and Implementer.

NEVER send real implementation observations back to the Test Author.

The Test Author may receive only:

- behavioral contract revisions
- public API revisions
- corrections to unsupported assumptions
- test compilation or tooling feedback that does not expose real implementation behavior

Do not send the Test Author:

- actual returned values from failing implementation
- internal exception details revealing implementation structure
- runtime call paths
- implementation stack traces
- descriptions of how the real implementation currently behaves

This prevents later test revisions from becoming implementation-guided.

## Adjudication

The Implementer classifies each issue before taking corrective action.

### BEHAVIOR_MISMATCH

Use this classification when the contract is clear and the implementation violates it.

Action:

- fix implementation
- preserve the contract
- preserve the existing generated tests
- rerun verification

### SPEC_AMBIGUITY

Use this classification when multiple reasonable expectations are permitted by the current contract.

Action:

- clarify the contract
- record a contract revision
- send only the relevant contract revision to the Test Author
- update affected tests
- rerun verification

### TEST_ASSUMPTION

Use this classification when a generated test requires behavior not guaranteed by the contract.

Action:

- tell the Test Author which guarantee does not exist
- request correction of the affected test
- do not modify production behavior solely to satisfy that assumption

Do not reveal actual implementation behavior while correcting the assumption.

### API_DESIGN

Use this classification when the intended behavior cannot be expressed or observed cleanly through the current public interface.

Action:

- revise the public API
- revise the behavioral contract
- request updates to affected tests
- rerun verification

### ENVIRONMENT

Use this classification when failure originates from infrastructure rather than product behavior.

Examples include:

- dependency failure
- build configuration
- permissions
- unsupported platform
- missing fixture
- external service failure
- broken test tooling

Action:

- repair the environment
- preserve product semantics
- rerun verification

## Critical test reuse rule

Implementation changes do not invalidate tests by themselves.

When only the implementation changes, reuse the same generated tests.

Do NOT regenerate tests.

Do NOT call the Test Author again.

This rule is essential for:

- test independence
- preventing tests from adapting to the implementation
- reducing token usage
- maintaining a stable verification target

Call the Test Author again only when:

- the behavioral contract changed
- the public API changed
- a test contained an unsupported assumption
- the test artifact was structurally defective
- test tooling itself needs correction

## Clarification loop

When expected behavior cannot be uniquely determined, the Test Author must ask instead of guessing.

Example:

```text
The contract says duplicate email comparison is case-insensitive.

Should persistence preserve caller-provided casing,
or should the stored value also be normalized?
```

For Orca, use its orchestration question or message mechanism where appropriate.

For Paseo, continue the existing Test Author using `send_agent_prompt`.

Prefer reusing the same Test Author for the entire unit.

Do not create a fresh Test Author for each clarification.

## Contract revisions

When the contract changes, communicate the smallest useful delta.

Example:

```text
Contract revision:

Previously:
Email comparison is case-insensitive.

Now:
Duplicate detection is case-insensitive.
Persistence preserves caller-provided casing.
```

Do not resend unrelated implementation context.

Do not tell the Test Author which behavior the real implementation currently exhibits.

## Test quality

Tests should distinguish intended behavior from plausible incorrect behavior.

The Test Author may create:

- boundary cases
- equivalence-class cases
- invalid-input cases
- invariant checks
- state-transition checks
- error-path checks

All expectations must remain derivable from the behavioral contract.

The Test Author must not inspect the real implementation to choose cases.

Optional mutation testing may be used when deterministic mutation tooling already exists.

Mutation testing must:

- operate mechanically
- use temporary implementation variants
- keep generated tests hidden from the Implementer
- remain optional rather than mandatory for every unit

## Token discipline

Keep expensive-model context focused on implementation and specification.

Therefore:

- never paste generated tests into coordinator messages
- never paste implementation into Test Author prompts
- pass artifact and revision identifiers instead
- reuse tests after implementation-only changes
- reuse the same Test Author during clarification
- send contract deltas instead of repeating unchanged context where practical
- report only relevant failing evidence
- suppress successful test logs
- avoid repeated summaries
- do not request chain-of-thought
- do not request confidence percentages
- do not reproduce long compiler or test output unnecessarily

## Completion criteria

A unit passes idonttest only when all of the following are true:

- verification status is `passed`
- no unresolved specification questions remain
- no unresolved environment failures remain
- the current public API matches the current behavioral contract

A green test command alone is insufficient while specification ambiguity remains.

## Loop ceiling

Do not create an endless correction loop.

If the same unit remains unresolved after four adjudication cycles:

1. stop automatic iteration
2. preserve implementation and test artifacts
3. identify the unresolved issue category
4. summarize the remaining semantic disagreement
5. request user input only when a genuine product decision is required

Do not expose generated tests while reporting the blocker.

## After verification

Generated tests remain opaque to the Implementer while idonttest verification is active.

After successful verification, project policy determines whether generated tests are:

- discarded
- archived as verification artifacts
- mechanically merged into the repository

The Implementer does not need to inspect generated tests before a mechanical merge.

Once merged and treated as ordinary repository tests, they no longer need to remain hidden during future development.

## Final report

Return a compact report using this form:

```text
idonttest: <unit>

Result: PASS | BLOCKED
Isolation: STRONG | POLICY
Contract origin: CONTRACT_FIRST | POST_IMPLEMENTATION

Implementation revisions: <count>
Contract revisions: <count>
Test revisions: <count>

Resolved:
- <important implementation or specification findings>

Remaining:
- none
```

Do not print generated test source.

Do not print internal orchestration transcripts.
