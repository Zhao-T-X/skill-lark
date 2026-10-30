# Feishu card acceptance and score rubric

Use this when reviewing a design or forward-checking an output. A skill that contains a rule has not automatically passed that rule; score the produced artifact.

## Format checks

- Is a card the right carrier, or would plain text, a digest, a document link, or a chart carry it with less interruption?
- Does the object count match the structure (1 → card; 2–6 → rows; 7+ → digest with counts and a link)?
- Is the cadence survivable — one message per round, per unchanged state, or per event storm would be a defect?
- Are actions in the card (with callbacks), while long diagnostics live behind a collapse or a link?

## Density and prominence checks

- Count the first-screen information units (title, value, threshold, trend, three metrics). Seven or fewer, one decision.
- Is exactly one element the strongest? Is the metric row quieter than the main value?
- Does any fact, unit, or threshold appear twice? Is the time window stated once?
- Would deleting each secondary field change the decision? If not, was it demoted to subtitle, footer, or panel?
- Does any body text sit below 12px, or was density reduced by shrinking type or gutters?
- Squint check: is the most important number still the most obvious? Grayscale check: do the conclusion and state words still read?
- One-message-row check: does the title alone still carry object and conclusion?

## Fast pass/fail checks

- At the first mobile view, can a reader identify the affected object, issue label, and measured value? Include threshold when one is supplied.
- Does the title state both object and conclusion ("对象 · 结论", e.g. "订单缓存 · 连接使用率超阈")? Time, address, role, and totals sit in the subtitle/object line, not the title.
- Are abnormals ordered by risk tier (不可用 > 数据可靠性 > 资源耗尽 > 性能 > 关注项), with the largest deviation from threshold first within a tier?
- Does every abnormal value include a text state and unit? Are only metrics with an explicit comparison direction judged against threshold (upper bound `>`; lower bound only when specified), with equality normal?
- Does a real `0`, no matching data, and collection failure have distinct wording and styling?
- Does the card avoid turning an absent field into a negative claim such as "no failures" or "not observed"?
- Are thresholds applied as specified (for this project, strict `>` for upper-bound dimensions; equality is not a breach; `<= 0` disables that dimension)?
- Does the card obey the current project component allowlist, time/unit formats, Markdown escaping (`[ ] * backtick < >`), and size limits? Are dynamic values escaped separately from trusted markup, and are exact-source fields kept out of transformed display text?
- Is any wide table rebuilt from characters (`┌─│└`, ASCII bars, emoji meters), or transposed into grouped blocks and folded detail? Are tables limited to three short columns when they survive at all?
- Do partial results reconcile with the stated total (`14 = 12 + 2`), rather than leaving a silent difference?
- Is the title color tied to the current state, not reused for every card regardless of state?
- At the actual narrow Feishu preview (about 302 px in narrow layout) and desktop preview, is there no horizontal scroll or clipping of the issue/value? Is any hidden content secondary?
- Are facts, business impact, and actions supported by input data or a known playbook, with no invented cause/remedy?
- With consecutive samples, does the main abnormal carry a trend signal (delta/window/period increment)? Are cumulative counters shown as totals — and never described as events happening now?
- If using `column_set`, are there exactly three short, independently comparable metrics; otherwise are metrics stacked?
- Can the same input produce the same order and output? Under dense data, is at least one abnormal example preserved and every omission counted?

## Eight-dimension scorecard (0–10 each)

| Dimension | Weight | A 10 requires |
|---|---:|---|
| Stability | 20% | All data states and thresholds are correct; repeated input yields deterministic output; production limits are checked. |
| Simplicity | 10% | Main skill is concise, loads only relevant references, and does not prescribe unrelated work. |
| High usability | 10% | Output type matches request; user can apply it without guessing the next step. |
| Specification accuracy | 15% | No conflict with current repository/platform rules; tags and fields are checked against current JSON 2.0 docs; examples are identified as fragments or validated full payloads. |
| First-screen issue recognition | 20% | Before scrolling, object + issue + value (and threshold where available) are all visible and unambiguous. |
| Mobile readability | 10% | Narrow preview has no horizontal scroll/clipping; text and metrics remain readable. |
| Information integrity | 10% | Zero/no-data/failure, units, threshold, time window, partial errors, and truncation are represented truthfully. |
| Maintainability | 5% | Rules have an identified source, references are linked, and the core instructions do not duplicate long catalogs. |

Weighted result = sum of (dimension score × weight). A target of 9.5 requires each high-weight outcome to pass, not merely a high average from easy categories. Report any dimension without artifact-based evidence as **not validated**, not as a passing score.

## Forward-check scenarios

Use representative inputs (not only the examples in the skill):

1. **Healthy snapshot:** no abnormal section; valid true-zero metrics remain neutral.
2. **Threshold breach:** affected object, actual value, threshold, and status appear in the initial mobile view; state color follows data; a trend delta appears when consecutive samples are supplied.
3. **Zero / no data / collection failure:** all three produce distinct outputs; failure never displays a fake zero; zero is abnormal only if an explicit expectation says so; threshold equality stays normal.
4. **Timeout/error:** type-specific compact incident layout; show elapsed/limit, object/tool, and trace ID without inventing a cause.
5. **Dense and long input:** long names/SQL, many rows, and partial failures do not push the issue below the first view; truncation/omission is explicit and counted; at least one abnormal example remains; payload stays within the project budget.

After each scenario, inspect the actual produced card or JSON, not just the skill text. Revise only rules that the scenario demonstrates are missing or ambiguous.
