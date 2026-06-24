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

## 目录结构

```text
.
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

## Skill 设计原则

- 先理解项目，再生成文档
- 不强行套用固定架构模型
- 输出内容必须贴合真实目录、命令和文件
- `CONVENTIONS.md` 保存稳定规则
- `AGENTS.md` 保持简短，只保留 Agent 修改代码前必须知道的高风险约束
- 避免生成空泛、通用、无法执行的工程建议

## 贡献方向

欢迎继续补充更多可复用 Skill，例如：

- API 设计规范推荐
- 测试策略推荐
- 前端组件开发规范
- 数据库迁移规范
- 安全审查流程
- 文档生成与维护流程

新增 Skill 时建议保持统一结构：

```text
skill-name/
├── SKILL.md
├── agents/
└── assets/
```

## License

本仓库当前未包含独立的 `LICENSE` 文件。正式发布开源版本前，建议补充明确的开源协议。
