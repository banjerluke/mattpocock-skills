# HTML Report Format

The architectural review is rendered as a single self-contained HTML file in the OS temp directory. Tailwind and Mermaid both come from CDNs. Mermaid handles graph-shaped diagrams reliably; hand-built divs and inline SVG handle the more editorial visuals (mass diagrams, cross-sections). Mix the two: don't lean on Mermaid for everything, it'll start to look generic.

## Scaffold

Copy [`assets/report.html`](assets/report.html) to the temp path and fill in its three sections. It carries the CDN tags, the token block, the Tailwind colour config, and the Mermaid init already wired together.

## Colour

Colour comes from tokens: `bg-card`, `text-muted`, `border-line`, `fill="var(--bad)"`, `stroke="var(--muted)"`. Both themes live in the template's token block, so a report written in tokens is already correct in dark. Semantics hold across themes for free: there is one `--bad`, so leakage is red in both.

Three of the tokens are semantic rather than chromatic, and the theme decides what they mean:

- `--deep-edge`: invisible against a light card, a visible outline against a dark one, so a deep slab never reads as a hole.
- `--good-bg` / `--warn-bg` / `--bad-bg`: the tinted backing for a badge or callout, pale in light, deep in dark. The matching `--good` / `--warn` / `--bad` is the text or stroke that sits on it.
- `--panel`: an inset panel inside a card. Lighter than the card in light mode, darker in dark.

Use solid colours. Tailwind's opacity modifiers (`bg-card/50`) don't work against `var()` colours.

**Check before you open the file:** `grep -o '#[0-9a-fA-F]\{3,6\}' <path>` returns nothing outside the token block. Every hit is a colour that will be wrong in one of the two themes.

## Header

Repo name, date, and a compact legend: solid box = module, dashed line = seam, red arrow = leakage, thick dark box = deep module. No introduction paragraph. Straight into the candidates.

## Candidate card

The diagrams carry the weight. Prose is sparse, plain, and uses the glossary terms (from the `/codebase-design` skill) without ceremony.

Each candidate is one `<article>`:

- **Title**: short, names the deepening (e.g. "Collapse the Order intake pipeline").
- **Badge row**: recommendation strength (`Strong` = `good`, `Worth exploring` = `warn`, `Speculative` = `muted`), plus a tag for the dependency category (`in-process`, `local-substitutable`, `ports & adapters`, `mock`).
- **Files**: monospaced list, `font-mono text-sm`.
- **Before / After diagram**: the centrepiece. Two columns, side by side. See patterns below.
- **Problem**: one sentence. What hurts.
- **Solution**: one sentence. What changes.
- **Wins**: bullets, ≤6 words each. e.g. "Tests hit one interface", "Pricing logic stops leaking", "Delete 4 shallow wrappers".
- **ADR callout** (if applicable): one line in a `bg-warn-bg text-warn` box.

No paragraphs of explanation. If the diagram needs a paragraph to be understood, redraw the diagram.

## Diagram patterns

Pick the pattern that fits the candidate. Mix them. Don't make every diagram look the same. Variety is part of the point.

### Mermaid graph (the workhorse for dependencies / call flow)

Use a Mermaid `flowchart` or `graph` when the point is "X calls Y calls Z, and look at the mess." Wrap it in a Tailwind-styled card so it doesn't feel parachuted in. Sequence diagrams work well for "before: 6 round-trips; after: 1."

Mermaid's `classDef` and `linkStyle` reject `var()`, so they carry geometry only (`stroke-width`, dash patterns) and the template's stylesheet supplies the colour: mark a leaking node with `class X,Y leak`, and draw a leaking edge dotted (`-.->`).

```html
<div class="rounded-lg border border-line bg-card p-4">
  <pre class="mermaid">
    flowchart LR
      A[OrderHandler] --> B[OrderValidator]
      B --> C[OrderRepo]
      C -.-> D[PricingClient]
      classDef leak stroke-width:2px;
      class C,D leak
  </pre>
</div>
```

### Hand-built boxes-and-arrows (when Mermaid's layout fights you)

Modules as `<div>`s with borders and labels. Arrows as inline SVG `<line>` or `<path>` elements positioned absolutely over a relative container. Reach for this when you want the "after" diagram to feel like one thick-bordered deep module with greyed-out internals, since Mermaid won't render that with the right weight.

### Cross-section (good for layered shallowness)

Stack horizontal bands (`h-12 border-l-4`) to show layers a call passes through. Before: 6 thin layers each doing nothing. After: 1 thick band labelled with the consolidated responsibility.

### Mass diagram (good for "interface as wide as implementation")

Two rectangles per module: one for interface surface area, one for implementation. Before: interface rectangle is nearly as tall as the implementation rectangle (shallow). After: interface rectangle is short, implementation rectangle is tall (deep).

### Call-graph collapse

Before: a tree of function calls rendered as nested boxes. After: the same tree collapsed into one box, with the now-internal calls shown faded inside it.

## Style guidance

- Lean editorial, not corporate-dashboard. Generous whitespace. Serif optional for headings (`font-serif` sits well on the token palette).
- Colour sparingly: `ink` and `muted` carry the page, `bad` marks leakage, `warn` marks caveats.
- Keep diagrams ~320px tall so before/after sits comfortably side by side without scrolling.
- Module labels read as schematic, not as UI: `text-xs uppercase tracking-wider` in HTML, the template's `.lbl` class in SVG (with `fill="var(--muted)"` on the element).
- The template's three script tags are the only scripts. The report is otherwise static: no app code, no interactivity beyond Mermaid's own rendering.

## Top recommendation section

One larger card. Candidate name, one sentence on why, anchor link to its card. That's it.

## Tone

Plain English, concise, but the architectural nouns and verbs come straight from the `/codebase-design` skill. Concision is not an excuse to drift.

**Use exactly:** module, interface, implementation, depth, deep, shallow, seam, adapter, leverage, locality.

**Never substitute:** component, service, unit (for module) · API, signature (for interface) · boundary (for seam) · layer, wrapper (for module, when you mean module).

**Phrasings that fit the style:**

- "Order intake module is shallow: interface nearly matches the implementation."
- "Pricing leaks across the seam."
- "Deepen: one interface, one place to test."
- "Two adapters justify the seam: HTTP in prod, in-memory in tests."

**Wins bullets** name the gain in glossary terms: *"locality: bugs concentrate in one module"*, *"leverage: one interface, N call sites"*, *"interface shrinks; implementation absorbs the wrappers"*. Don't write *"easier to maintain"* or *"cleaner code"*, because those terms aren't in the glossary and don't earn their place.

No hedging, no throat-clearing, no "it's worth noting that…". If a sentence could be a bullet, make it a bullet. If a bullet could be cut, cut it. If a term isn't in the `/codebase-design` glossary, reach for one that is before inventing a new one.
