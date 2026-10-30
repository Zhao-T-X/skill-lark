---
name: feishu-card-design
description: Design, improve, review, or implement Feishu interactive Card 2.0 monitoring and alert cards, with a mobile-first focus on making the affected object and issue immediately clear.
metadata:
  short-description: Design clear, reliable Feishu cards
---

# Feishu Card Design

Help the reader see **what is affected, what is wrong, and how large the issue is** in the first mobile view. Treat that as the primary outcome; visual polish and extra detail come after it.

## Choose the format first

A card is one format among several. Before designing one, check that a card is justified: a threshold or state must be read at a glance, a decision is expected within minutes, an action must be taken from the message, or first-screen structure prevents a misread. One fact with no action is a plain text message; 7+ objects or a recurring sweep is a digest with counts, the worst few, and a link; long timelines, SQL, and full tables belong in a document the message points to. Cadence decides whether the card may exist at all — never send one card per round for an inventory sweep. Read [format.md](references/format.md) for the decision table, the list-versus-card discriminator, and worked examples.

## Choose the requested work

- **Analyze/review:** identify the main scan or semantics problems, then show a concrete improved hierarchy or wording.
- **HTML example:** make a self-contained responsive prototype using supplied data. State that it is a visual approximation, not proof of Feishu rendering.
- **Production payload/code:** follow the current repository specification and patterns. Do not expand visual-design work into backend or message-delivery changes.

## Make the design decisions

1. Identify the card type (inspection, alert, recovery, summary, or execution error) and the intended reader/action. Keep repeated cards of the same type structurally consistent. Each type's first screen answers a fixed question: alert — which metric, current value vs threshold; inspection — is the instance healthy and how do core metrics look; recovery — what was resolved and is the value now normal; execution error — which object is unreachable and the failure message; summary — which objects are abnormal and the worst problem.
2. Determine state from explicit thresholds, expected values, or business rules. Distinguish a true `0`, no matching data, and collection failure. Never infer that zero is abnormal without an expectation or rule. When no supplied threshold is breached, the card is an inspection card: the title conclusion states healthy for the configured checks and takes that state's color. An unthresholded signal (evictions, blocked clients, slowlog length) stays evidence and never becomes the title verdict or an alert color.
3. Use an "对象 · 结论" title so the reader learns object and conclusion at once, e.g. "订单缓存 · 连接使用率超阈", "配置中心 · 运行正常", "会话缓存 · 实例不可达". Never a bare "Redis 性能告警" or "发现 4 项异常". Put time, instance address, role, and abnormal totals in secondary details. If an instance ID is long, use a supplied short display name in the title and keep the full ID below; if no alias exists, shorten the title identifier with an ellipsis and preserve the complete ID in details. Do not let metadata push the first issue/value below the first mobile view.
4. Order by risk tier, then largest deviation from threshold, then stable name/ID. Fixed tiers: 不可用 (unreachable, connection refused/near-limit) > 数据可靠性 (persistence failure, replication broken/lagging) > 资源耗尽 (high memory watermark, continued eviction) > 性能 (P99, slow queries, throughput) > 关注项 (hit rate, expiry, fragmentation). Apply each metric's declared comparison direction: `value > threshold` is the supplied project's upper-bound breach rule; a lower-bound metric needs an explicit `<` rule or an explicitly normalized inverse metric. Equality is normal. If direction is absent, do not infer a breach. The first abnormal block states problem name, current value, text status, threshold, unit, and window, e.g. `98.0% 超阈 · 9,800 / 10,000 · 告警阈值 90% · 近 5 分钟 +4.2%`. Omit the threshold slot when the metric has no threshold; use `-` only for a missing value of a metric that should have one. Do not invent causes, business impact, or remediation; show impact only when supported by supplied measurements/business data, and show an action only when supplied by the data or an approved playbook.
4b. When consecutive samples exist, let trend carry urgency: current value answers "is it abnormal", trend answers "is it worsening" (delta vs previous period, 5-minute/1-hour window, or period increment). Cumulative counters (`evicted_keys`, `rejected_connections`, `expired_keys`) are shown only as a total and, after two samples, a period increment, e.g. `累计 12,340 · 本窗口 +850`. Do not add event phrasing such as "正在淘汰" or "持续发生"; the increment communicates the observed change. True zero, no matching data, and collection failure are three different statements; failure never substitutes last round's success data for current state. Do not turn absent input into a negative claim (for example, `未观察到事务失败`)—state `-` or the known unavailable state only for metrics whose collection/missing state is supplied.
5. Remove empty sections and repeated facts. Layer by decreasing decision value: title (object + conclusion) → main abnormal (value, threshold, trend) → three core metrics for horizontal scan → measured impact scope → remaining abnormals and collection basis. Only short independently comparable metrics go in the three-column area (`column_set`); rows needing context (replication lag, persistence state, slow-query detail) are vertical `markdown` rows. For a single-instance alert card keep at least: instance name+address, worst issue, current value+threshold, three core metrics, one trend/impact signal, remaining abnormal count, sampling time, and collection basis.

## Keep the first screen sparse

The first mobile view carries one decision and at most seven information units: title, current value, threshold, trend, and three comparable metrics. Everything else is evidence — put it after the three-metric row, in a `collapsible_panel`, or on another card, and count what you omit. Only the title and the value/threshold pair may be loud; trend is half-loud; the metric row stays quiet. State each fact once, demote fields that cannot change the reader's decision, and reduce density by deleting fields rather than shrinking type or gutters. Read [density.md](references/density.md) for the budget, prominence order, metric-shape mapping, and the four acceptance checks.

## Make mobile the acceptance target

- Preview the actual narrow Feishu card (about 302 px in the narrow layout; use the current client/builder preview as authority). The first view should show object + issue label + value, with threshold when available.
- Follow the platform's spacing rhythm instead of inventing one: 4px grid, about 12px between the container edge and content, 12px between a heading and its content, 8–16px within a group. Keep the platform's type sizes (title 16/14, body 14, secondary 12) and do not create custom visual styles the reader has never seen; the platform's own design principles reward efficiency over novelty.
- A table wider than the mobile body does not fit, whatever the component supports. Transpose it into grouped blocks and folded detail rather than shrinking columns; see [transpose.md](references/transpose.md).
- No horizontal scrolling, clipped key values, or data-dependent columns. In this project's specification, `column_set` uses exactly three fixed columns. If those metrics do not fit, use stacked `markdown` rows; do not silently switch to two columns.
- Keep long SQL, trace IDs, and payloads secondary. If a supplied source link or trace ID exists, use it to recover exact data. Under this project's Markdown-only body rule, any dynamic SQL shown in a card is escaped display text (including full-width `<`/`>`), not an exact copy; do not promise copy fidelity.
- Review both mobile and desktop previews. An HTML browser preview alone cannot validate Feishu component rendering.

## Keep data and state truthful

- Red/orange/green/blue title state is derived from the supplied threshold rules; multi-object cards use the worst state. Use the platform's semantic mapping — green complete, orange warning, red error, grey expired — and change the color with the state; coloring every card the same way removes the emphasis.
- For the supplied monitoring spec, an upper-bound threshold breaches only when `value > threshold`; equality is normal, and thresholds `<= 0` disable that dimension. Lower-bound dimensions need an explicit comparator or normalized inverse metric. Apply minimum-sample and percentage-clamp rules when the source spec defines them.
- Red values also need a text label such as “失败”, “超阈”, or “无索引”. Normal values remain neutral. Use emoji only when useful and within the target's supported set.
- Use concise Chinese, preserve standard technical terms, include units, and show missing values as `-`. Use `Asia/Shanghai` for displayed times and state `[start, end)` once when relevant.

## Build Card JSON 2.0 only when requested

Read [components.md](references/components.md) before selecting or writing element tags. Apply precedence in this order: user's current request, repository/project specification, then this Skill's defaults. For the provided monitoring-card specification, key defaults are:

- `schema: "2.0"`, a header title/template, and body content under `body.elements`.
- No `table`; use the local permitted tag set and Markdown subset. Escape user-controlled text before inserting it into Markdown: `[`→`［`, `]`→`］`, `*`→`＊`, backtick→`＇`, `<`→`＜`, `>`→`＞`. Apply this only to dynamic values, never to trusted template markup. Normalize line breaks for the field; never let user data become a Markdown link or HTML tag. This keeps the value readable but changes those display characters; use a supplied source link or trace ID for exact data recovery. Do not claim exact copy fidelity for dynamic values in the Markdown-only card body.
- Maximum 5 nested levels, 180 elements (platform ceiling is about 200), and 28,000 compact UTF-8 JSON bytes. Keep headroom; when trimming, preserve the conclusion and at least one abnormal example, report omission counts, and keep dense details from pushing the first issue off the mobile view.
- Builders are pure over collected data: no I/O and no current-time reads. Preserve zero/no-data/failure distinctions and deterministic ordering.
- For production code, check the relevant state boundaries, escaping, truncation, ordering, and size limits with the repository's tests.

Read [acceptance.md](references/acceptance.md) to score or forward-check a card. Read [refinements.md](references/refinements.md) for known gaps and reconciliations with the source design spec.
