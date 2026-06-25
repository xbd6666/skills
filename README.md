# skills

skills 是一个开源的 Codex Skills 项目，用于沉淀、组织和推荐可复用的 AI 编程助手技能。

项目通过标准化的 Skill 描述、模板和 Agent 配置，帮助开发者在不同代码仓库中快速选择合适的技能，让 AI 助手更准确地理解项目结构、生成约定文档，并遵循团队的工程规范。

## 项目用途

- 推荐适合当前任务的 Codex Skill
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

`dbx-mcp` 是一个通用数据库操作 Skill，用于在 Codex 中规范使用 DBX MCP。

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

## 目录结构

```text
.
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

将本仓库中的 skill 安装或复制到你的 Codex Skills 目录后，即可在支持 Skills 的 Codex 环境中调用。

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

## License

本仓库当前未包含独立的 `LICENSE` 文件。正式发布开源版本前，建议补充明确的开源协议。
