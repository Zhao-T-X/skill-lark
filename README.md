# skill-lark

[![skills.sh](https://skills.sh/b/Zhao-T-X/skill-lark)](https://skills.sh/Zhao-T-X/skill-lark)

飞书卡片设计 Agent Skill 集合。当前包含一个 Skill：`feishu-card-design`，用于设计、改进、评审或实现飞书交互式 Card 2.0 监控告警卡片，以移动端优先为核心，让读者在首屏即可看清**受影响对象、异常问题、影响范围**。

## 安装

### 通过 skills CLI（推荐）

```bash
npx skills add Zhao-T-X/skill-lark
```

只安装 `feishu-card-design` 这一个 Skill：

```bash
npx skills add Zhao-T-X/skill-lark --skill feishu-card-design
```

CLI 会自动识别当前使用的 Agent（Claude Code、Cursor、Windsurf 等）并安装到对应目录，也可用 `-a` 指定目标：

```bash
npx skills add Zhao-T-X/skill-lark -a claude-code
```

### 手动安装

克隆仓库后，将 `skills/feishu-card-design/` 复制到你的 Agent Skill 目录：

```bash
git clone https://github.com/Zhao-T-X/skill-lark.git
cp -r skill-lark/skills/feishu-card-design .claude/skills/   # Claude Code 示例
```

其他 Agent 的 Skill 目录：Cursor 为 `.cursor/skills/`，Codex 为 `.agents/skills/`。

安装后在对话中提出卡片设计需求即可自动触发。

## Skill 一览

### feishu-card-design

面向运维/监控场景的飞书告警卡片设计规范，覆盖五类卡片（巡检、告警、恢复、汇总、执行错误）的设计决策、首屏信息密度、Card 2.0 JSON 构建与验收评分。适用请求示例：

- 「帮我设计一张 Redis 巡检卡片」
- 「评审这张告警卡片的首屏结构」
- 「根据这份数据生成 Card 2.0 JSON」

## 目录结构

```
skill-lark/
└── skills/
    └── feishu-card-design/
        ├── SKILL.md              # Skill 主文档：设计原则与工作流程
        ├── references/
        │   ├── format.md         # 格式选择决策表（卡片 vs 列表 vs 文档）
        │   ├── density.md        # 首屏信息密度预算与突出层级
        │   ├── components.md     # Card 2.0 组件标签选择指南
        │   ├── transpose.md      # 宽表格转置为分组块的方法
        │   ├── acceptance.md     # 卡片评分与验收检查清单
        │   └── refinements.md    # 已知差距与源设计规范的调和说明
        └── agents/
            └── openai.yaml       # Codex UI 元数据
```

## 核心设计原则

- **先定格式**：卡片只是众多格式之一，先判断是否值得用卡片（需要一眼读阈值、需要分钟级决策等），否则用纯文本或摘要。
- **对象 · 结论标题**：如「订单缓存 · 连接使用率超阈」，而非笼统的「Redis 性能告警」。
- **风险分级排序**：不可用 > 数据可靠性 > 资源耗尽 > 性能 > 关注项，同级内按与阈值的偏差幅度排序。
- **首屏稀疏**：第一屏最多 7 个信息单元（标题、当前值、阈值、趋势、三个可比指标），其余放入折叠面板或详情。
- **移动端为验收标准**：以约 302px 窄屏预览为准，禁止横向滚动与截断关键值。
- **数据真实**：状态颜色严格由阈值规则推导，区分真 0、无数据、采集失败，不虚构原因与影响。
