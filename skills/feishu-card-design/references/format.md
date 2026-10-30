# Choose the format before the card

A card is one of several delivery formats. Choosing the wrong one makes a well-designed card harmful: it costs attention, interrupts, and trains people to ignore the channel. Decide the format from four facts — object count, structure, action, cadence — then design inside it.

## Formats and when they win

| Format | Use when | Typical content |
|---|---|---|
| Plain text message | One fact, no threshold, no action; readers need to know, not decide | `发布完成 · order-api v2.14.3 · 生产 · 4m12s` |
| Rich-text post | Several paragraphs or sections, no state color, no interaction | Release notes, short incident narration |
| Interactive card | One object with threshold or state, a trend, or an action button | Alert, inspection, recovery, execution error |
| Card with vertical rows (list in a card) | 2–6 uniform objects compared on the same fields, all on screen | Which instances are abnormal, worst first |
| Digest card | Many objects, recurring cadence, or an inventory sweep | Counts by state, worst 2–3, remaining count, link |
| Document or table link | Long text, SQL, full timelines, large tables, anything needing search or copy | Postmortem, runbook, full metric table |
| Image or chart | A shape is the message and precision does not matter | Trend curve, capacity ceiling |

A card is justified when at least two of these hold: a threshold or state must be read at a glance, a decision is expected within minutes, an action must be taken from the message, or mobile first-screen structure prevents a misread. Otherwise the cheaper format wins.

## Object count drives the structure

- **1 object, 1–3 facts, no action** → plain text. A card adds ceremony and fixes nothing.
- **1 object, threshold breach, trend, or action** → a card.
- **2–6 objects, same shape** → one card with vertical rows, sorted by risk, no three-column layout across objects.
- **7+ objects** → digest: counts by state, the worst 2–3 named, `另有 N 项`, link to the full list. Never enumerate.
- **10+ fields on one object** → card holds conclusion plus three comparable metrics; the rest goes into a panel or a linked document.

## Cadence decides whether a card may exist at all

Cards interrupt. Match the format to how often the reader can afford to be interrupted:

- Real-time breach on a single object → card, once per state change, with escalation when unacknowledged.
- Recurring sweep over an inventory (per-minute or per-5-minute) → do not send one card per round. Aggregate into a digest window, or send only when the state worsens or persists, and include how long it has persisted.
- Repeated identical message for an unchanged state → suppress; state the count on the next occurrence instead of re-sending.
- Daily or shift summary → one digest card, sent at a fixed time, covering closed and open items.
- Event storms → one rollup with counts, never N cards. If the transport cannot roll up, the design is wrong, not the card.

## Alert etiquette

A card is an interruption. Treat the interrupt budget as a first-class constraint, not a styling question.

- **One card, one message.** If two unrelated facts share a card, one of them will be missed.
- **Interrupt only when someone can act.** Informational updates do not deserve a card; platform guidance is explicit that an alert carrying no action wears out the channel. Text or a digest covers them.
- **Group, inhibit, silence, escalate.** These are the four levers, and they belong to the alerting design, not the card: group related alerts into one message, suppress downstream alerts caused by the same fault, allow a time-boxed silence, and escalate when an acknowledged alert stays open. A card that re-sends an unchanged state is fighting all four.
- **Send on change, not on poll.** If the collector runs every 5 minutes, the card does not.
- **Color carries state, and only that.** Green complete, orange warning, red error, grey expired. In a direct message, change the color with the state; using one color for every card removes the emphasis entirely and reads as visual noise.

## Actions belong in cards, facts belong in text

- Buttons (`确认`, `静默`, `打开手册`) require an interactive card and a defined callback. Do not fake an action with text like "回复 1 确认".
- A card without an action must still say who owns the next step, in words, if that is supplied.
- Long diagnostic material — SQL, logs, timelines — goes behind a collapse or a link. A card that requires scrolling to find the conclusion has chosen the wrong format.

## List or card: the discriminator

"List" means two different things; pick deliberately.

- **Text list** (a few bullets in a message): items are informational, no shared threshold, no state color, no comparison needed. Cheaper, less intrusive, searchable.
- **Card with vertical rows**: items share fields, the reader compares them (name, value, threshold, state), and order carries meaning (worst first).
- **Card with three-column metrics**: only when three numbers of the same unit and purpose are compared for one object. Never for objects, and never for values that need context.

When a table cannot fit, transpose it rather than shrinking it: see [transpose.md](transpose.md).

If the reader would scan a column ("which instance is worst?") the rows belong in a card. If the reader would read sentences, use text.

## Anti-patterns

| Pattern | Why it fails | Instead |
|---|---|---|
| Card for "发布完成" | Costs a card slot to say one line | Plain text |
| One card per 5-minute sweep | 288 interruptions a day; people mute the bot | Digest window or state-change trigger |
| 20 uniform objects enumerated in a card | First screen lost, reader cannot rank | Digest with counts and worst 3 |
| Table image of numbers | Cannot copy, search, or read on small screens | Card rows or a table link |
| Long SQL, logs, or timelines in the card body | Conclusion pushed below the fold | Collapse or document link |
| Text message carrying a threshold breach | No color, no structure, easily missed | Interactive card |
| Same alert re-sent on every round | Alarm fatigue, real signals drowned | Suppress unchanged state, report persistence |

## Worked examples

- Single Redis at 98% connection usage, on-call must ack within 10 minutes → interactive card: conclusion, value vs threshold, trend, `确认`/`静默`, escalation if unacknowledged.
- 40 domains × 3 MySQL metrics every 5 minutes → digest card per 30–60 minutes (or on state change): abnormal domain count, worst 3 rows, `另有 N 项`, link to the full table. Not 40 cards, and not one card per round.
- Two-hour postmortem with 12 timeline entries, 5 SQL statements, 8 curves → document, posted with a one-line abstract; the card only if an owner must act.
- Release finished, no anomaly → plain text, one line.
- Daily 09:00 rollup of 12 alerts, 3 open → digest card: open items first with owner and age, closed counts, link.
