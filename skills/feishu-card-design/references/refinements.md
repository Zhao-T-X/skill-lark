# Reconciliations with the supplied card specification

Use the user's current request and repository spec as authority. This note resolves ambiguities observed in the supplied `FEISHU_CARD_DESIGN_SPEC.md`; it does not silently change that source document.

## Decisions applied in the Skill

1. **Mobile acceptance:** use the actual Feishu narrow-card preview (about 302 px in the narrow layout; confirm in the current client/builder). The first view should expose object, issue label, and value; include threshold when provided. A 360 dp phone viewport is a device context, not the card content width.
2. **Three metric columns:** the supplied project spec requires exactly three fixed `column_set` columns. Keep that when the metrics fit. When they do not, stack metrics with `markdown`; do not create a two-column variant unless the project spec changes.
3. **Zero semantics:** a zero is a true measured value. Mark it abnormal only when an explicit threshold, expected cadence/value, or business rule says zero is unexpected. Show that rule/expectation to the reader when possible.
4. **Card-type structure:** the general inspection order does not force alerts, recoveries, timeouts, or errors into the same layout. Keep the incident conclusion visible and use the smallest type-appropriate summary.
5. **Sorting:** order by explicit severity / threshold breach, then largest relevant deviation, then stable name or ID.
6. **Action text:** show a cause or remediation only when it comes from supplied data or an approved playbook. Otherwise state the symptom and diagnostic identifiers.
7. **HTML versus Feishu:** HTML helps review hierarchy and spacing but cannot validate Feishu markdown rendering or client behavior. Use the current Feishu preview for production visual validation.
8. **Emoji:** the source proposes an allowlist but does not make it mandatory. Use only when useful, and always keep a text status beside color/emoji.
9. **Scope:** engineering constraints belong in production-payload tasks; do not burden visual-only feedback with API or service architecture details.
10. **Risk tiers and trend (from 飞书监控卡片设计原则):** severity ordering is fixed tiers (不可用 > 数据可靠性 > 资源耗尽 > 性能 > 关注项), title follows the "对象 · 结论" pattern, trend answers "is it worsening" while the current value answers "is it abnormal", and cumulative counters are total + period increment without event phrasing. Threshold direction must come from the metric specification (the local strict `>` rule is for upper-bound dimensions); never infer business impact from a metric alone. These are Skill defaults; a repository spec overrides them.
11. **Density and prominence (from monitoring-card review):** the first screen is capped at seven information units and one decision; only the title and the value/threshold pair may be loud; each fact, unit, and threshold appears once; non-decision fields move to subtitle, footer, or panel. Reduce density by deleting fields, never by shrinking type below 12px. Visual identity gets one signature element per card type, and alert cards prefer consistency over novelty. Baseline evidence: an unguided design produced 12 first-screen facts, duplicated cumulative and current values, and a title carrying two states.
12. **Format selection before card design:** a card must earn its interruption — threshold/state at a glance, decision within minutes, an action in the message, or structure that prevents a misread. One fact and no action is plain text; 7+ objects or a recurring sweep is a digest with counts, worst 2–3, and a link; long timelines, SQL, and full tables live in a document. Cadence is a hard gate: a per-round card for an inventory sweep is a defect regardless of card quality. Baseline evidence: an unguided design sent one card per 5-minute round for 40 domains (288 interruptions a day) instead of a digest, and treated a card as the default for a one-line release notice.
13. **Official design spec adopted where it does not conflict:** design principles are efficiency, flexibility, and instinct — do not invent interaction or visual styles; semantic colors are green complete, orange warning, red error, grey expired; using a color title on every card cancels the emphasis; mobile cards are a fixed ~302 px and desktop cards range 302–605 px; spacing follows a 4px grid with ~12 px container padding, 12 px heading-to-content, 8–16 px inside a group. Sources: open.feishu.cn message-card design specifications and the Feishu card component spec.
14. **Wide-to-narrow transpose (from a three-iteration monitoring card):** an unguided redesign moved a text table to a native table, then to blocks plus folding because a six-column table clipped on mobile. The rule is hoist repeated dimension columns into the group title, turn metric columns into slots, fold history, reconcile totals against parts (`14 = 12 + 2`), and count omissions. Box-drawing characters and emoji meters are never a table. Baseline: a blind run produced the right structure but drew a `┌─│└` table and invented a severity (`告警 · 中`) with no threshold supplied.

## Review checklist

- A phone reader can identify affected object + issue + value in the first view.
- The card uses the correct type and state; no fake zeros or unsupported two-column metric layout.
- Units, threshold, time window, and local time zone are clear without repeating facts.
- Abnormal text has a textual label; normal values remain neutral.
- No horizontal scroll or clipping in both narrow and desktop previews; long identifiers/diagnostics remain secondary without hiding the issue/value.
- No cause, business impact, expected value, or action was invented.
- Cumulative counters are totals with period increments, not "now happening"; trend is present when samples allow it.
- Footer/收口 wording exists ("另有 N 项异常") with at least one abnormal example retained when content is dense.
- First screen holds seven information units or fewer and one decision; nothing below 12px; one element reads as strongest in grayscale.
- No character-drawn tables or emoji meters; totals reconcile with the visible parts.
- Title color follows the state, and a card with no breached threshold is not colored as an alert.
- Production payload follows the project component allowlist, deterministic order, escaping, and element/byte/nesting budgets.
