# HTML Report Template

The architectural review is rendered as a single self-contained HTML file in the OS temp directory. Tailwind and Mermaid both come from CDNs. Mermaid handles graph-shaped diagrams reliably; hand-built divs and inline SVG handle the more editorial visuals such as mass diagrams, cross-sections, and deep/shallow module comparisons. Mix the two. Do not lean on Mermaid for everything or the report will look generic.

## Required Scaffold

Use this exact CDN setup:

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Grug Architecture Review - {{repo name}}</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script type="module">
      import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
      mermaid.initialize({ startOnLoad: true, theme: "neutral", securityLevel: "loose" });
      window.mermaid = mermaid;
    </script>
    <script>
      function showCandidate(id) {
        document.querySelectorAll("[data-candidate-panel]").forEach((panel) => {
          panel.hidden = panel.id !== id;
        });
        document.querySelectorAll("[data-candidate-tab]").forEach((tab) => {
          const active = tab.getAttribute("aria-controls") === id;
          tab.setAttribute("aria-selected", active ? "true" : "false");
          tab.dataset.active = active ? "true" : "false";
        });
        window.location.hash = id;

        const panel = document.getElementById(id);
        if (window.mermaid && panel) {
          window.mermaid.run({ nodes: panel.querySelectorAll(".mermaid:not([data-processed])") });
        }
      }

      window.addEventListener("DOMContentLoaded", () => {
        const initial = window.location.hash.slice(1);
        const firstPanel = document.querySelector("[data-candidate-panel]");
        const target = initial && document.getElementById(initial) ? initial : firstPanel?.id;
        if (target) showCandidate(target);
      });
    </script>
    <style>
      .diagram-label { letter-spacing: 0.08em; }
      .shallow-line { stroke: #dc2626; stroke-width: 2; stroke-dasharray: 5 5; }
      .deep-box { fill: #0f172a; stroke: #0f172a; }
      .faded { opacity: 0.46; }
      .mermaid svg { width: 100%; max-width: none; min-height: 360px; }
    </style>
  </head>
  <body class="bg-stone-50 text-slate-900 antialiased">
    <main class="mx-auto max-w-6xl px-6 py-10">
      <!-- report content -->
    </main>
  </body>
</html>
```

The file should work by opening it directly in a browser. Do not add a build step, app framework, bundler, or external UI library.

## Page Shape

Use this structure:

- **Header**: repo name, review date, short one-line purpose, and a compact legend for shallow module, deep module, leakage, seam, and fence.
- **Candidate review workspace**: a selector plus one visible candidate panel at a time. Do not render all candidate cards as one long scrolling list.
- **Top recommendation**: one larger final card naming the first candidate to tackle and why.

Keep the page editorial and scannable, not dashboard-like. Use generous whitespace, restrained color, and concrete codebase names.

## Candidate Review Workspace

Prefer a candidate-by-candidate review UI over a long list. Tabs alone are acceptable for two or three candidates with short titles, but the default pattern should be a review workspace:

- **Candidate selector**: a left rail on desktop and horizontal scroll selector on mobile. Each selector item shows candidate number, short title, recommendation badge, and one terse friction phrase.
- **Active candidate panel**: only one candidate is visible at a time. The user should be able to focus on the evidence, diagram, and cautions without seeing every other candidate below it.
- **Previous / Next controls**: add text buttons at the bottom of each panel for sequential review. These buttons call `showCandidate("candidate-id")`.
- **Stable anchors**: each candidate panel has a durable id such as `candidate-state-locality` so the hash URL opens that candidate.
- **Summary strip**: above the workspace, show a compact one-line summary of how many candidates are `Strong`, `Worth exploring`, and `Speculative`.

Use this structure:

```html
<section aria-labelledby="candidates-heading" class="mt-10">
  <div class="mb-5 flex flex-wrap items-center justify-between gap-3">
    <h2 id="candidates-heading" class="text-2xl font-semibold">Candidates</h2>
    <p class="text-sm text-slate-600">2 Strong · 1 Worth exploring · 1 Speculative</p>
  </div>

  <div class="grid gap-6 lg:grid-cols-[18rem_1fr]">
    <div
      role="tablist"
      aria-label="Architecture candidates"
      class="flex gap-3 overflow-x-auto pb-2 lg:block lg:space-y-3 lg:overflow-visible lg:pb-0"
    >
      <button
        type="button"
        role="tab"
        aria-controls="candidate-state-locality"
        aria-selected="true"
        data-candidate-tab
        data-active="true"
        onclick="showCandidate('candidate-state-locality')"
        class="min-w-64 rounded-lg border border-slate-200 bg-white p-4 text-left shadow-sm data-[active=true]:border-slate-900 data-[active=true]:ring-2 data-[active=true]:ring-slate-900/10 lg:min-w-0"
      >
        <span class="text-xs font-semibold uppercase text-slate-500">Candidate 1</span>
        <span class="mt-1 block text-sm font-semibold text-slate-950">Pull state behavior together</span>
        <span class="mt-2 inline-flex rounded-full bg-emerald-100 px-2 py-1 text-xs font-medium text-emerald-800">Strong</span>
        <span class="mt-2 block text-xs text-slate-600">Handlers, store updates, and validation are scattered.</span>
      </button>
    </div>

    <div>
      <article id="candidate-state-locality" data-candidate-panel>
        <!-- candidate card content -->
      </article>
    </div>
  </div>
</section>
```

If JavaScript is disabled, all panels may remain visible by default, but normal generated reports should initialize to one visible panel. Keep the inline script small and dependency-free. Do not add a framework, router, bundler, or external UI library.

## Candidate Card

Each candidate is an `<article>` panel with this content:

- **Title**: action-oriented, specific to the architecture move.
- **Recommendation badge**: exactly one of `Strong`, `Worth exploring`, or `Speculative`.
- **Files involved**: concrete files/modules and their role in the candidate.
- **Observed friction**: why the current shape slows understanding, change, testing, debugging, or removal.
- **Proposed change**: plain-English description of the deeper/simpler shape.
- **Why it helps**: benefits framed as reduced concepts, better locality, narrower interface, clearer tests, easier debugging, or smaller blast radius.
- **Before / After diagram**: side-by-side visual showing the current shallow/scattered shape and the proposed deeper/simpler shape. These diagrams are the centerpiece of the card, not thumbnails.
- **Grug patterns used**: cite pattern names from `references/grug-brained-patterns.md`.
- **Cautions / fences**: what must be understood or protected before changing the code.

The diagram should carry real explanatory weight. If the prose has to explain the whole diagram, redraw the diagram. If the diagram renders as tiny labels connected by thin gray or black lines, it has failed; replace it with larger hand-built boxes, inline SVG, or a simpler Mermaid graph.

## Diagram Size and Legibility

Before/after diagrams must be large enough to inspect without zooming:

- Use a full-width diagram region inside each candidate card.
- On desktop, each before/after panel should be at least `min-h-[360px]`; `min-h-[420px]` is better for dense flows.
- Use `grid grid-cols-1 lg:grid-cols-2 gap-6` so panels stack on small screens and sit side by side only when there is enough width.
- Diagram labels must be readable: use at least `text-sm` in HTML diagrams and at least 14px text in SVG.
- Keep node counts low. If a Mermaid graph needs many small nodes, split it into two diagrams or use a custom visual.
- Avoid Mermaid sequence diagrams for candidate before/after panels unless there are only a few actors and the text remains large. Mermaid sequence diagrams often create black/gray lifelines and arrows that look like visual noise at report-card scale.
- Do not shrink diagrams to make them fit. Simplify the diagram instead.

## Diagram Patterns

Choose the diagram type that fits the candidate. Mix diagram types across cards.

### Mermaid Dependency or Flow Graph

Use Mermaid for call graphs, dependency graphs, and state transitions when the labels stay readable. Prefer `flowchart` over `sequenceDiagram` for before/after cards. Use sequence diagrams only for a small number of meaningful interactions.

```html
<div class="min-h-[380px] rounded-lg border border-slate-200 bg-white p-6">
  <pre class="mermaid">
flowchart LR
  A[Common caller] --> B[Shallow wrapper]
  B --> C[Internal machinery]
  A -.leaks detail.-> C
  classDef leak stroke:#dc2626,stroke-width:2px;
  class A,C leak;
  </pre>
</div>
```

### Hand-Built Deep/Shallow Module

Use HTML/CSS or inline SVG when comparing interface size to implementation depth. Before: wide public surface with thin internals. After: small interface with more internal complexity hidden behind it.

### Cross-Section

Use stacked horizontal bands to show too many thin layers. Before: many shallow bands. After: one or two thicker bands with clearer ownership.

### Locality Map

Use clustered boxes to show behavior scattered across files. Before: related behavior spread across distant modules. After: behavior pulled near the thing it affects, with external seams still explicit.

### Fence Callout

Use an amber-tinted callout when a candidate touches code that may encode history, production constraints, hidden edge cases, or compatibility behavior. Name the evidence needed before removal.

## Styling Rules

- Use Tailwind classes for layout, typography, spacing, cards, and badges.
- Use inline SVG or small CSS diagrams for custom visuals.
- Use Mermaid only where graph layout is the right tool.
- Make diagrams large: before/after panels are primary content, not supporting thumbnails.
- Keep before/after diagrams side-by-side on wide desktop and stacked on narrow screens.
- Badge colors: `Strong` emerald, `Worth exploring` amber, `Speculative` slate.
- Use red only for leakage, excessive coupling, or risk.
- Avoid generic labels such as "Service A" or "Module B"; use real file, module, or concept names from the codebase.

## Top Recommendation

End with a section titled `Top Recommendation`.

Include:

- Candidate title.
- Recommendation strength.
- One paragraph explaining why this should go first.
- The smallest reversible first step.
- Main fence or risk to verify before coding.

Prefer candidates with strong evidence, high simplification, stable boundaries, and a small shore-close first move.
