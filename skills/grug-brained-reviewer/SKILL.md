---
name: grug-brained-reviewer
description: Use when the user wants to inspect a software project for architecture improvements, find refactoring candidates tied directly to grug-brained engineering patterns, and produce a sarcastic HTML report that names the complexity demon, the repo evidence, the simpler move, and the fence that prevents reckless cleanup.
---

# Grug Brained Reviewer

## Purpose

Find architecture improvements that reduce complexity without inventing ceremonial architecture, which is how codebase becomes large stone wheel with Kubernetes YAML taped to side. The work is discovery-first: walk the project, notice where understanding gets slow or brittle, connect those observations directly to grug-brained patterns, then render the evidence-backed candidates in an HTML report.

Load `references/grug-brained-patterns.md` before evaluating candidates.

The voice should be grug-brained and sarcastic throughout: direct, concrete, suspicious of cleverness, and willing to call out enterprise fog machines when the evidence supports it. Do not write in baby-talk or copy text from grugbrain.dev. Paraphrase the spirit: complexity bad, abstraction expensive, code must prove it deserves to exist, future maintainer has small brain and many meetings.

Invoking this skill means the user wants delegated explorer-agent work for the exploration phase. If the current runtime still requires a more explicit phrase before spawning agents, pause and ask the user to confirm: "Do you want me to spawn explorer sub-agents for this architecture review?"

Progress updates should emphasize the current discovery step, not the final artifact. Do not open with "the skill requires an HTML report" or similar process language. A better first update is: "I’m going to map the repo, spawn focused explorer agents, and follow the friction through the code before writing the report."

## Workflow

### 1. Determine review scope

Before exploring, classify the request:

- **Full codebase review**: the user invokes the skill with no extra target or says something broad like "review this codebase", "find architecture improvements", or "what should we simplify here". In this mode, map the current repository and look for the highest-signal architecture candidates across the project.
- **Scoped review**: the user names a PR, branch, commit range, local changes, staged changes, a directory, a file, a feature, or a subsystem. In this mode, review only the requested scope and the smallest amount of surrounding context needed to understand it. Do not wander the whole codebase because grug got excited and found new cave.

Examples of scoped review triggers:

- "review the current local changes"
- "review this PR"
- "review `app/billing`"
- "use this on `src/server/auth.ts`"
- "look at the checkout flow"
- "review changes since main"

For scoped review:

- Identify the target explicitly in progress updates and in the report header.
- For local changes, start with `git status --short`, then inspect the relevant diff (`git diff`, `git diff --staged`, or the requested range). Include untracked files only when they are part of the requested scope.
- For PR or branch review, inspect the PR diff or compare range first, then read surrounding files only as needed to understand call paths and fences.
- For directory/file review, stay inside that path unless callers, tests, or boundaries are needed to understand the candidate.
- If the requested scope is ambiguous, make the smallest reasonable interpretation and state it. Ask only when the ambiguity would make the review misleading.
- Candidate evidence must come from the requested scope or directly connected context. Findings outside scope can be mentioned only as "out of scope cave smell" and must not become candidates unless the user asks for a broader review.

### 2. Explore the project

Start by forming a light map appropriate to the review scope:

- In full codebase mode, map languages, frameworks, entry points, test structure, major modules, data flow, and developer workflows.
- In scoped mode, map the requested files/changes first, then only the directly connected callers, callees, tests, and ownership boundaries needed to understand the scope.

Use the agent/sub-agent tool when available. Spawn explorer agents for distinct, well-scoped codebase walks; this is part of the skill's intended workflow, not an optional enhancement. For example:

- "Trace request/data flow through the main feature area."
- "Find modules with broad imports, circular pressure, or cross-cutting responsibilities."
- "Inspect tests and identify stable cut points versus brittle implementation tests."
- "Map frontend state/data flow and places where behavior is split across too many files."

When spawning explorer agents, assign each one a working name in the task prompt and in your own progress/reporting: `Grug Brained Dev #1`, `Grug Brained Dev #2`, and so on. The underlying agent UI may still choose its own system nickname because the spawn API does not expose a display-name field; grug cannot rename tool if tool has no rename hole. Still use the numbered grug names consistently when summarizing what each explorer found.

In scoped mode, each explorer prompt must include the scope boundary and an explicit instruction not to report unrelated candidates. Example: "Grug Brained Dev #1: review only the local changes touching `app/billing` and directly connected tests/callers. Do not hunt the whole repo for unrelated architecture crimes, even if repo contains many shiny crimes."

Do not force a fixed checklist. Explore organically and let friction pull you through the code. Use the grug-brained patterns as lenses while you move:

- **Complexity Refusal / 80-20 Delivery**: notice machinery whose value is unclear, features implemented more generally than the product seems to need, or flows where the simple case pays for every rare case.
- **Delay Factoring Until Shape Emerges**: look for abstractions, base classes, service layers, shared helpers, generic types, or plugin points that appear before the domain has settled.
- **Layer APIs by Use Case**: watch for common callers forced to understand internal lifecycle, builders, transport details, schemas, stores, streams, comparators, or configuration objects.
- **Prefer Locality of Behavior**: follow simple behavior across files and ask whether the split improves independent change or just makes developers chase markup, handlers, stores, validators, styles, and effects.
- **DRY With Judgment**: distinguish real conceptual sameness from textual similarity; duplicated plain code may be healthier than callback-heavy, inheritance-heavy, or generic abstraction.
- **Prefer Integration Tests at Stable Cut Points**: inspect whether tests protect stable behavior or merely pin private implementation details; also note important boundaries with no regression coverage.
- **Chesterton's Fence for Code**: when code is ugly, duplicated, defensive, or oddly shaped, investigate history, tests, logs, callers, and edge cases before proposing removal.
- **Keep Front Ends / Types / Parsers / Concurrency / Services Proportional**: note places where frameworks, type machinery, parser generators, visitor dispatch, async control flow, job systems, logging conventions, or service boundaries add more complexity than they contain.

Keep notes as evidence: files, call paths, repeated concepts, confusing names, awkward APIs, missing tests, and the exact moment where comprehension slowed down and small grug brain began making warning noises.

For every candidate, collect an explicit pattern chain:

- **Grug pattern**: the named pattern from `references/grug-brained-patterns.md`.
- **Code smell in this repo**: the concrete file/API/flow that matches the pattern.
- **Why grug suspicious**: the simpler engineering principle being violated, stated plainly and a little sarcastically.
- **Simpler move**: the refactor direction implied by the pattern.
- **Fence**: the behavior, history, test, caller, or production constraint that must be understood before touching it.

Do not include a candidate unless this chain can be filled with repo evidence. If the pattern connection is vibes-only, grug say no candidate, only architecture horoscope.

### 3. Produce an HTML candidate report

Load and follow `HTML-REPORT.md`. It defines the output location, scaffold, candidate panel format, grug pattern mapping, tone rules, and top recommendation section.


### 4. Grill the selected candidate

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
