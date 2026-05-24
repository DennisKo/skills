# Grug-Brained Engineering Patterns

## Core Thesis

Complexity is the primary long-term enemy of software systems. It enters through well-intended features, premature abstractions, process theater, overdesigned APIs, excessive tooling, and fear-driven silence. Good engineering is mostly the disciplined practice of keeping complexity visible, delayed, contained, tested, debugged, and removable.

## Patterns

### 1. Complexity Refusal

**Pattern:** Use "no" as the first defense against unnecessary system growth.

Apply when a feature, abstraction, framework, or process adds more moving parts than value. The best complexity is the complexity never admitted into the codebase.

**Do:**

- Say no to features that do not justify their maintenance burden.
- Say no to abstractions before the domain shape is understood.
- Treat every new layer as a long-term liability unless proven otherwise.
- Prefer fewer concepts that developers can hold in their heads.

**Don't:**

- Say yes just because it is politically easier.
- Trade maintainability for short-term approval without naming the cost.
- Let "career incentives" silently override engineering judgment.

### 2. Pragmatic Compromise / 80-20 Delivery

**Pattern:** When refusal is impossible, build the smallest useful version that captures most value with far less complexity.

**Do:**

- Implement the 80 percent outcome with 20 percent of the machinery.
- Deliver the business value without every requested bell and whistle.
- Keep the solution simple enough to replace later.
- Use judgment when stakeholders over-specify details they will not remember or depend on.

**Don't:**

- Build the maximal version just because it was requested.
- Ask permission for every simplification if it creates needless process drag.
- Confuse "complete spec compliance" with "best engineering outcome."

### 3. Delay Factoring Until Shape Emerges

**Pattern:** Let the codebase reveal natural boundaries before formalizing abstractions.

Early in a project, the domain is fluid. Premature factoring guesses at boundaries before the system has shown where they belong.

**Do:**

- Start concrete.
- Watch for repeated pressure, stable concepts, and natural cut points.
- Refactor gradually once boundaries become obvious.
- Prefer boundaries with narrow interfaces and hidden internal complexity.
- Treat a good boundary as a place where complexity is contained behind a small API.

**Don't:**

- Create architecture before working behavior exists.
- Let abstract modeling replace contact with real code.
- Assume early abstractions will survive unchanged.
- Split systems just because a diagram looks elegant.

### 4. Prototype to Ground Abstraction

**Pattern:** Force abstract design conversations into working code quickly.

A demo or prototype reveals real constraints faster than architecture debate.

**Do:**

- Ask for working behavior early.
- Use prototypes to discover domain shape.
- Let real code challenge theoretical designs.
- Use diagrams as disposable thinking tools, not source-of-truth architecture.

**Don't:**

- Let architecture committees design far ahead of implementation.
- Preserve UML or design artifacts after they stop matching reality.
- Let high-abstraction contributors avoid operational consequences.

### 5. Test After Understanding, Then Be Disciplined

**Pattern:** Testing is essential, but the best timing and level depend on how stable the design is.

Tests written before understanding the domain often encode guesses. Tests skipped after prototyping leave the system fragile.

**Do:**

- Write exploratory/unit tests early if they help you think.
- Add serious tests once the domain and interfaces stabilize.
- Use regression tests before fixing known bugs.
- Test along the way as understanding improves.
- Treat tests as future-debugging infrastructure.

**Don't:**

- Use "works on my machine" as evidence.
- Let prototype code graduate without tests.
- Treat test-first as a universal law.
- Chase 100 percent unit-test coverage if it hardens volatile implementation details.

### 6. Prefer Integration Tests at Stable Cut Points

**Pattern:** Aim most durable testing effort at APIs and boundaries that are stable enough to matter.

Integration tests often give the best value: broad enough to catch real behavior, narrow enough to debug.

**Do:**

- Test through stable subsystem APIs.
- Keep tests close enough to use a debugger effectively.
- Focus on behavior that should survive refactoring.
- Maintain a small, reliable end-to-end suite for core user flows and critical edge cases.

**Don't:**

- Over-invest in brittle unit tests tied to implementation.
- Build huge end-to-end suites that fail often and get ignored.
- Mock everything.
- Use mocks except where necessary, and then mock coarse-grained boundaries rather than internals.

### 7. Process Is Secondary to People, Tools, and Prototypes

**Pattern:** Agile or any process can help, but it is not a silver bullet.

**Do:**

- Use process lightly to coordinate work.
- Prioritize prototypes, good tools, and strong engineers.
- Be suspicious when process failure is always blamed on incorrect ritual.

**Don't:**

- Let methodology become a substitute for judgment.
- Spend heavily on process theater while ignoring technical feedback.
- Believe any process removes the need for hard engineering choices.

### 8. Small, Shore-Close Refactoring

**Pattern:** Refactor in small steps while keeping the system working.

Large refactors fail more often because they move too far from a known-good state.

**Do:**

- Keep refactors incremental.
- Finish one step before starting another.
- Keep behavior working throughout.
- Use end-to-end tests as guardrails.
- Refactor later when the code has firmed up.

**Don't:**

- Launch massive rewrites under the label "refactor."
- Introduce abstraction as the default refactoring move.
- Assume a more abstract system is automatically a better system.
- Repeat failures like enterprise over-frameworking or modularity systems that add more complexity than they contain.

### 9. Chesterton's Fence for Code

**Pattern:** Understand why existing code exists before removing or "cleaning" it.

Ugly code may encode real constraints, history, operational fixes, or hidden edge cases.

**Do:**

- Ask what problem the code solves.
- Use tests, logs, commit history, and production behavior to infer intent.
- Respect working code even when it is inelegant.
- Improve systems after understanding them.

**Don't:**

- Delete code because it looks ugly.
- Assume aesthetic discomfort means the code is wrong.
- Charge into legacy systems with cleanup energy but no context.

### 10. Be Very Careful With Microservices

**Pattern:** Factoring a system is already hard; adding network boundaries makes it harder.

**Do:**

- Use service boundaries only when organizational, scaling, deployment, or ownership needs justify them.
- Remember that network calls add latency, failure modes, observability needs, and data consistency problems.

**Don't:**

- Treat microservices as ordinary code factoring.
- Split services before you know the domain boundaries.
- Add distributed systems problems to avoid local modularity problems.

### 11. Invest Deeply in Tools

**Pattern:** Tools extend limited human memory and reasoning.

**Do:**

- Spend real time learning local tools when joining a codebase.
- Learn the debugger deeply: conditional breakpoints, expression evaluation, stack navigation, watches.
- Use IDE completion to reduce memory burden.
- Keep improving tooling continuously.
- Treat logging, debugging, and editor fluency as core engineering skill.

**Don't:**

- Rely on memory for APIs.
- Treat debugger use as optional or junior.
- Ignore local developer workflows because documentation is weak.

### 12. Type Systems as Developer Assistance

**Pattern:** The highest everyday value of types is discoverability and tooling, not only formal correctness.

**Do:**

- Prefer types that make valid operations obvious.
- Optimize for autocomplete, navigation, refactoring, and readable APIs.
- Use types to reduce what developers must remember.

**Don't:**

- Turn the codebase into a type-theory exercise.
- Overuse generics outside high-value cases like containers and reusable data structures.
- Make ordinary business tasks require abstract type machinery.

### 13. Reduce Expression Complexity

**Pattern:** Prefer debuggable, named intermediate expressions over dense conditionals.

```ts
if (contact) {
  const isInactive = !contact.isActive();
  const isFamilyOrFriend = contact.inGroup(FAMILY) || contact.inGroup(FRIENDS);

  if (isInactive && isFamilyOrFriend) {
    notify(contact);
  }
}
```

**Do:**

- Name meaningful sub-expressions.
- Make conditionals easy to inspect in a debugger.
- Optimize for comprehension over line count.

**Don't:**

- Compress logic into clever one-liners.
- Treat fewer lines as automatically simpler.
- Hide important business concepts inside boolean soup.

### 14. DRY With Judgment

**Pattern:** Duplication is sometimes cheaper than the abstraction required to remove it.

**Do:**

- Remove duplication when the shared concept is real and stable.
- Allow simple, obvious repetition when variants are small.
- Prefer duplicated straightforward code over callback-heavy or inheritance-heavy generalization.

**Don't:**

- Worship DRY.
- Create complex abstractions to eliminate harmless repetition.
- Confuse textual similarity with conceptual sameness.

### 15. Prefer Locality of Behavior Over Excessive Separation

**Pattern:** Put behavior near the thing it affects when that improves comprehension.

**Do:**

- Keep related markup, behavior, and styling close when it helps developers understand a feature.
- Optimize for "look here, understand this thing."
- Separate concerns when they truly change independently.

**Don't:**

- Split code across many files purely because categories differ.
- Make developers chase a button's behavior across markup, CSS, handlers, stores, schemas, and generated layers.
- Treat separation of concerns as universally superior to locality.

### 16. Use Closures Sparingly

**Pattern:** Closures are powerful for operations over collections and local behavior, but overuse creates hidden control flow.

**Do:**

- Use closures where they simplify collection operations or scoped behavior.
- Keep closure chains readable.

**Don't:**

- Build callback-heavy control flow.
- Let asynchronous closure nesting become unreadable.
- Use closures as a substitute for clear named functions or boundaries.

### 17. Logging Is Production Debugging Infrastructure

**Pattern:** Logging is not decoration; it is how distributed systems become observable.

**Do:**

- Log major logical branches.
- Include request IDs across machine boundaries.
- Support dynamic log levels.
- Support per-user or targeted debug logging where possible.
- Invest in getting logging infrastructure right.

**Don't:**

- Treat logging as too expensive by default.
- Leave production debugging dependent on guessing.
- Accept logging libraries or conventions nobody understands.

### 18. Fear Concurrency

**Pattern:** Prefer simple concurrency models.

**Do:**

- Use stateless request handlers where possible.
- Use independent job queues with simple APIs.
- Use optimistic concurrency for common web cases.
- Use proven concurrent data structures carefully.
- Reach for thread-local state only in narrow framework-level cases.

**Don't:**

- Share mutable state casually.
- Build interdependent job systems without strong reason.
- Assume language/library support makes concurrency easy.

### 19. Profile Before Optimizing

**Pattern:** Optimize only after real-world measurement identifies the bottleneck.

**Do:**

- Require concrete performance profiles.
- Expect bottlenecks to surprise you.
- Consider network, I/O, serialization, database access, and coordination costs, not only CPU.
- Minimize network calls where possible.

**Don't:**

- Optimize from intuition alone.
- Focus only on Big-O or nested loops.
- Add complexity to fix a theoretical performance issue.

### 20. Layer APIs by Use Case

**Pattern:** Good APIs make simple things simple and complex things possible.

**Do:**

- Design from the caller's perspective.
- Provide a simple API for common cases.
- Add lower-level or configurable APIs for rare complex cases.
- Put object-oriented behavior on the object users already have.
- Return the common useful type when possible.

**Don't:**

- Force users through implementation concepts for common operations.
- Make callers learn streams, collectors, comparators, builders, or lifecycle machinery for basic tasks.
- Design APIs only around internal domain purity.

### 21. Prefer Recursive Descent for Understandable Parsers

**Pattern:** Handwritten recursive descent parsers are often more understandable and debuggable than generated parser machinery.

**Do:**

- Use recursive descent when grammar and maintenance needs fit.
- Keep parser structure close to grammar structure.
- Favor debuggability in production parsers.

**Don't:**

- Assume parser generators are always superior.
- Accept generated parser code that nobody can debug.
- Treat parsing as inaccessible magic.

### 22. Avoid Visitor Pattern Unless Strongly Justified

**Pattern:** Visitor often adds indirection and ceremony that outweigh its benefit.

**Do:**

- Prefer simpler dispatch or direct behavior placement where possible.

**Don't:**

- Reach for Visitor as a default extensibility pattern.
- Use it when it makes ordinary changes harder to trace.

### 23. Keep Front Ends Simple Unless the App Demands Complexity

**Pattern:** Avoid creating separate front-end and back-end complexity centers for simple products.

**Do:**

- Use simple HTML and server-rendered flows when enough.
- Add JavaScript proportionally to interaction needs.
- Use heavier SPA architectures when the product truly requires rich client state.

**Don't:**

- Use SPA plus GraphQL plus JSON API plus separate build systems for a form or brochure site.
- Adopt front-end frameworks only because large companies popularized them.
- Forget that JavaScript-heavy systems are fertile ground for complexity.

## Dense Application Checklist

- Say no to complexity by default.
- If you must say yes, build the 80-20 version.
- Prototype before architecting deeply.
- Delay factoring until stable cut points appear.
- Prefer narrow interfaces that trap complexity internally.
- Test after understanding, but do not skip tests.
- Put most durable tests at integration boundaries.
- Keep end-to-end tests small, critical, and always green.
- Mock rarely and coarsely.
- Refactor in small, reversible steps.
- Understand the fence before removing it.
- Avoid microservices until the boundary is worth a network.
- Master debugger, IDE, logs, and local workflows.
- Use types for discoverability and safe change.
- Avoid type cleverness and generic overreach.
- Split complex expressions into named values.
- Let harmless duplication survive when abstraction would be worse.
- Prefer locality of behavior when it improves comprehension.
- Use closures lightly.
- Log enough to debug production.
- Keep concurrency models boring.
- Profile before optimizing.
- Design APIs for common callers first.
- Prefer understandable parsers.
- Be suspicious of Visitor.
- Keep front ends proportional to the product.
