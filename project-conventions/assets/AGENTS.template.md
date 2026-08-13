# AGENTS.md

## 适用范围

本文件适用于 `{ScopePath}` 下的所有文件。

## 规则读取顺序

修改前，按从仓库根目录到本目录的顺序阅读：{ConventionDocumentChain}。

- 上级 CONVENTIONS.md 默认适用于本范围。
- 同目录 CONVENTIONS.md 与本文件冲突时，以 CONVENTIONS.md 为准。
- 本文件仅补充本地执行规则；明确覆盖的上级规则为：{ExplicitOverridesOrNone}。

## 工作前必读

修改本目录下任何文件前，先阅读并遵守规则读取顺序中的所有约定。无法确认规则冲突是否为显式覆盖时，先报告冲突，不要自行判断。

## 项目/模块职责

`{ScopeName}` 是 `{ScopeRole}`，负责 `{PrimaryResponsibility}`。

- `{DoRuleOne}`
- `{DoRuleTwo}`
- 不要 `{DontRuleOne}`
- 不要 `{DontRuleTwo}`

## 高风险规则

- `{RiskRuleOne}`
- `{RiskRuleTwo}`

## 验证要求

- `{VerificationRule}`
