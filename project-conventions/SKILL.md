---
name: project-conventions
description: Use when creating or updating project convention docs such as CONVENTIONS.md or AGENTS.md for any repo type, including frontend apps, backend services, libraries, CLIs, data or ML projects, monorepos, layered architectures, module boundaries, coding rules, dependency directions, or AI coding-agent instructions.
---

# Project Conventions

## Overview

Create concise, project-specific `CONVENTIONS.md` and `AGENTS.md` files that help both humans and coding agents preserve architecture boundaries, ownership rules, folder taxonomy, naming, testing, tooling, and implementation habits.

Core rule: infer the project's shape first, then write conventions for that shape. Do not force every project into a layered architecture model.

## Workflow

1. Inspect the repository before writing:
   - list top-level folders and project files
   - read existing `README.md`, `CONVENTIONS.md`, `AGENTS.md`, package manifests, build files, config files, and docs
   - identify languages, frameworks, entrypoints, tests, generated outputs, deployment surfaces, and dependency directions
2. Classify the project or module archetype from evidence.
3. Choose the convention dimensions that matter for that archetype.
4. Propose the convention-file layout and let the user choose before creating new files, unless the user already specified the layout or the task is only updating existing docs in place.
5. Generate or update `CONVENTIONS.md` for durable human/project rules.
6. Generate or update `AGENTS.md` as the short agent-facing entrypoint for that scope.
7. Re-read the generated docs and check for conflicts, stale paths, vague placeholders, and boundary leakage.

When existing convention docs are present, preserve their tone, language, section style, and naming choices unless the user asks for a rewrite.

## File Roles

| File | Purpose | Typical length |
| --- | --- | --- |
| `CONVENTIONS.md` | Durable rules for project/module organization, responsibilities, dependency direction, naming, comments, tests, tooling, and standard steps. | Medium |
| `AGENTS.md` | Short instructions that coding agents should read before editing that scope. Usually points to `CONVENTIONS.md` and repeats only the highest-risk boundaries. | Short |

`AGENTS.md` should not duplicate every rule from `CONVENTIONS.md`. It should make the agent do the right first thing and avoid the most expensive mistakes.

## Archetype Detection

Use actual repo evidence first. These archetypes are starting points, not boxes to force:

| Evidence | Likely archetype | Main convention emphasis |
| --- | --- | --- |
| `src/components`, `pages`, `app`, `vite`, `next`, `react`, `vue`, `svelte` | Frontend app | routes, components, state, API clients, styles, assets, accessibility, visual QA |
| `controllers`, `routes`, `middleware`, `server`, `Program.cs`, `appsettings`, `OpenAPI` | Backend/API service | endpoints, request/response contracts, auth, validation, services, config, observability |
| `application`, `domain`, `infrastructure`, `framework`, `web`, `api` | Layered architecture | dependency direction, layer responsibilities, ports/adapters, DTOs, persistence boundaries |
| `packages/*`, `apps/*`, `libs/*`, workspace manifests | Monorepo | package ownership, shared code, build/test commands, cross-package dependencies, release boundaries |
| `src`, `lib`, public exports, package manifest, no app entrypoint | Library/package | public API surface, compatibility, examples, tests, versioning, dependency policy |
| `cli`, `commands`, `bin`, argument parser, terminal output | CLI/tooling project | commands, flags, config, stdout/stderr, exit codes, idempotency, tests |
| `notebooks`, `data`, `models`, `pipelines`, `features` | Data/ML project | data provenance, pipeline stages, experiments, artifacts, reproducibility, secrets |
| `infra`, `terraform`, `helm`, `k8s`, `docker`, workflows | Infrastructure/DevOps | environments, state, secrets, rollout safety, generated files, validation commands |
| `mobile`, `android`, `ios`, `react-native`, `flutter` | Mobile app | screens, navigation, platform folders, assets, permissions, release config |
| `docs`, `content`, `site`, `mkdocs`, `docusaurus` | Docs/content site | content structure, frontmatter, assets, publishing, link checks |

For hybrid repos, write repo-level conventions plus targeted module-level conventions. Do not pretend the whole repo has one architecture if it clearly has several.

## Convention Dimensions

Select only relevant dimensions:

| Dimension | Include when |
| --- | --- |
| Scope and ownership | The repo has modules, packages, layers, teams, or repeated folder patterns |
| Folder organization | New files need predictable placement |
| Boundary rules | Code can easily leak across layers, packages, runtime surfaces, or ownership zones |
| Public contracts | APIs, components, CLI commands, schemas, package exports, or file formats are user-facing |
| Data and persistence | The project touches databases, files, datasets, migrations, generated records, or durable state |
| Config and secrets | Runtime behavior depends on config, env vars, keys, credentials, or deployment targets |
| Testing and verification | There are known test commands, visual QA, snapshots, type checks, or build gates |
| Generated artifacts | Build outputs, migrations, clients, docs, or model artifacts should not be hand-edited |
| Style and comments | The repo has strong language, syntax, naming, formatting, or documentation preferences |
| Security and privacy | Auth, authorization, PII, secrets, tenant boundaries, or unsafe inputs are present |

## CONVENTIONS.md Content

Include sections useful for the specific project or module:

- project/module purpose
- folder organization with a short tree
- responsibility and ownership boundaries
- dependency direction or allowed imports when relevant
- naming, namespace, export, route, command, or file conventions
- common type/component/service patterns
- standard steps for adding a new feature in that scope
- config, data, persistence, security, serialization, or deployment rules when relevant
- test and verification commands when stable
- coding syntax preferences when the repo already has them
- comment/documentation requirements when the repo expects them

Avoid:

- generic style advice that could apply to any repo
- long architecture theory
- repeating implementation details that will drift quickly
- rules for folders outside the file's scope unless they define a boundary
- placeholders such as `TODO`, `TBD`, `{Feature}` unless they are part of an intentional pattern example
- forcing layer words like "application", "domain", or "infrastructure" onto projects that do not use them

Use `assets/CONVENTIONS.template.md` only as a shape guide. Replace placeholders with repository-specific content.

## AGENTS.md Content

Every scoped `AGENTS.md` should usually contain:

1. `适用范围` or `Scope`
2. `工作前必读` or `Before Editing`
3. a short project/module responsibility section
4. the most important do/don't boundaries
5. any high-risk local requirements, such as route authorization, generated-file rules, public API compatibility, database annotations, or visual QA

Default rule:

```markdown
修改本目录下任何文件前，先阅读并遵守同目录的 `CONVENTIONS.md`。
```

If `AGENTS.md` and `CONVENTIONS.md` conflict, make `CONVENTIONS.md` authoritative unless the user explicitly wants agent-only overrides.

Use `assets/AGENTS.template.md` only as a shape guide. Keep the final file short.

## Root vs Module Strategy

- Root `CONVENTIONS.md`: use for repo-wide workflow, package map, dependency policy, shared commands, generated artifacts, and cross-cutting style.
- Module `CONVENTIONS.md`: use when a subfolder has its own role, public contract, or contribution rules.
- Root `AGENTS.md`: use as the first agent entrypoint; it should route agents to relevant module docs.
- Module `AGENTS.md`: use when mistakes in that folder are expensive and local rules must be visible before edits.

Before creating new convention docs, recommend one layout from evidence and ask the user to choose:

| Choice | Use when | Creates |
| --- | --- | --- |
| Root only | Small or single-purpose repos | root `CONVENTIONS.md` and root `AGENTS.md` |
| Root plus modules | Monorepos, hybrid repos, layered systems, or multiple apps/packages | root docs plus targeted module docs |
| Module only | The user targets one subfolder or wants local rules without repo-wide policy | scoped `CONVENTIONS.md` and `AGENTS.md` in that folder |

If one layout is clearly best, mark it as recommended. If the user asks to proceed without confirmation, apply the recommended layout and state the assumption.

For small repos, one root `CONVENTIONS.md` plus one root `AGENTS.md` is enough.

## Output Style

- Match the repository's existing language. If existing docs are Chinese, write Chinese.
- Prefer concrete folder names, type names, commands, and config names from the repo.
- Keep rules actionable: "put X in Y", "do not edit X by hand", "new A follows steps 1-5".
- Use Markdown headings and bullets, not prose walls.
- Use code blocks for folder trees and short examples.
- For C#/.NET projects that already use XML comments, preserve Chinese XML comment requirements when present.

## Common Mistakes

| Mistake | Fix |
| --- | --- |
| Treating every repo as layered architecture | Identify the actual archetype first |
| Writing generic best practices | Anchor each rule to a folder, command, framework, or risk in the repo |
| Making `AGENTS.md` too long | Put durable detail in `CONVENTIONS.md`; keep `AGENTS.md` as the entrypoint |
| Duplicating stale implementation details | Describe stable boundaries and extension points |
| Ignoring existing style | Match existing language, tone, and section structure |

## Verification

Before finishing:

- list generated or changed files
- scan for leftover placeholders
- confirm every referenced path exists or is intentionally illustrative
- confirm each `AGENTS.md` points to the correct `CONVENTIONS.md`
- confirm the docs do not force an archetype contradicted by the repo
- state that no build/tests were run when only documentation changed
