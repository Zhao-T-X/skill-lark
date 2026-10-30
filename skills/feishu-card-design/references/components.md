# Feishu Card JSON 2.0 components: inventory and selection

This is a **component chooser**, not a copy of every field schema. The official component pages are authoritative for exact properties, nesting, client-version support, and interaction behavior. Recheck them before using less common components because the platform evolves.

## Platform support versus this project's allowed set

Feishu supports components that the supplied monitoring-card spec deliberately excludes. `table` is a platform component but **forbidden by the local spec**. The spec standardizes body text on `markdown`; platform availability of `div` does not override that rule. The current request and repository specification take precedence over these defaults.

Platform limits, for reference: JSON 2.0 requires client 7.20+, allows about 200 elements per card, and `update_multi` must be `true`. JSON 2.0 markdown can render standard Markdown, including tables — but a table wider than the 302 px mobile body clips, so the local no-table rule remains the right default, and `transpose.md` covers the conversion. `column_set` accepts multiple columns on the platform; the local spec still fixes exactly three.

## Card structure (not all of these are body elements)

| Item | Role | Typical use |
|---|---|---|
| `schema` | Root version marker | Set to `"2.0"` for a JSON 2.0 card. |
| `header` | Root title region | `title` is `plain_text`; `template` sets the title color; optional subtitle/icon/tags are secondary. |
| `body.elements` | Ordered list of body components | Put the visible summary and details here. |
| `element_id` | Optional component identity | Stable ID when component update/interaction APIs need to address a component. |

## Display components

| Tag | Use | Monitoring-card guidance |
|---|---|---|
| `markdown` | Rich text, compact detail rows, links, emphasis | Default text component for this project. Use short lines, explicit status words, units, and escaped data. Escape dynamic values separately from trusted template markup; for the local escape recipe see `SKILL.md`. |
| `div` | Structured text component with text styling | Use only if the target spec allows it and segment-level styling materially helps; otherwise use `markdown`. |
| `hr` | Section separator | Separate major blocks only, not each row. |
| `img` | One image | Use only when an image carries diagnostic meaning; an image must not replace the textual issue summary. |
| `img_combination` | Several images in a composed layout | Rare for monitoring; check nesting and phone sizing. |
| `person` / `person_list` | User identity or owner display | Use only when ownership is relevant and identifiers are available; keep secondary to the issue. |
| `chart` | Structured chart data | Use for trend/comparison questions, not a single current value. Check chart spec and phone readability. |
| `table` | Tabular display | Feishu supports it; the supplied project spec prohibits it. Use stacked rows in this project. |
| `audio` | Audio content/player | Specialized content; normally irrelevant to monitoring cards. |

## Layout and container components

| Tag | Use | Constraint / guidance |
|---|---|---|
| `column_set` (contains `column`) | Fixed horizontal layout | Local spec: exactly three columns, each short and used for summary metrics only. If unreadable on mobile, replace the whole metric row with stacked `markdown`; do not create a two-column variant without changing the spec. |
| `collapsible_panel` | Hide secondary details until expanded | Can hold secondary SQL/payload after the issue summary. Under this project's Markdown-only body rule, dynamic contents are escaped display text (for example `<`/`>` become full-width); do not promise exact copy fidelity. Use a supplied trace ID/source link to recover exact data. Never hide the conclusion or first abnormal item; avoid deep nesting. |
| `form` | Collect and submit several values together | Only for a real data-entry workflow. Define names, validation, submit behavior, and callback handling. |
| `interactive_container` | Make a region interactive | Use only when the whole region has a clear action and callback behavior. |

## Interactive components

| Tag | Use | Guidance |
|---|---|---|
| `button` | Link or callback action | Add only a known useful action, such as opening a runbook. Label the action clearly. |
| `overflow` | Menu of secondary actions | Use when several non-primary actions would clutter the card. |
| `input` | Free-text or numeric entry | Interactive workflow only; provide validation and submission behavior. |
| `select_static` / `multi_select_static` | Pick one or several fixed options | Keep options short; define whether selection submits immediately or through a form. |
| `select_person` / `multi_select_person` | Choose one or several people | Assignment workflow only. |
| `date_picker` | Select a date | Use to request a date, not to display one. |
| `picker_time` / `picker_datetime` | Select time or date-time | Use to request scheduling/filter values; render read-only timestamps as text. |
| `select_img` | Choose image options | Specialized image-choice workflow; verify form and callback nesting. |
| `checker` | Check/toggle an item | Checklist/confirmation only; not a decorative status indicator. |

## Minimal JSON 2.0 patterns

Use only patterns permitted by the target spec. These fragments show component shapes, not guaranteed send-ready cards. Validate the complete root payload and each field against the current official schema.

### Text summary and rows

```json
{"tag":"markdown","content":"**🔴 海外邮件：0 行**\n本窗口无新增数据 · 预期至少 1 行"}
```

### Three fixed summary metrics

```json
{
  "tag":"column_set",
  "flex_mode":"none",
  "columns":[
    {"tag":"column","background_style":"grey","padding":"10px 12px","vertical_spacing":"4px","elements":[{"tag":"markdown","content":"<font color='grey'>总写入</font>\n**40 行**\n<font color='grey'>窗口内新增</font>"}]},
    {"tag":"column","background_style":"grey","padding":"10px 12px","vertical_spacing":"4px","elements":[{"tag":"markdown","content":"<font color='grey'>失败项</font>\n**0 项**\n<font color='grey'>全部成功</font>"}]},
    {"tag":"column","background_style":"grey","padding":"10px 12px","vertical_spacing":"4px","elements":[{"tag":"markdown","content":"<font color='grey'>监控项</font>\n**2 项**\n<font color='grey'>ES · MySQL</font>"}]}
  ]
}
```

### Secondary details in a panel

```json
{
  "tag":"collapsible_panel",
  "expanded":false,
  "header":{"title":{"tag":"plain_text","content":"查看 SQL 摘要（展示文本）"}},
  "elements":[{"tag":"markdown","content":"SELECT ..."}]
}
```

### Button with an explicit action

```json
{
  "tag":"button",
  "text":{"tag":"plain_text","content":"打开排查手册"},
  "type":"default",
  "behaviors":[{"type":"open_url","default_url":"https://example.com/runbook"}]
}
```

Replace example URLs and data with supplied values. For callbacks, forms, images, charts, and selectors, consult the matching official component schema rather than extrapolating from these snippets.

## Do not mix versions

- JSON 2.0 requires `schema: "2.0"` and places elements under `body.elements`.
- Do not use the JSON 1.0 `action` wrapper or `note` component; use current `button`/`overflow` behaviors or styled text when allowed.
- `plain_text` is commonly a text object inside fields such as a title or button label, not a generic body component.
- The visual builder may not expose every raw JSON component. Confirm code-only support and target-client compatibility in the official component page.

## Official documentation

- [Card JSON 2.0 structure](https://open.feishu.cn/document/uAjLw4CM/ukzMukzMukzM/feishu-cards/card-json-v2-structure)
- [JSON 2.0 breaking changes](https://open.feishu.cn/document/feishu-cards/card-json-v2-breaking-changes-release-notes)
- [Card builder overview and preview](https://open.feishu.cn/document/common-capabilities/message-card/message-card-builder)
- [Column set](https://open.feishu.cn/document/feishu-cards/card-json-v2-components/containers/column-set)
- [Collapsible panel](https://open.feishu.cn/document/feishu-cards/card-json-v2-components/containers/collapsible-panel)
- [Button](https://open.feishu.cn/document/uAjLw4CM/ukzMukzMukzM/feishu-cards/card-json-v2-components/interactive-components/button)
- [Image picker](https://open.feishu.cn/document/uAjLw4CM/ukzMukzMukzM/feishu-cards/card-json-v2-components/interactive-components/image-picker)
- [Color values](https://open.feishu.cn/document/feishu-cards/enumerations-for-fields-related-to-color)
