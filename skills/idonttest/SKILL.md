---
name: idonttest
description: >-
  Verify implementation units using tests independently authored from behavioral
  contracts. The implementation agent does not write or inspect generated tests,
  and the test-authoring agent does not inspect implementation bodies. Uses Orca
  orchestration or Paseo agents/workspaces when available.
user-invocable: true
argument-hint: "[unit, symbol, file, or current implementation task]"
---

# idonttest

You implement.

You do not test.

Another agent writes tests from the contract without reading your implementation.

The implementation agent MUST NOT write, inspect, or derive the generated tests.

The test-authoring agent MUST NOT inspect the implementation body.

Tests are derived from specified behavior, not existing implementation.

## Principle

Maintain an information boundary between implementation and verification.

```text
IMPLEMENTER
  sees:
    implementation
    behavioral contract
    public API
    verifier feedback

  must not see:
    generated test source


TEST AUTHOR
  sees:
    behavioral contract
    public API
    allowed type/LSP information
    build metadata needed to compile tests

  must not see:
    implementation body
    implementation diff


RUNNER
  sees:
    implementation
    generated tests

  returns:
    execution evidence

  must not expose:
    generated test source
    implementation excerpts unrelated to the failure
```

Technical ability to access a file does not grant permission to inspect it.

The isolation rules are semantic requirements, not suggestions.

# When to use

Use `idonttest` for unit-level behavioral verification after implementing or modifying:

- a function
- a method
- a class behavior
- an endpoint
- a parser or serializer rule
- a repository operation
- a small cohesive module boundary

Operate on the smallest meaningful behavioral unit.

Do not combine unrelated behaviors into one verification task.

# Orchestration backend

Use an installed orchestration system instead of implementing a custom subagent launcher.

Select one backend for the run.

## Orca

When Orca is available:

1. Load the `orca-cli` skill.
2. Load the `orchestration` skill.
3. Load the version-matched guides from the installed Orca runtime.
4. Use Orca Runs, Tasks, supervised workers, messages, questions, and completion tracking.
5. Use isolated worktrees where necessary to enforce the information boundary.

Use Orca orchestration for the Test Author and any agent-based Runner.

Do not substitute unrelated subagent mechanisms when Orca orchestration is active.

Do not hard-code Orca commands that may drift between versions.

Follow the installed `orca-cli` and `orchestration` skills.

## Paseo

When Paseo is available:

1. Load the `paseo` skill.
2. Call `list_profiles`.
3. Read the configured profile notes before selecting an agent.
4. Prefer a fast or inexpensive coding profile for test generation.
5. Use `create_workspace` when isolation is required.
6. Use `create_agent` for the Test Author.
7. Use `send_agent_prompt` for clarification rounds.
8. Use completion notifications rather than repeatedly polling.

Do not invent model or provider identifiers.

If no profile fits, use Paseo's provider discovery procedure.

Do NOT use `paseo-handoff` for the verification loop.

`paseo-handoff` transfers ownership and intentionally does not supervise completion.

Do NOT use `paseo-advisor` as the Test Author.

Advisor mode prohibits writing code.
