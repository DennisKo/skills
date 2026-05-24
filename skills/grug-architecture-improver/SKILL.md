---
name: grug-brained-reviewer
description: Use when the user wants to inspect a software project for architecture improvements, simplify a codebase, identify refactoring candidates, produce an HTML architecture report, or apply grug-brained engineering patterns to reduce complexity. The skill explores the project organically with codebase-walking agents, surfaces candidate improvements as report cards, and then guides the chosen candidate through a design-grilling conversation before implementation.
---

# Grug Brained Reviewer

## Purpose

Find architecture improvements that reduce complexity without inventing ceremonial architecture. The work is discovery-first: walk the project, notice where understanding gets slow or brittle, connect those observations to grug-brained patterns, then render the evidence-backed candidates in an HTML report.

Load `references/grug-brained-patterns.md` before evaluating candidates.

Invoking this skill means the user wants delegated explorer-agent work for the exploration phase. If the current runtime still requires a more explicit phrase before spawning agents, pause and ask the user to confirm: "Do you want me to spawn explorer sub-agents for this architecture review?"

Progress updates should emphasize the current discovery step, not the final artifact. Do not open with "the skill requires an HTML report" or similar process language. A better first update is: "I’m going to map the repo, spawn focused explorer agents, and follow the friction through the code before writing the report."

## Workflow

### 1. Explore the project

Start by forming a light map of the repository: languages, frameworks, entry points, test structure, major modules, data flow, and developer workflows.

Use the agent/sub-agent tool when available. Spawn explorer agents for distinct, well-scoped codebase walks; this is part of the skill's intended workflow, not an optional enhancement. For example:

- "Trace request/data flow through the main feature area."
- "Find modules with broad imports, circular pressure, or cross-cutting responsibilities."
- "Inspect tests and identify stable cut points versus brittle implementation tests."
- "Map frontend state/data flow and places where behavior is split across too many files."

Do not force a fixed checklist. Explore organically and let friction pull you through the code. Use the grug-brained patterns as lenses while you move:

- **Complexity Refusal / 80-20 Delivery**: notice machinery whose value is unclear, features implemented more generally than the product seems to need, or flows where the simple case pays for every rare case.
- **Delay Factoring Until Shape Emerges**: look for abstractions, base classes, service layers, shared helpers, generic types, or plugin points that appear before the domain has settled.
- **Layer APIs by Use Case**: watch for common callers forced to understand internal lifecycle, builders, transport details, schemas, stores, streams, comparators, or configuration objects.
- **Prefer Locality of Behavior**: follow simple behavior across files and ask whether the split improves independent change or just makes developers chase markup, handlers, stores, validators, styles, and effects.
- **DRY With Judgment**: distinguish real conceptual sameness from textual similarity; duplicated plain code may be healthier than callback-heavy, inheritance-heavy, or generic abstraction.
- **Prefer Integration Tests at Stable Cut Points**: inspect whether tests protect stable behavior or merely pin private implementation details; also note important boundaries with no regression coverage.
- **Chesterton's Fence for Code**: when code is ugly, duplicated, defensive, or oddly shaped, investigate history, tests, logs, callers, and edge cases before proposing removal.
- **Keep Front Ends / Types / Parsers / Concurrency / Services Proportional**: note places where frameworks, type machinery, parser generators, visitor dispatch, async control flow, job systems, logging conventions, or service boundaries add more complexity than they contain.

Keep notes as evidence: files, call paths, repeated concepts, confusing names, awkward APIs, missing tests, and the moment where comprehension slowed down.

### 2. Produce an HTML candidate report

Load and follow `HTML-REPORT.md`. It defines the output location, scaffold, candidate card format, diagram rules, and top recommendation section.


### 3. Grill the selected candidate

When the user picks a candidate, use the `grill-me` skill if it is installed or available. If it is not available, run the same style of grilling conversation directly.

Walk the design tree before coding:

- What constraints are real versus assumed?
- Which current behavior must survive unchanged?
- Which files and APIs become the boundary?
- What dependencies point inward or outward today?
- What would the future code shape look like?
- What tests prove the refactor preserved behavior?
- What is the smallest reversible first step?
- What would make the recommendation wrong?

Ask pointed questions one at a time or in small groups. Push on weak assumptions, hidden coupling, migration risk, observability, testability, ownership, and operational constraints. The goal is not to win an argument; it is to harden the design until implementation is obvious or the candidate is rejected.
