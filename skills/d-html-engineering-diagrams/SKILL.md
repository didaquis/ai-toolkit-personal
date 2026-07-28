---
name: d-html-engineering-diagrams
description: Builds polished technical diagrams with semantic HTML and CSS, precise connector geometry, meaningful motion, and browser-based visual verification. Use when creating or reviewing architecture, sequence, state, pipeline, ER, topology, telemetry, infrastructure, or other diagrams rendered in web pages.
---

# HTML Engineering Diagrams

## Quick start

1. Define what every node, line, arrow, color, and animation means.
2. Give the diagram one positioned stage and one shared coordinate system.
3. Render nodes first; render connectors as a separate layer behind them.
4. Derive connector endpoints from node edges or centers. Never stop at “looks close.”
5. Apply one visual system across diagrams: spacing, type, stroke, glow, status colors, and motion.
6. Inspect every diagram at full desktop scale and measure endpoint error in the browser.

Every diagram file is a full HTML document, never a bare fragment. Always start with this skeleton, with `<meta charset="UTF-8">` as the first element inside `<head>` — diagram labels routinely use non-ASCII characters (`·`, `→`, `⇄`, `—`), and without an explicit charset declared before any other content, browsers fall back to a legacy encoding and corrupt every one of them into mojibake:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Request sequence</title>
<style>
  /* diagram styles */
</style>
</head>
<body>
  <div class="diagram" aria-label="Request sequence from client to worker">
    <div class="node client">Client</div>
    <div class="node worker">Worker</div>
    <div class="connector request"><span>enqueue()</span></div>
  </div>
</body>
</html>
```

## Build workflow

### 1. Model before styling

- List nodes, relationships, direction, states, and important values.
- Choose the diagram family before choosing layout: flow, sequence, hierarchy, state, topology, relationship, timeline, or metric.
- Remove any line that cannot be explained in one sentence.

### 2. Establish geometry

- Make the stage `position: relative`; position all nodes and connectors against it.
- Keep nodes and connectors inside the same padded inner rectangle.
- Use CSS Grid or Flexbox to establish repeated node centers.
- Share dimensions through custom properties instead of repeating magic numbers.
- For diagonal edges, calculate length with `hypot(dx, dy)` and angle with `atan2(dy, dx)`; rotate from `transform-origin: 0 50%`.

### 3. Draw connectors

- Start and end horizontal lines at node side edges.
- Start and end vertical lines at node top or bottom edges.
- Attach diagonal lines to explicit anchor points, normally edge centers.
- Put arrowheads at the destination and labels near the middle without changing line geometry.
- Use solid lines for calls or ownership, dashed lines for returns or optional flow, and glow only for active signals.

### 4. Add visual language and motion

- Keep strokes quieter than nodes; emphasize only the active path.
- Use a small semantic palette consistently across every diagram.
- Animate direction, throughput, state, or progress—not decoration alone.
- Animate particles along the actual connector path and preserve a static line beneath them.
- Support `prefers-reduced-motion`.

### 5. Verify

- Inspect each diagram individually at a normal desktop viewport.
- Check every connector in both directions: source attachment and destination attachment.
- Measure `getBoundingClientRect()` values; target 0 px error for orthogonal lines and less than 0.5 px for rotated lines.
- Confirm arrowheads point toward the destination, labels do not interrupt strokes, and animation follows the same path.
- Check for clipping, unintended gaps, overlaps, horizontal overflow, and browser-console errors.
- Confirm the file starts with `<!DOCTYPE html>` and `<meta charset="UTF-8">` is the first element inside `<head>`, so special characters (`·`, `→`, `⇄`, `—`) render correctly instead of as mojibake.
- Run build, lint, and relevant tests after visual verification.

## Reference

For reusable geometry formulas, connector primitives, diagram-specific patterns, and a browser audit snippet, read [REFERENCE.md](REFERENCE.md).
