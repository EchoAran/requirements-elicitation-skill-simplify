<p align="center">
  <img src="./pic/poster.png" alt="需求访谈技能海报" width="100%" />
</p>

<h1 align="center">需求访谈技能 (轻量级版)</h1>

<p align="center">
  <a href="./README.md"><strong>English Version</strong></a>
</p>

<p align="center">
  一个纯粹由 Prompt 驱动的轻量级 Agent 技能，用于执行半结构化的多轮需求调研访谈。
  它剔除了过度复杂的系统工程包袱，让模糊的想法轻松沉淀为可追踪的结构化需求产物。
</p>

## 这个技能能做什么
- 以自适应多轮访谈替代一次性问卷。
- 在 Agent 内存与本地 JSON 中维护带有置信度与证据链的访谈框架。
- 支持需求演进过程中的动态话题调整。
- 识别、记录并优先澄清需求矛盾。
- 最终输出两个产物：
  - `final_interview_framework`（JSON）
  - `requirements_summary_report`（Markdown）

## 如何集成 (Agent 环境)

这个版本的技能经过了**大刀阔斧的去工程化**，完全依赖现代 AI Agent（大模型）的长上下文记忆和基础的文件读写能力。

你可以轻松将它集成到：
- **Cursor**（通过 `.cursor/rules/` 挂载）
- **Windsurf**（通过 `.windsurf/rules/` 挂载）
- **Claude Code** 等其他支持本地文件操作的 CLI Agent 中。

### Cursor/Windsurf 快速开始
1. 将此仓库克隆到你的项目目录（例如 `.cursor/skills/`）下：
   ```bash
   mkdir -p .cursor/skills
   cd .cursor/skills
   git clone https://github.com/EchoAran/requirements-elicitation-skill.git
   ```
2. 创建一个适配规则文件（例如 `.cursor/rules/requirements-elicitation.mdc`），在里面使用 `@` 或相对路径指向此技能的 `SKILL.md`。
3. 在你的 Agent 中发起需求访谈，例如：
   *“请帮我做一个校园二手交易 App 的需求访谈。”*

## 仓库结构
```text
.
├── SKILL.md                                # 核心 Prompt 入口与主流程编排
├── README.md                               # 英文文档
├── README-zh.md                            # 中文文档
├── assets/
│   ├── interview_framework_schema.json     # 运行时 Agent 维护的 JSON 结构规范
│   └── requirements_report_format.md       # 最终需求报告的输出模板
├── references/                             # 策略层文档（Agent 按需加载）
│   ├── checkpoints.md                      # 阶段判定与定性完工检查清单
│   ├── conflict_resolution.md              # 矛盾检测与处理逻辑
│   ├── generate_speak.md                   # 如何生成下一轮的访谈话术
│   ├── intent_routing.md                   # 用户意图分类与产品类型偏置
│   ├── select_current_topic.md             # 话题路由逻辑
│   ├── topic_dependency_map.md             # 话题的前置依赖关系
│   └── update_framework.md                 # JSON 框架的修改与填槽规则
└── examples/                               # 各种边界情况的处理示例
    └── ...
```

## 核心运行循环

在每一轮对话中，Agent 会在后台（不向用户展示）默默执行：
1. **检查阶段 (Check Phase)**：判断是开始、运行中还是已完成。
2. **意图分类 (Classify Intent)**：理解用户刚才说的话属于什么意图。
3. **更新框架 (Update Framework)**：根据 `update_framework.md` 修改内存中的 JSON 结构。
4. **检测矛盾 (Detect Contradictions)**：标记冲突并准备追问。
5. **选题与话术 (Select Topic & Generate Question)**：严格输出**一个**聚焦的问题。
6. **状态持久化 (Persist State)**：用基础的写入工具覆盖保存 `state/interview_framework.json`。

## 为什么是轻量级版？
原版设计中包含了一套媲美生产级数据库的事务系统（包含多版本并发控制 MVCC、原子提交、回滚 Checkpoint 等成百上千行的 Python 脚本）。这在真实的 Agent 环境中极易引发工具调用错误，且严重浪费大模型的 Token 和注意力。

本版本**彻底移除了所有系统工程脚本**，只保留了该 Skill 最精华的**“访谈策略和规则”**。它让大模型专注于“如何把访谈做好”，而不是“如何扮演一个数据库”，从而极大提升了可靠性与部署便利性。

## 许可证
专有许可（见 `SKILL.md` frontmatter）。
