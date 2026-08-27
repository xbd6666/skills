---
name: project-conventions
description: 审查、提案、创建或更新仓库中的 CONVENTIONS.md 与 AGENTS.md。用于依据真实仓库证据建立项目规则、目录职责、文档布局和父子约定继承关系；不用于修改生产代码或替代产品需求与实施计划。
---

# 项目约定

## 目标

为具体仓库生成简洁、可执行、可追溯的 `CONVENTIONS.md` 与 `AGENTS.md`，帮助人类和编码代理保持目录职责、架构边界、公共契约、命名、测试、工具和安全要求的一致性。

核心原则：先识别仓库真实形态，再为该形态建立约定。不要把所有项目强行解释为分层架构，也不要把偶然实现直接升级为团队规范。

## 中文模式

三种公开模式统一使用中文名称：

| 模式 | 适用请求 | 是否写入文件 |
| --- | --- | --- |
| `审查` | 检查既有约定、冲突、缺口、漂移或可维护性问题 | 否 |
| `提案` | 设计文档布局、规则范围和准确的变更清单 | 否 |
| `应用` | 按已解析范围创建或最小更新约定文档 | 仅写入已解析的约定文档 |

模式名称不得再用英文替代。模式选择、已有文档策略和范围扩张检查见 [references/request-contract.md](references/request-contract.md)；每次使用本技能都先读取该文件。

## 不可破坏的边界

- `审查`和`提案`只能进行无副作用的只读发现，不创建文件，不运行会生成产物、访问数据库、调用真实外部服务或改变环境状态的命令。
- `应用`只授权创建或更新已经解析的 `CONVENTIONS.md`、`AGENTS.md` 及用户明确点名的约定类文档，不授权修改生产代码、测试代码、Git 状态、数据库、部署或外部系统。
- 读取配置或记录证据时不得输出密钥、令牌、连接字符串、个人信息或生产敏感地址；证据只保留必要路径、字段名和脱敏结论。
- 现有文档与代码冲突时，不得默认文档过时，也不得默认代码违规；先把冲突列为未决事项。
- `CONVENTIONS.md` 承载本技能新增或维护的长期项目约定；既有专业工程文档继续维护各自的权威主题。`AGENTS.md` 是短执行入口，无权创建或覆盖长期规则。
- 保留工作区中的无关改动。若目标约定文档已有未归属的修改，先报告重叠，不覆盖用户工作。

## 工作流

1. 读取 [references/request-contract.md](references/request-contract.md)，记录用户已经明确的字段和需要通过发现推断的字段。
2. 按 [references/discovery.md](references/discovery.md) 做有边界的证据发现，区分明确规则、强制配置、重复惯例、例外、冲突和假设。
3. 发现完成后最终解析模式、范围、布局、已有文档策略和输出语言。
4. 如果存在父子目录文档、模块级布局或规则冲突，读取 [references/inheritance.md](references/inheritance.md)。
5. 选择与模式对应的输出：
   - `审查`：使用 [assets/审查报告.template.md](assets/审查报告.template.md) 的结构，只报告问题与证据。
   - `提案`：使用 [assets/提案报告.template.md](assets/提案报告.template.md) 的结构，给出准确文件清单和变更摘要后停止。
   - `应用`：根据根级或模块级角色选择模板，实施最小、证据充分的文档变更。
6. 按 [references/verification.md](references/verification.md) 完成模式对应的验证和交付说明。

## 文件角色与模板

| 文件 | 角色 | 模板 |
| --- | --- | --- |
| 根级 `CONVENTIONS.md` | 仓库级边界、包或模块地图、共享命令、公共规则和文档权威关系 | [assets/根级.CONVENTIONS.template.md](assets/根级.CONVENTIONS.template.md) |
| 模块级 `CONVENTIONS.md` | 模块职责、局部扩展、显式覆盖、公共契约和验证方式 | [assets/模块.CONVENTIONS.template.md](assets/模块.CONVENTIONS.template.md) |
| 根级 `AGENTS.md` | 代理第一入口、根规则入口和模块文档路由 | [assets/根级.AGENTS.template.md](assets/根级.AGENTS.template.md) |
| 模块级 `AGENTS.md` | 当前模块的规则读取顺序和高风险执行护栏 | [assets/模块.AGENTS.template.md](assets/模块.AGENTS.template.md) |

模板只是结构引导，不是必须填满的表单。删除不适用章节，不得用泛化内容或 `TODO`、`TBD`、空占位符凑齐模板。输出语言不是中文时，翻译模板中的所有固定文本，不得生成中英混杂文档。

## 文档内容原则

- 每条规则应能回答“把什么放在哪里”“允许或禁止什么”“新增能力按什么步骤”“如何验证”。
- 规则必须指向真实目录、文件、类型、命令、配置或风险；没有证据时省略，或明确列为待确认事项。
- 不重复 `README.md`、`CONTRIBUTING.md`、`ARCHITECTURE.md`、`.editorconfig`、`CODEOWNERS` 等文档已经权威维护的内容；在 `CONVENTIONS.md` 中标明权威来源并链接。
- `AGENTS.md` 保持短小，只路由适用规则、说明模块职责并强调高风险执行护栏。长期规则发生变化时，更新 `CONVENTIONS.md`。
- 混合仓库可以同时具有多个项目原型；只为风险高、职责独立或公共契约明显不同的模块创建局部文档。

## 完成条件

交付前必须说明最终请求契约、检查覆盖范围、实际或计划文件、验证结果、未决冲突，以及没有获得授权的验证或外部操作。只有`应用`模式可以报告文件已变更。
