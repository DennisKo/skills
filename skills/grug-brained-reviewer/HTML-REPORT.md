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

The file should work by opening it directly in a browser. Do not add a build step, app framework, bundler, or extra runtime script.

## Page Shape

Use this structure:

- **Header**: repo name, review date, short one-line purpose, and a compact legend for shallow module, deep module, leakage, seam, and fence.
- **Candidate cards**: one card per architecture candidate.
- **Top recommendation**: one larger final card naming the first candidate to tackle and why.

Keep the page editorial and scannable, not dashboard-like. Use generous whitespace, restrained color, and concrete codebase names.

## Candidate Card

Each candidate is an `<article>` card with this content:

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
