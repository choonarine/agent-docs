---
name: yoink
description: Yoink coding conventions, design habits, idioms, and code smells from an existing codebase and turn them into practical coding guidance.
---

# yoink

Analyze an existing codebase and extract the conventions that make its code look and behave like itself.

Do not only describe formatting or naming.

Find the recurring decisions behind the code:

- naming
- file and directory organization
- class and type design
- ownership
- object lifetime
- initialization and shutdown
- memory management
- error handling
- API design
- data structures
- abstraction boundaries
- dependency direction
- build and platform conventions
- concurrency
- performance-oriented idioms
- common implementation patterns
- deliberately avoided language features
- legacy patterns
- recurring code smells

The result must be useful as a coding guide, not only as a repository summary.

## Core rule

Do not infer a convention from a small number of examples.

Find repeated evidence across the codebase.

Distinguish between:

- a project-wide convention
- a subsystem-specific convention
- an implementation idiom
- an architectural invariant
- a compatibility workaround
- a performance optimization
- a legacy pattern
- an accidental pattern
- a code smell

A frequent pattern is not automatically a good rule.

A strange pattern is not automatically a mistake.

Determine why the pattern exists before recommending that another piece of code reproduce it.

## Analysis procedure

Analyze the code in the following order.

### 1. Observe

Inspect the repository before forming rules.

Look at representative files from different parts of the project.

Do not base the analysis only on one subsystem or a few large files.

Collect repeated patterns first.

Examples:

- class names often start with `C`
- engine objects commonly use raw pointers
- constructors perform little work
- `Init()` and `Shutdown()` appear repeatedly
- the project uses a custom container instead of `std::vector`
- implementation files include their matching header first

At this stage, state observations only.

Do not convert observations directly into recommendations.

### 2. Compare

Check whether each pattern appears consistently.

Compare:

- old and new code
- public and internal APIs
- core and peripheral modules
- platform-specific and platform-independent code
- performance-sensitive and ordinary code
- engine code and tooling code

A pattern that exists only in one subsystem must not become a project-wide rule.

When repository history is available, use it to distinguish current conventions from historical conventions.

Prefer current direction over raw occurrence count.

For example, if older code uses `NULL` but newer code consistently uses `nullptr`, do not recommend `NULL` only because it appears more often.

### 3. Infer

For each strong pattern, determine its likely purpose.

Ask:

- What problem does this solve?
- What constraint caused this pattern?
- Does the constraint still exist?
- Is the pattern related to performance?
- Is it related to ABI stability?
- Is it related to portability?
- Is it related to object lifetime?
- Is it related to compiler or language limitations?
- Is it related to readability?
- Is it related to tooling?
- Is it simply historical?

Prefer explanations supported by surrounding code, comments, documentation, build files, tests, and repository history.

Do not invent project philosophy when the evidence does not support it.

If the reason cannot be determined, say that the reason is unclear.

### 4. Challenge

Before promoting a pattern into a rule, challenge it.

Ask:

- Is this actually intentional?
- Is this only legacy code?
- Is this caused by an obsolete compiler or platform?
- Does newer code still follow it?
- Is there evidence that maintainers are moving away from it?
- Does it improve consistency but reduce safety?
- Does it improve performance but increase complexity?
- Does it depend on another architectural rule?
- Would copying it outside its original context create a bug?

Do not preserve a bad practice only for consistency.

Local consistency is useful when there is no stronger technical reason to choose another approach.

Legacy consistency alone is not sufficient justification for reproducing obsolete design.

### 5. Write

Turn validated patterns into practical rules.

The reader must be able to use the result while writing new code.

Do not produce a statistical report and stop there.

## Rule strength

Use RFC-style normative terms consistently.

### MUST

Use `MUST` only when violating the rule would break an architectural invariant, compatibility requirement, correctness requirement, or another strongly established constraint.

### MUST NOT

Use `MUST NOT` for patterns that the project clearly prohibits or that would violate a required invariant.

### SHOULD

Use `SHOULD` for the normal project convention when legitimate exceptions exist.

### SHOULD NOT

Use `SHOULD NOT` when a pattern is normally avoided but may be justified in special cases.

### MAY

Use `MAY` when multiple approaches are intentionally acceptable.

Do not promote a rule to `MUST` because it is merely common.

Frequency determines evidence strength, not normative strength.

## Writing style

Write technical guidance in an approximately ASD-STE100-inspired style.

The goal is clarity, not strict compliance with aerospace documentation rules.

Use short and direct sentences.

Prefer active voice.

Use one term for one concept.

Do not alternate between synonyms only to vary the prose.

For example, if the document uses `initialize`, continue to use `initialize`. Do not alternate between `initialize`, `set up`, `prepare`, and `bootstrap` unless they refer to different operations.

Put conditions before actions when the condition changes the meaning of the instruction.

Prefer:

> If the manager owns the object, store a non-owning pointer.

Avoid:

> Store a non-owning pointer unless ownership happens to be transferred by the manager.

Avoid unnecessary hedging.

Avoid phrases such as:

- generally speaking
- perhaps
- it may be a good idea to
- in many cases it could be useful to
- usually you might want to

Use the correct normative term instead.

Keep C++, engine, platform, and project-specific technical terms when they improve precision.

Do not simplify a technical term into a less accurate everyday term.

## Writing each convention

Start each rule with the rule itself.

Then explain why it exists.

Use this structure when the available evidence supports each section:

```markdown
## <Rule name>

<One short normative rule.>

### Why

Explain the reason for the convention.

### Pros

Explain the useful properties of the convention.

### Cons

Explain its costs, risks, or limitations.

### Use this when

Describe the context where the rule applies.

### Avoid this when

Describe important exceptions or contexts where copying the rule would be harmful.

### Example

Show a small representative example.

### Evidence

Point to representative files, symbols, modules, or patterns that support the rule.
```

Do not create empty sections.

Do not invent Pros, Cons, exceptions, or rationale only to satisfy the template.

Simple rules do not require long explanations.

For example, a naming rule may need only a rule, short rationale, and examples.

## Preserve context

Never turn a contextual pattern into an unconditional rule.

Bad:

> MUST use raw pointers.

Better:

> SHOULD use raw pointers for non-owning references to objects whose lifetime is controlled by the owning subsystem.

Bad:

> MUST initialize objects with `Init()`.

Better:

> SHOULD use explicit `Init()` or `Create()` functions for objects whose initialization can fail or requires external resources.

The generated rule must preserve the condition that made the original pattern valid.

## Legacy code and code smells

The skill intentionally analyzes code smells.

Do not hide them.

Do not automatically recommend them either.

When a repeated pattern appears obsolete, risky, or historically constrained, mark it clearly.

Use labels when useful:

- Current convention
- Legacy convention
- Subsystem convention
- Compatibility pattern
- Performance idiom
- Workaround
- Code smell

Explain the trade-off.

For example:

```markdown
## Null pointer literals

**Legacy convention**

Older parts of the codebase use `NULL`.

Newer code uses `nullptr`.

### Why

Much of the older code predates the project's adoption of modern C++.

### Pros

Using `NULL` can preserve local consistency when modifying tightly coupled legacy code.

### Cons

`nullptr` provides clearer type semantics.

### Recommendation

SHOULD use `nullptr` in new code.

MAY preserve `NULL` when making a small change inside legacy code where local consistency is more valuable than migration.
```

Never treat old code as automatically authoritative.

Never silently modernize the extracted style either.

Describe the original convention first, then explain whether it should still be reproduced.

## Examples

Prefer examples from the analyzed project.

Keep examples small.

Show only the code needed to demonstrate the rule.

Do not repeatedly reproduce large blocks of source code.

When possible, reference the original file or symbol instead.

Do not create synthetic examples when a clear real example exists.

If a synthetic example is necessary, label it as an illustrative example.

## Evidence

Every substantial convention must have evidence.

Evidence can include:

- repeated source patterns
- comments
- documentation
- tests
- build configuration
- static assertions
- API structure
- repository history
- migration patterns
- compiler configuration

Do not present speculation as evidence.

Do not calculate or print arbitrary confidence scores.

Use plain descriptions of evidence instead.

For example:

> This pattern appears in the renderer, audio, and world subsystems.

or:

> Older files use `NULL`, while files added after the C++17 migration use `nullptr`.

## Repository scale

Do not attempt to understand a large repository by reading files sequentially from beginning to end.

First identify:

- top-level directories
- major subsystems
- public APIs
- core types
- common base classes
- central managers
- allocator or memory facilities
- build configuration
- platform abstraction layers
- frequently included headers

Then sample representative code from each area.

Follow dependencies when a pattern requires more context.

Read the actual source before making claims about its contents.

Do not infer the contents of unread files from their names.

## Focus on decisions

Do not spend most of the output documenting automatic formatting.

Formatting can be included when it is distinctive, but prioritize decisions that affect how code is designed.

High-value conventions include:

- who owns an object
- who destroys it
- whether initialization can fail
- whether constructors allocate resources
- whether exceptions are allowed
- how errors propagate
- which abstractions cross module boundaries
- which containers are allowed
- whether RTTI is used
- whether dynamic allocation is restricted
- how platform code is isolated
- how interfaces express ownership
- how state is synchronized
- which modern C++ features are avoided
- which engine-specific utilities replace standard facilities

These patterns have more value than indentation rules.

## Output

Create a concise coding guide that can be used directly while reading or writing code.

Prefer a structure similar to:

```markdown
# <Project> Coding Guide

## Background

## Core principles

## Files and modules

## Naming

## Types and classes

## Ownership and lifetime

## Initialization and shutdown

## Memory management

## Containers and strings

## Error handling

## API design

## Concurrency

## Performance

## Platform code

## Build conventions

## Legacy patterns and code smells
```

Only include sections supported by the repository.

Do not create sections only because they appear in this template.

Put the most important and most reusable conventions first.

Avoid turning the result into a repository tour.

The document must explain how code in this project is expected to be written and why.

## Final review

Before finishing, check every important rule.

Verify that:

1. the rule has repeated or authoritative evidence;
2. the rule is scoped to the correct part of the project;
3. historical patterns are not confused with current direction;
4. frequency is not confused with correctness;
5. the reason for the rule is stated when it can be determined;
6. important trade-offs are visible;
7. exceptions are preserved;
8. normative language matches the actual strength of the convention;
9. terminology is consistent;
10. the prose is short, direct, and unambiguous.

Remove rules that cannot survive this review.

The goal is not to collect as many conventions as possible.

The goal is to extract the conventions that explain how the codebase thinks.