# skills

skills 是一个开源的通用 AI 助手技能项目，用于沉淀、组织和推荐可复用的 Skills。

项目通过标准化的 Skill 描述、模板和 AI 助手配置，帮助开发者在不同代码仓库中快速选择合适的技能，让 AI 助手更准确地理解项目结构、生成约定文档，并遵循团队的工程规范。

## 项目用途

- 推荐适合当前任务的 AI 助手 Skill
- 沉淀个人或团队常用的 AI 编程助手能力
- 为不同类型的代码仓库生成工程约定文档
- 让 AI Agent 在修改代码前理解项目边界、目录职责和验证要求
- 通过模板减少重复编写 `CONVENTIONS.md`、`AGENTS.md` 等协作文档的成本

## 当前内置 Skill

### project-conventions

`project-conventions` 用于为任意代码仓库创建或更新项目约定文档。

它会先根据仓库结构判断项目类型，再生成适合该项目的：

- `CONVENTIONS.md`：面向人类和团队的长期工程约定
- `AGENTS.md`：面向 AI 编程 Agent 的短入口说明

适用场景包括：

- 前端应用
- 后端/API 服务
- 类库或 SDK
- CLI 工具
- Monorepo
- 数据/机器学习项目
- 基础设施项目
- 文档站点

### dbx-mcp

`dbx-mcp` 是一个通用数据库操作 Skill，用于在支持 MCP 的 AI 助手环境中规范使用 DBX MCP。

它不绑定具体数据库、项目、账号或密码，而是提供一套通用工作流，帮助 AI 助手在处理数据库任务时先发现连接、检查表结构，再安全地编写、执行和验证 SQL。

适用场景包括：

- 查看 DBX 已配置的数据库连接
- 新增或验证数据库连接
- 列出表和视图
- 查看单表字段结构
- 获取多表 schema 上下文并辅助编写 SQL
- 执行查询并总结结果
- 在 DBX 桌面端打开表或查询结果
- 将文档、表格等结构化数据导入数据库并进行校验

### clarifying-development-requirements

`clarifying-development-requirements` 用于将模糊、零散、不完整或存在歧义的开发想法，逐步澄清为严谨、可执行、可验收的开发需求。

它会先区分已确认信息、待确认事项和可在实施时决定的内部细节，每轮只询问一个影响最大的关键问题；在目标、业务规则、修改范围和验收标准等信息充分后，再输出结构化需求并请用户确认。

适用场景包括：

- 将口述、碎片化的开发想法整理为完整需求
- 补全功能开发中的权限、数据规则和异常边界
- 明确缺陷修复的复现条件、期望结果和验收方式
- 确认重构中必须保持的外部行为和禁止修改范围
- 澄清 UI 调整的目标区域、交互状态和响应式要求
- 在直接实现前确认会影响业务语义、兼容性或公共接口的关键决策

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
        └── CONVENTIONS.template.md
```

## 使用方式

将本仓库中的 skill 安装或复制到对应 AI 助手的 Skills 目录后，即可在支持 Skills 的环境中调用。

例如：

```text
Use $project-conventions to generate CONVENTIONS.md and AGENTS.md for this project.
```

AI 助手会读取目标仓库结构，识别项目类型，并根据实际文件、框架、目录和验证方式生成项目专属说明。

也可以使用 `dbx-mcp` 处理数据库任务：

```text
Use $dbx-mcp to inspect my DBX connections and help write a safe SQL query.
```

AI 助手会优先使用 DBX MCP 工具查看连接、表结构和 schema，再根据任务执行查询、打开 DBX UI 或写入数据，并在写入后反查校验结果。

当开发需求仍然模糊或不完整时，可以使用 `clarifying-development-requirements`：

```text
Use $clarifying-development-requirements to clarify this development idea into an actionable and testable requirement.
```

AI 助手会逐轮确认最关键的未知事项，在满足完成门槛后输出结构化开发需求，并在用户确认前避免修改代码或外部状态。

## License

本仓库当前未包含独立的 `LICENSE` 文件。正式发布开源版本前，建议补充明确的开源协议。
