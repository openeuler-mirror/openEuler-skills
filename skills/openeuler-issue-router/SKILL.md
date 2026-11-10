---
name: openeuler-issue-router
description: 当用户要提交或咨询 openEuler 的 issue、bug、版本升级、RPM 包问题、源码包问题或应用镜像问题时使用，负责把问题路由到 AtomGit 上的正确仓库。
category: others
license: MulanPSL-2.0
---

# openEuler Issue 路由

## 速度要求

用户只问“提哪里/哪个仓库”时，直接按下面的分类给出 AtomGit 地址，不要先爬多个页面。

- 默认最多做 1–2 次验证
- 只有用户要求写完整 issue 时，才整理复现步骤和正文
- 已知固定路由时直接回答

## 目标

先判断问题属于 RPM/源码包还是应用镜像，再找到 AtomGit 上的正确 issue 入口，避免把用户带到迁移前的 Gitee 仓库。

## 分类

### RPM、源码包、构建、依赖、版本升级

去 src-openeuler 对应包仓库：

```text
https://atomgit.com/src-openeuler/<包名>/issues
```

示例：

```text
https://atomgit.com/src-openeuler/nginx/issues
```

如果无法确定包名，先搜索 `https://atomgit.com/src-openeuler`，不要猜一个不存在的仓库。

### 应用镜像、容器镜像

去：

```text
https://atomgit.com/openeuler/openeuler-docker-images/issues
```

适用于：

- openEuler 官方应用镜像
- 镜像 tag、架构、启动参数、镜像内依赖问题
- 镜像缺失、版本落后、Dockerfile 或构建问题

### 无法判断

先问用户一句：

```text
这是 RPM/源码包问题，还是应用/容器镜像问题？
```

不要默认路由到 Gitee，也不要随便选一个仓库。

## 迁移规则

如果搜索结果出现：

- `gitee.com/openeuler/...`
- `gitee.com/src-openeuler/...`

识别为迁移前旧链接，改用 AtomGit 对应地址。

## 提交前检查

1. 先在目标仓库搜索是否已有相同 issue
2. 确认系统版本、架构、软件版本、镜像 tag/digest
3. 准备复现步骤、实际结果、期望结果、关键日志
4. 删除 token、密码、私钥和内部地址
5. 提交前向用户确认标题和正文

## Issue 草稿格式

```text
标题：[包名/镜像名] 版本/架构：问题摘要

环境：
- openEuler 版本：
- 架构：
- 软件/镜像版本：
- 安装方式：

复现步骤：
1.
2.

实际结果：

期望结果：

日志：
```

## 输出

给用户：

- 正确的 AtomGit 仓库地址
- 建议的 issue 标题
- 完整正文草稿
- 是否有重复 issue
- 需要用户补充的信息

## 禁止

- 不把 Gitee 作为有效提交入口
- 不在没有确认的情况下提交 issue
- 不把密钥、token 或内部凭据写进 issue
- 不把 RPM 问题发到镜像仓库，也不把镜像问题发到 src-openeuler 包仓库
