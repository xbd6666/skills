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
| [`product-development-workflow`](./product-development-workflow/SKILL.md) | 从问题发现到可实施开发需求的完整分阶段工作流 | 产品或开发请求的问题、方案或交付边界尚不明确 | 一个公开入口按阶段分流；保留证据、用户决策与执行授权边界 |

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

使用 $product-development-workflow 将这个产品或开发想法分流到探索、方案或需求澄清阶段。
```

#### product-development-workflow：按当前状态调用

不需要预先判断自己处于哪个阶段。说明已知事实、当前不确定项和希望做出的决定即可；Skill 会从最早的关键未知项开始。

**1. 产品需求探索：问题或价值尚未确认**

```text
使用 $product-development-workflow 帮我判断是否值得为诊所前台做“患者到诊提醒”。
目前只有同事的零散反馈，还不清楚哪些岗位最受影响、现有做法的代价，以及应先验证什么；请先不要设计功能或修改文件。
```

**2. 方案头脑风暴：问题已确认，但不知道怎么做**

```text
使用 $product-development-workflow 设计解决方案。
我们已确认销售在跟进客户时很难找到历史沟通记录；请比较复用现有 CRM 搜索、增加客户时间线和引入 AI 摘要三种方向，并说明各自价值、成本、风险和最小验证方式。
```

**3. 开发需求澄清：方向已选，但交付边界不完整**

```text
使用 $product-development-workflow 澄清需求。
我们决定在现有后台增加“聊天记录导出”功能，但尚未确定谁可以导出、数据范围、脱敏规则、失败提示和验收标准。请先只读检查现有权限与筛选逻辑，再逐项确认高风险决策；暂不修改文件。
```

**4. 直接实施：目标、范围和验收都已明确**

```text
使用 $product-development-workflow 实现以下低风险修改：将管理后台用户列表默认日期范围改为最近七个自然日，重置筛选时恢复同一范围，并补充相应测试。只修改前端筛选默认值和测试，不创建分支、不提交、不调用外部系统。
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

## product-development-workflow 工作方式

该 Skill 对外只有一个入口，内部按当前最早的关键未知项进入三个独立阶段：

1. **产品需求探索**：确认用户、场景、痛点和价值机会，并形成可验证的假设。
2. **方案头脑风暴**：针对已确认的问题，生成、比较并收敛不同的产品、流程或技术方向。
3. **开发需求澄清**：将已选方向整理为带范围、业务规则、验收标准和授权边界的可执行需求。

阶段文档只在需要时读取，避免把探索、方案与实施需求混成同一份结论。清晰、局部、可逆的修改不会因为流程而被阻塞。

## 目录结构

```text
.
├── dbx-mcp/
│   ├── SKILL.md
│   └── agents/
│       └── openai.yaml
├── product-development-workflow/
│   ├── SKILL.md
│   ├── agents/
│   │   └── openai.yaml
│   └── references/
│       ├── product-discovery.md
│       ├── brainstorming.md
│       └── clarifying-development-requirements.md
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
