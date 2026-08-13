# skills

> 一组可复用、可移植的 AI 助手 Skills，帮助代理在重复任务中遵循稳定的工作流、边界与验证要求。

本仓库的核心是每个 Skill 目录中的 `SKILL.md`。它描述任务何时触发、应如何执行以及需要遵守的约束，不绑定某一个 AI 产品或代码代理。不同宿主对 Skill 的发现、安装和调用方式可能不同；请按宿主的机制导入目录，并保留目录内的相对结构。

## 适用场景

- 希望把个人或团队反复使用的 AI 工作流沉淀为可复用资产。
- 希望 AI 在操作数据库、澄清需求或修改代码前遵循明确的安全边界。
- 希望为不同类型的仓库建立可追溯、可维护的工程约定。
- 希望避免每次对话都重复说明流程、风险与验收要求。

## 内置 Skills

| Skill | 解决的问题 | 典型触发 | 关键约束 |
| --- | --- | --- | --- |
| [`project-conventions`](./project-conventions/SKILL.md) | 创建、审查或更新项目规范文档 | 需要 `CONVENTIONS.md`、`AGENTS.md`，或要梳理目录职责和架构边界 | 默认先提案；规则必须有仓库证据；子模块只能显式覆盖父级规则 |
| [`dbx-mcp`](./dbx-mcp/SKILL.md) | 通过 DBX MCP 安全地检查和操作数据库 | 查看连接、检查 schema、编写/执行 SQL、导入结构化数据 | 先发现连接与 schema；高风险写操作须有明确确认；写后复查 |
| [`clarifying-development-requirements`](./clarifying-development-requirements/SKILL.md) | 将模糊开发想法澄清为可实施、可验收的需求 | 功能、缺陷、重构或 UI 请求存在实质歧义 | 先做只读调查；只询问会改变结果的关键决策；区分需求确认与执行授权 |

## 快速开始

### 1. 获取仓库

```bash
git clone https://github.com/xbd6666/skills.git
```

也可以只获取所需的单个 Skill 目录。

### 2. 导入到你的 AI 助手

将目标 Skill 的完整目录导入或复制到宿主所识别的 Skills 位置。例如，使用 `project-conventions` 时，应一并保留：

```text
project-conventions/
├── SKILL.md
├── agents/
│   └── openai.yaml
└── assets/
    ├── AGENTS.template.md
    ├── CONVENTIONS.template.md
    └── DISCOVERY.template.md
```

- `SKILL.md` 是必需的通用指令入口。
- `assets/` 中的资源由 Skill 在需要时读取或复用，不能只复制 `SKILL.md`。
- `agents/openai.yaml` 是可选的界面元数据；不识别它的宿主可以忽略，不影响 `SKILL.md` 的核心工作流。

> 以下示例使用 `$skill-name` 形式调用。若你的宿主采用其他调用语法，请按其文档替换触发方式。

### 3. 调用 Skill

```text
Use $project-conventions to propose evidence-backed CONVENTIONS.md and AGENTS.md for this project.

Use $dbx-mcp to inspect my DBX connections and help write a safe SQL query.

使用 $clarifying-development-requirements 澄清这项不完整的开发需求，并整理为可执行、可验收的说明。
```

## project-conventions 工作方式

`project-conventions` 用于把仓库中实际存在的结构、构建方式和边界，整理为人类与 AI 代理都能遵守的项目约定。

### 三种模式

| 模式 | 行为 | 是否写入文件 |
| --- | --- | --- |
| `audit` | 检查现有约定文档，报告缺口与冲突 | 否 |
| `propose` | 输出发现报告、推荐布局与变更摘要 | 否；默认模式 |
| `apply` | 按已确认的范围创建或更新约定文档 | 是 |

明确要求“生成”“创建”“更新”或“写入”时，Skill 才会进入 `apply`。对于已有文档，还可按 `preserve`、`update` 或 `replace` 区分保留、最小更新和明确重写。

### 生成前的证据链

在形成规则前，Skill 会记录：

1. 请求的模式、范围、布局、语言和已有文档处理策略。
2. 从仓库根到目标目录的 `CONVENTIONS.md` 与 `AGENTS.md`。
3. 每项“证据 → 观察 → 推断 → 规则影响”。
4. 无法从仓库确认的假设与待确认事项。

因此，未从仓库证实的框架、验证命令、职责边界或依赖方向不会被伪装为既定事实。

### 规范文档的继承

- 父级 `CONVENTIONS.md` 默认适用于子目录。
- 子模块只可通过 `Overrides` 或“覆盖项”明确覆盖父级规则。
- 同目录的 `CONVENTIONS.md` 优先于 `AGENTS.md`；后者只保留简短的操作入口与高风险提醒。
- 发现未显式声明的规则冲突时，Skill 会报告冲突而非自行裁决。

常见产物：

- `CONVENTIONS.md`：面向团队的长期工程约定。
- `AGENTS.md`：面向代理的短入口说明。
- 发现报告：本次规则、布局和改动建议所依据的证据。

## dbx-mcp 工作方式

`dbx-mcp` 适用于已提供 DBX MCP 能力的宿主。它不保存任何项目的账号、密码或连接信息，而是要求代理按以下顺序工作：

1. 查找或建立连接。
2. 检查表、视图和 schema。
3. 使用限定条件执行读取查询。
4. 对写入、删除、DDL 或大范围更新先进行风险确认。
5. 写入后重新查询受影响记录、计数或完整性条件。

如果 DBX MCP 不可用，Skill 会要求先发现相应能力，而不是凭空假设数据库连接或凭据。

## clarifying-development-requirements 工作方式

该 Skill 面向“不同答案会明显改变实现结果”的开发请求。它会：

1. 区分用户已确认的目标、已观察到的当前事实、建议默认值与待确认事项。
2. 先进行安全的只读调查，避免向用户重复询问可从项目中发现的信息。
3. 按影响、未知程度与不可逆性优先澄清高风险决策。
4. 将需求确认、工作区修改授权和仓库/外部系统授权分别处理。
5. 在信息充分后输出带验收标准、范围和非目标的结构化需求。

对于清晰、局部、可逆的请求，它不会为了“完整”而阻塞实施。

## 目录结构

```text
.
├── clarifying-development-requirements/
│   ├── SKILL.md
│   └── agents/
│       └── openai.yaml
├── dbx-mcp/
│   ├── SKILL.md
│   └── agents/
│       └── openai.yaml
└── project-conventions/
    ├── SKILL.md
    ├── agents/
    │   └── openai.yaml
    └── assets/
        ├── AGENTS.template.md
        ├── CONVENTIONS.template.md
        └── DISCOVERY.template.md
```

## 兼容性与边界

- 核心指令以 `SKILL.md` 提供，目标是可迁移到支持 Skills 或等价指令机制的 AI 助手。
- 仓库中存在的 `agents/openai.yaml` 仅是某类宿主可使用的可选界面元数据，不是使用 Skill 的前置依赖。
- 不同宿主的安装位置、自动发现机制、工具命名与调用语法可能不同；导入前请查阅对应宿主的说明。
- `dbx-mcp` 依赖 DBX MCP 及相应数据库访问权限；其余 Skills 不要求特定外部服务。

## License

本仓库当前未包含独立的 `LICENSE` 文件。正式发布或允许他人再分发前，建议补充明确的开源协议。
