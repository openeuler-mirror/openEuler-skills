---
name: openeuler-software-finder
description: 当用户要查找 openEuler 软件、RPM 包、源码包、制品或官方容器镜像，询问软件中心、镜像仓库、src-openeuler 或 repo.openeuler.org 时使用。
category: others
license: MulanPSL-2.0
---

# openEuler 软件与镜像定位

## 速度要求

用户问“在哪里找/入口是什么”时，直接用下面的固定入口回答，不要为了一个 URL 爬多个页面。

- 默认最多做 1–2 次验证，但软件中心的三遍搜索不受此限制
- 用户没有要求具体版本、tag 或架构时，不要展开查版本
- 已知固定入口时直接给链接
- 不要重复搜索同一个地址

## 目标

给用户一个可验证的官方入口，不要凭记忆编造包名、镜像 tag 或下载地址。

## 固定入口

- 软件中心：`https://easysoftware.openeuler.org/zh/`
- 软件中心搜索：`https://easysoftware.openeuler.org/zh/search?q=<关键词>`
- 应用镜像目录：`https://easysoftware.openeuler.org/zh/image`
- 官方镜像源码仓库：`https://atomgit.com/openeuler/openeuler-docker-images`
- src-openeuler 源码/制品组织：`https://atomgit.com/src-openeuler`
- 二进制软件包仓库：`https://repo.openeuler.org/`
- 文档：`https://docs.openeuler.org/`

## 搜索顺序

1. 先判断用户要的是：软件中心里的软件、容器镜像、RPM 包、源码包，还是构建制品。
2. 软件安装和兼容性优先查软件中心。软件中心必须搜索三遍：第一遍原名，第二遍大小写/别名变体，第三遍更宽泛的关键词；三遍都未命中，才继续下一步。
3. 容器镜像优先查 `openeuler-docker-images` 和软件中心的镜像页。
4. RPM/源码/补丁优先查 `src-openeuler` 对应包仓库。
5. 下载地址查 `repo.openeuler.org` 或镜像仓库。

## 迁移规则

openEuler 仓库已经从 Gitee 迁移到 AtomGit。

如果搜索结果出现：

- `gitee.com/openeuler/...`
- `gitee.com/src-openeuler/...`

把它视为旧链接，不作为最终推荐地址。对应到：

- `atomgit.com/openeuler/...`
- `atomgit.com/src-openeuler/...`

无法确认对应关系时，先说明“这是迁移前旧链接”，不要直接让用户访问 Gitee。

## 镜像查找

- 官方镜像仓库：`atomgit.com/openeuler/openeuler-docker-images`
- 镜像清单重点看各目录的 `image-list.yml`
- 实际 tag 和架构以 Docker Hub、Quay、`hub.oepkgs.net` 或 `repo.openeuler.org` 为准
- 需要验证多架构时，检查对应 manifest，不要只看 tag 名称

## 输出格式

给用户一个简短表格：

| 类型 | 推荐入口 | 版本/架构 | 下一步 |
|---|---|---|---|

并明确说明：

- 软件中心三遍搜索的关键词和命中情况
- 推荐的是软件中心、镜像仓库还是 src-openeuler
- 是否已确认存在
- 如果没找到，缺少的是包、镜像还是版本

## 禁止

- 不把 Gitee 作为当前有效入口
- 不编造镜像 tag、包名或下载链接
- 不把“搜索结果里出现过”当成“官方已支持”
- 不跳过软件中心的三遍搜索
- 不执行安装或下载，除非用户明确要求
