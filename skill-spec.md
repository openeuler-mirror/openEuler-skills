# SKILL.md 编写规范

本规范定义 openEuler-skills 仓库中 Skill 原文件（`SKILL.md`）的编写标准，包括目录布局、frontmatter 字段要求和本仓库需要的常见扩展数据。

扫描器依据本规范发现和解析 Skill：不符合规范的 `SKILL.md` 会被跳过。

## 1. 目录结构

一个 Skill 是一个目录，目录名即 Skill 的展示标识：

```
<skill-name>/
├── SKILL.md          # 必填：Skill 入口文件，文件名大小写敏感，只识别 SKILL.md
├── references/       # 可选：参考文档
│   └── *.md
├── scripts/          # 可选：可执行脚本
│   └── *.sh / *.py
└── assets/           # 可选：其他资源文件
```

规则：

- 文件名必须是 `SKILL.md`（大小写敏感，`skill.md`、`Skill.md` 均无效）。
- 一个 Skill 目录只包含一个 `SKILL.md`。
- 资源文件不主动加载，由 `SKILL.md` 正文中的指令按需引用。

## 2. 文件格式

`SKILL.md` 由两部分组成：YAML frontmatter（元数据）+ Markdown 正文（指令内容）。

```markdown
---
name: skill-name
description: 一句话说明该 Skill 的用途和使用时机
category: Development and Build
---

# Skill 标题

正文使用 Markdown 编写，描述 Agent 执行该 Skill 时需要遵循的指令…
```

### 2.1 Frontmatter 字段

| 字段 | 必填 | 类型 | 说明 |
| --- | --- | --- | --- |
| `name` | 必填 | string | Skill 名称；小写字母、数字、连字符（`-`），建议与目录名一致 |
| `description` | 必填 | string | Skill 用途简述；建议一句话，说明"做什么"和"何时用" |
| `category` | 建议 | string | Skill 所属分类；取值必须来自 [`templates/skill-spec.yaml`](templates/skill-spec.yaml) 的 `categories` |
| `license` | 可选 | string | 许可证标识（SPDX），如 `Apache-2.0`、`MIT` |
| `version` | 可选 | string | Skill 版本号，建议语义化版本（如 `1.0.0`） |
| `metadata` | 可选 | object | 扩展数据容器，见 2.2 |
| `allowed-tools` | 可选 | list | 允许该 Skill 使用的工具列表（兼容业界 Agent Skills 格式） |

缺 `name` 或 `description`、或二者类型不是 string 的 `SKILL.md` 会被扫描器直接跳过。

### 2.2 metadata 扩展数据

本仓库需要的其他常见数据统一放在 `metadata` 对象下：

```yaml
metadata:
  homepage: https://example.com/skill-docs
  repository: https://github.com/example/skills
```

| 字段 | 必填 | 说明 |
| --- | --- | --- |
| `metadata.homepage` | 可选 | Skill 文档或介绍页 |
| `metadata.repository` | 可选 | Skill 源码仓库地址 |

## 3. 分类

分类取值定义在 [`templates/skill-spec.yaml`](templates/skill-spec.yaml)，共 9 类：

| 分类 | 适用场景 |
| --- | --- |
| Research and Design | 需求调研、方案设计、架构评估 |
| Development and Build | 编码开发、构建打包 |
| Engineering and Compilation | 工程化、编译优化 |
| Quality and Validation | 测试、质量门禁、合规检查 |
| Release and Deployment | 版本发布、部署上线 |
| Monitoring and Operations | 监控告警、日常运维 |
| Performance Optimization | 性能分析与调优 |
| Security Hardening | 安全加固、漏洞扫描 |
| others | 无法归入以上分类的 Skill |

## 4. 编写建议

- `description` 写清楚"做什么 + 何时用"，扫描器和用户主要依赖它判断 Skill 用途；建议不超过两句话。
- 正文直接写给 Agent 的指令，使用祈使句；复杂流程分步骤，示例放代码块。
- 需要引用资源文件时，给出相对路径（如 `references/api.md`）。
- frontmatter 与正文均使用 UTF-8 编码，YAML 统一 2 个空格缩进，不要使用 Tab。

## 5. 完整示例

```markdown
---
name: rpm-build
description: 在 openEuler 环境中构建 RPM 软件包；当用户需要从 SPEC 文件构建或本地调试软件包时使用。
category: Engineering and Compilation
license: Apache-2.0
version: 1.0.0
metadata:
  repository: https://gitcode.com/openeuler/example
---

# RPM 构建

按以下步骤构建 RPM 包：

1. 安装构建依赖
2. 执行 rpmbuild 构建
3. 校验产物完整性

详细参数说明见 `references/rpmbuild.md`。
```
