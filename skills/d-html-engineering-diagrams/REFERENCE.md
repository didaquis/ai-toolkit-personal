# HTML Diagram Geometry Reference

## 1. Coordinate system

Use one stage for both nodes and connectors:

```css
.diagram {
  --pad: 32px;
  position: relative;
  min-height: 24rem;
  padding: var(--pad);
}

.connector-layer {
  position: absolute;
  inset: 0;
  pointer-events: none;
  z-index: 0;
}

.node { position: relative; z-index: 1; }
```

Do not position nodes in an inner wrapper and connectors against the outer stage unless the offset is included in every connector calculation.

## 2. Repeated-column formula

For a padded stage of width `W`, padding `P`, and a normalized inner position `p`:

```text
x = P + p × (W - 2P)
  = p × W + P × (1 - 2p)
```

With four columns, centers are `p = .125, .375, .625, .875`. With `P = 32px`, the first center is:

```css
left: calc(12.5% + 24px);
```

The distance between adjacent centers is:

```css
width: calc(25% - 16px);
```

This is why approximate values such as `left: 15%; width: 24%` visibly miss sequence lifelines.

## 3. Connector primitives

### Horizontal

```css
.edge-horizontal {
  position: absolute;
  height: 1px;
  background: var(--stroke);
}

.edge-horizontal::after {
  content: "";
  position: absolute;
  right: 0;
  top: -3px;
  border-left: 6px solid var(--stroke);
  border-top: 3px solid transparent;
  border-bottom: 3px solid transparent;
}
```

Set `left` to the source node's right edge and `right` to the distance from the stage's right edge to the destination node's left edge.

### Vertical

```css
.edge-vertical {
  position: absolute;
  width: 1px;
  background: var(--stroke);
}
```

Set `top` to the source bottom and `height` to `destinationTop - sourceBottom`.

### Diagonal

```css
.edge-diagonal {
  position: absolute;
  height: 1px;
  background: var(--stroke);
  transform-origin: 0 50%;
}
```

Given source anchor `(x1, y1)` and destination anchor `(x2, y2)`:

```js
const dx = x2 - x1;
const dy = y2 - y1;
const width = Math.hypot(dx, dy);
const angle = Math.atan2(dy, dx) * 180 / Math.PI;
```

Apply `left: x1`, `top: y1`, `width`, and `transform: rotate(angle)`.

## 4. Patterns by diagram family

### Architecture and hierarchy

- Size the horizontal branch from the first child center to the last child center.
- Place one vertical stem at every child center.
- Connect secondary stores or queues explicitly; do not leave labels floating below services.

### Pipeline and timeline

- Place the track from the first marker center to the last marker center.
- Keep markers above the track with a contrasting border so the track appears to pass through them cleanly.

### Sequence

- Use a shared grid for actor headers and lifelines.
- Derive every message start and end from adjacent lifeline centers.
- A return message reverses the arrowhead; it does not reverse its coordinate system.

### Entity relationship

- Anchor edges at entity boundaries, not nearby empty space.
- Prefer top/bottom centers for hierarchy and side centers for peer relationships.
- Counter-rotate cardinality labels so they remain readable.

### State machine

- Give states explicit, consistent dimensions.
- Start transitions exactly at one state boundary and end at another.
- Separate bidirectional transitions into parallel lanes so direction remains legible.

### Streaming topology

- Align producer centers, lane centers, and consumer centers.
- Extend each lane until it touches the broker boundary.
- Animate packets from `left: 0` to `left: calc(100% - packetWidth)`.

### Load balancing

- Size the fanout from the first instance center to the last instance center.
- Extend each stem through any flex gap until it touches its instance.

### Telemetry and cache

- Bars must share one baseline.
- Metric dividers must remain inside their grid cells.
- Progress strokes must remain clipped to their corresponding tier and encode the stated value.

## 5. Browser geometry audit

Use the browser after the visual pass. Compare rendered rectangles, not source-code intentions:

```js
const centerX = (rect) => rect.left + rect.width / 2;
const source = document.querySelector(".source").getBoundingClientRect();
const target = document.querySelector(".target").getBoundingClientRect();
const edge = document.querySelector(".edge").getBoundingClientRect();

const error = {
  source: Math.abs(edge.left - source.right),
  target: Math.abs(edge.right - target.left),
};

console.table(error);
```

For repeated diagrams, project all relevant nodes and connectors in one bounded browser evaluation. Report the maximum endpoint error per diagram. A screenshot confirms appearance; rectangle measurements confirm geometry.

## 6. Failure modes

- Approximate percentages that ignore stage padding.
- Connectors positioned against a different ancestor than their nodes.
- Text content determining connector width or height.
- Flex `gap` creating an invisible break between a line and a node.
- A rotated line using its center as the transform origin.
- Animated particles following a shorter path than the static connector.
- Arrowheads floating beyond, or buried inside, destination nodes.
- Responsive transforms scaling nodes but not recomputing connector anchors.
