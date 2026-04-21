---
name: clearshot
description: "Structured screenshot analysis for UI implementation and critique. Analyzes every UI screenshot with a 5x5 spatial grid, full element inventory, and design system extraction — facts and taste together, every time. Escalates to full implementation blueprint when building. Trigger on any digital interface image file (png, jpg, gif, webp — websites, apps, dashboards, mockups, wireframes) or commands like 'analyse this screenshot,' 'rebuild this,' 'match this design,' 'clone this.' Skip for non-UI images (photos, memes, charts) unless the user explicitly wants to build a UI from them. Does NOT trigger on HTML source code, CSS, SVGs, or any code pasted as text."
user-invocable: true
triggers:
  - "analyse this screenshot"
  - "rebuild this"
  - "match this design"
  - "clone this"
  - "implement this UI"
  - "what's wrong with this design"
---

## Preamble

Run the preamble bash block before any analysis. It locates the skill directory, reads config, checks for updates, and initializes session tracking. See @references/preamble.md for the full script and output interpretation.

## Gate Check

Not every image needs this skill. Ask two questions before doing anything:

1. Is this image a digital interface? (Websites, apps, dashboards, mockups, wireframes, Figma exports, CLI with UI context, browser DevTools with a visible page all count. Photos, memes, standalone charts, slides, documents, handwritten notes do not.)
2. Is the conversation about building, debugging, designing, or evaluating UI?

**Three outcomes:**

- Neither is true: exit the skill entirely. Respond normally. Don't mention this framework.
- Image is not a UI, but the conversation IS about building UI (e.g. "build me a page that feels like this photo"): the image is inspiration, not a spec. Describe what it communicates — mood, texture, weight — and move on. No structured analysis.
- Image IS a UI and the conversation is about building/evaluating: proceed with the analysis levels below.

## Analysis Levels

Every analysis combines facts and taste. There is no separate "analytical mode" or "qualitative mode" — every observation is grounded in specifics (hex values, pixel measurements) AND includes how it feels (hierarchy, weight, cohesion). This mirrors how a senior designer thinks: feel first, then investigate why, always both.

### Level 1: Map (always runs)

Divide the screenshot into a **5x5 grid**. For each occupied region: what section lives there (nav, hero, sidebar, content, footer, modal, drawer, empty space), its approximate size relative to viewport, and how it relates to neighbors.

For every visible element, capture: type (button, input, card, image, icon, text, link, toggle, dropdown, tab, badge, avatar, table, chart, etc.), label/content (exact visible text), position (grid region + relative placement), state (default, hover, active, disabled, selected, error, loading, focused), size (pixel estimate), background color (hex), text color (hex), border (visible/none + radius in px), shadow (none/sm/md/lg), icon if present. Group by section.

Also note: where the eye goes first. Whether the layout breathes or feels cramped. Whether the hierarchy is clear or competing. What feels intentional vs accidental.

### Level 2: System (always runs)

Extract the design system behind what's visible:

**Colors:** page bg, card/surface bg, primary action, secondary, text primary, text secondary/muted, border/divider, accent, destructive, success. All hex values. Note whether the palette feels cohesive or patchwork.

**Typography:** heading style (size in px, weight, case), body text (size, weight, line-height), caption/small text, font family if identifiable. Note whether the type scale steps consistently or jumps randomly.

**Spacing and shape:** spacing pattern (tight 4-8px / comfortable 12-16px / spacious 24-32px+), border radius pattern (sharp 0-2px / subtle 4-6px / rounded 8-12px / pill), overall density (compact / comfortable / spacious). Note whether spacing is consistent across sections.

### Level 3: Blueprint (escalates when building)

Runs when the user needs to implement, rebuild, or clone the UI from the screenshot. Escalate to Level 3 when the conversation involves writing code from this screenshot.

**Layout architecture:** page layout pattern (single column, sidebar+content, dashboard grid, centered container, full-bleed), content layout per section (flex row, flex column, CSS grid with column count, stack), container width (max-width constrained vs full-width), responsive context (mobile <640px / tablet 640-1024px / desktop >1024px), scroll clues (content cut off, sticky header, fixed bottom bar), z-index layers (overlays, modals, dropdowns, toasts).

**Interaction map:** primary CTA (the single most important action), secondary actions, navigation pattern (top nav, side nav, tabs, breadcrumbs, bottom bar), form elements and grouping, data display patterns (tables, card grids, lists), visible states (loading, empty, error, success). Note where a user would hesitate or feel friction, and what feels polished.

For a worked example showing completed Level 1+2 output, see @references/analysis-example.md.

## Output

Match the output to the context. Don't force headers and sections when a paragraph will do.

- **Critique/feedback:** lead with what's wrong or needs attention. Ground each observation in specifics (the exact hex, spacing, or element) and how it affects the experience. Don't catalog everything — focus on what matters.
- **Implementation spec (Level 3):** structured output with section headers — layout map, elements by section, design tokens, layout architecture, interaction map. This is the build document.
- **Comparison (two screenshots):** what changed, what improved, what regressed, what still needs work.

## Core Principles

**Be specific.** "A dashboard with some cards" is never acceptable. "3-column grid, ~280px cards, #F9FAFB bg, 8px radius, subtle shadow — the cards feel weightless, almost floating" is. Every observation needs both the measurement and the judgment.

**Hex over color names, pixels over vague sizes.** Say #3B82F6 not "blue." Say ~16px not "some." If uncertain, give your best estimate and note it.

**Group by section, not by element type.** The nav's elements belong together. Don't lump all buttons across the page into one list.

**Call out the non-obvious.** Custom illustrations, unusual component patterns, implied animations, dynamic vs static data. These are the things that break implementations.

**Match the user's pace.** Rapid iteration = concise output. Detailed clone request = exhaustive. But the analysis depth (Levels 1+2) is always the same — what changes is how much you output, not how much you see.

## Self-Rating, Feedback, and Epilogue

After every analysis, perform an internal self-rating (0-10) and run the telemetry epilogue. See @references/feedback-and-telemetry.md for the full self-rating criteria, feedback trigger rules, field report format, and epilogue bash script.
