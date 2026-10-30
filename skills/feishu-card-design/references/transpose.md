# Wide tables to narrow cards

A result set with many columns is not a card. The card body is fixed at about 302 px on mobile; a six-column table needs roughly 400 px, so the right-most column — usually the rate that matters — is the one that clips. Transpose before styling.

## Decide whether a table can survive

| Shape | What to do |
|---|---|
| 1–3 short columns, 1–5 rows, comparison is the whole point | A table is acceptable, values short, numbers right-aligned, unit in the header |
| 4+ columns, or any long value | Transpose into blocks |
| 2+ repeated dimension columns (`company`, `channelType`, `source`) | Hoist them into the block title |
| History over many buckets | Fold into `collapsible_panel`, one line per bucket |
| Full history, SQL, logs, timelines | Document, linked from the card |

"Compact" is not a reason to keep a wide table. If fitting it requires narrower columns or smaller type, the structure is wrong, not the spacing.

## The transpose, in order

1. **Measure.** `columns × minimum readable width` against about 278 px of content width. Over budget: stop, the table is out.
2. **Hoist dimensions.** Repeated dimension columns become the group title (`ECOVACS · DY · 晓多`), stated once. This removes two or three columns and repeats nothing.
3. **Turn rows into blocks.** Remaining metrics become three slots of the same unit and purpose (`总数 / 超时数 / 超时率`), or one line per item when there are four or more.
4. **Fold history.** Time buckets move inside a collapsible panel; the summary stays outside.
5. **Reconcile.** The top-level total must equal the visible parts: `14 = 12 + 2`, with `另有 2 个实体 · 共 2 次` when the rest is collapsed. Never leave a silent difference.
6. **Count.** State omitted items (`另有 N 项`), never just cut them.

## Do not rebuild a table from characters

Box drawing and character alignment do not survive proportional fonts, CJK widths, or client-side wrapping:

```
┌────────────┬────────┬────────┐
│ 14:00      │ 587    │ 0.85%  │
└────────────┴────────┴────────┘
```

The same goes for emoji meters (`▰▰▰▱▱`) and ASCII bars. Use blocks, rows, or nothing. A card that looks like a terminal dump reads as machine output, not as a message someone wrote for a reader.

Minimal acceptable expression of one bucket: `14:00 · 587 总 · 5 超时 · 0.85%`.

## What the transpose buys

- Every value sits within the first 302 px, so nothing clips.
- The group title replaces repeated dimensions, cutting noise rather than information.
- The reader answers "which entity is worst" from the title, then "is it improving" from the folded buckets, in that order.

## Target shape

```
标题      整通超时 0.20% · 集中在 ECOVACS
摘要      超时 14 · 会话 6,992 · 超时率 0.20%
分组      ECOVACS · DY · 晓多 · 超时 12      [展开]
  14:00   587 总 · 5 超时 · 0.85%
  14:30   594 总 · 5 超时 · 0.84%
  15:00   791 总 · 1 超时 · 0.13%
收口      另有 2 个实体 · 共 2 次
页脚      14:00–16:00 · 2026-10-29 · 30 分钟粒度
```

Only the worst bucket deserves three metric slots. Repeat blocks for every bucket and density returns through the back door.
