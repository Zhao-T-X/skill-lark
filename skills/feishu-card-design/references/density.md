# Density and prominence

The first mobile view carries one decision. Design to that budget first; visual polish serves it and never competes with it.

## The first-screen budget

At most seven information units, one decision:

| Unit | Count |
|---|---:|
| Object + conclusion (title) | 1 |
| Current value + unit | 1 |
| Threshold (with ratio or deviation when supplied) | 1 |
| Trend for the main issue | 1 |
| Three comparable core metrics | 3 |

Everything else is evidence. It goes after the three-metric row, into a `collapsible_panel`, or onto another card. There is no fourth destination.

A dense input is not a reason to exceed the budget. It is a reason to count omissions (`另有 3 项异常`) and keep one abnormal example visible.

## Rank prominence once

Only two layers may be loud: object + conclusion, then current value + threshold. The trend is half-loud — color or weight, choose one. The three-metric row stays quiet; if it competes, the card has two protagonists and the reader has none.

- Use one emphasis device per element where possible, two at most. Order them by cost: position → whitespace isolation → size/weight → state color → icon/emoji.
- Main value about 2× body size; body 14px; never below 12px. Three weight levels is the practical maximum.
- Keep 12–16px of breathing room around the main value. Whitespace is the cheapest emphasis and the least noisy.
- The state word (`超阈`, `失败`, `不可达`) is mandatory, so prominence survives grayscale and screenshots.

## Say each fact once

Duplication is the main source of density because it looks like new information.

- A metric appears in the three-column row or in a vertical row, not both.
- Put repeated units and thresholds at first use (`消息速率 · 条/秒`) instead of in every cell.
- State the time window once, in the footer or the first line; do not repeat it per block.

## Demote what cannot change the decision

Version, role, uptime, node count, address, last successful save, and earlier values rarely change whether the reader acts now. Move them to the subtitle, the footer, or a panel. The test is single: if deleting the field leaves the decision unchanged, it does not belong on the first screen.

## Match structure to metric shape

| Shape | Placement |
|---|---|
| Ratio or saturation (usage, hit rate, memory watermark) | Value with its limit (`812 / 1,000`), so distance is readable |
| Cumulative counter | Total plus window increment, never phrased as an event |
| State (persistence, replication, reachability) | Vertical row; it is not comparable across a three-column layout |
| Distribution (P99, slow queries) | Value plus window and comparison base; no comparison base, no trend claim |
| Multi-object summary | Sort by risk; first screen shows the worst object only |

## Keep rhythm, not separators

Group with space before lines. Inside a group 4–8px, between groups 12–16px, so the gap between groups is at least twice the gap inside one. Use `hr` for major sections only — three stacked dividers in a short card is a density smell, not structure.

Typography carries hierarchy: bold the value, grey the label, keep the rest neutral. Do not add containers, icons, or color to compensate for a flat hierarchy.

## Avoid pseudo-visuals

Markdown-only bodies cannot draw gauges, bars, or sparklines. Character bars and emoji meters misalign across clients and read as decoration. Express magnitude with value + limit + ratio instead, or move the trend to a `chart` component where the target spec allows it.

## Verification

Four checks, each pass/fail:

1. **Squint:** at 30% scale or blurred, is the single most important number still obvious?
2. **Grayscale:** with color removed, do the conclusion and the state words still read?
3. **Three seconds:** can someone who has not seen it name the object, the issue, and the severity?
4. **List view:** shrunk to one message row, does the title still carry the conclusion?

Also count the first-screen units and the number of eye returns. More than seven units, more than one decision, or more than two returns: delete a field or restructure a row — never shrink the type.
