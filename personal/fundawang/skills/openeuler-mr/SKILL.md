---
name: openeuler-mr
description: 创建、更新、修改 openEuler(src-openeuler) 仓库的 PR/MR 时必须先加载本技能——凡涉及 src-openeuler 的 fork/clone/spec 改动/升级/提交/推送/建 MR/改 PR 正文/改 commit message 的任务都适用。提供官方 MR 正文模板逐字原文、AI 参与披露与 commit trailer 一致性要求、commit message 格式、验证与汇报规范。
category: Development and Build
---

# openEuler (src-openeuler) PR/MR 规范

**动手前先读完整篇。以下是硬性规则，违反过一次的都在文末列了。**

## 1. PR 正文必须逐字使用官方模板

不允许自创结构（"Commit 分组""变更摘要"这类自编小节都是错的）。模板原文：

```
<!-- 感谢您的合入申请! -->

### 当前PR是否有AI参与:   
[x]  否     

[ ]  Yes   
__1.  AI Agent 平台:    
__2.  AI 模型 :  
__3.  Prompt 上下文 : 

参考：openEuler社区[《生成式AI工具使用与开源贡献政策》](https://www.openeuler.org/zh/community/aigc/)

### PR功能描述 / 为什么需要这个合入**:  
     
       
     
### 该PR关联的issue   
*(格式为fixes #<issue号>, 或者resolves #<issue号>)*: fixes #


   
### 希望检视人员了解:
```

四个小节标题一字不改，包括 `### PR功能描述 / 为什么需要这个合入**:` 结尾那两个字符也是模板自带的。

**AI 参与时**把勾选对调，并逐行填 AI 披露：

```
[ ]  否

[x]  Yes
__1.  AI Agent 平台:    AtomCode
__2.  AI 模型 :  <当前模型名>
__3.  Prompt 上下文 :  <一句话说明这次让 AI 做了什么，含仓库/分支/版本/动作>
```

### 填写规则

- 内容行**顶格写，不要前导空格**：Markdown 会把 4 空格缩进渲染成代码块，导致模板显示错乱。
- 模板自带的空行/留白保持原样，只替换该替换的地方。
- `### 该PR关联的issue` 一节：有关联 issue 就写 `fixes #<号>` / `resolves #<号>`；没有就保留模板原样 `fixes #`。不要擅自编一个 issue 号。

## 2. 模型名：以实测为准，且必须与 commit trailer 一致

- 先跑 `describe_self(aspect="models")` 取**本会话实际在跑的**模型名，不要凭记忆写（模型会随 `/model` 切换）。
- 门禁会校验 commit 里的 `Co-Authored-By` 模型名与 PR 正文披露的"AI 模型"一致，两者必须对齐。
- trailer 写法：`Co-Authored-By: AtomCode (<当前模型名>) <noreply@atomgit.com>`

## 3. commit message 格式

升级/更新类提交（用户已纠正过多次）：

```
Update package to version X.Y.Z

- 要点一
- 要点二

Co-Authored-By: AtomCode (<当前模型名>) <noreply@atomgit.com>
```

**标题不要裸写版本号**（写 `0.8.9` 是错的，即使上游仓库历史提交长这样也不行）。正文列要点，空行后接 trailer。

## 4. 建完必须验证，不能只发请求

`atomgit_pr action=update` / `create` 有时返回 `failed to parse AtomGit response`——**这不代表失败**，必须实际核对：

```bash
# 用 atomgit_api GET /repos/{owner}/{repo}/pulls/{n}
# 检查 body 是否为新内容、head.sha 是否指向最新提交、labels 是否挂上
```

门禁通过后会挂 `ai-co-authored`、`ai-includes-code`、`ai-compliance-successful`（还有 `openeuler-cla/yes`）。

## 5. 汇报规范

只说**自己做了什么**：改了哪些文件、推了哪个分支和 commit、开了哪个 PR、验证了什么。

不要写"本机没有 XX 工具链""无法本地构建""WSL 无发行版"这类环境欠缺说明；PR 正文里也不要出现环境限制段落。

## 6. 改 PR 分支历史的纪律

改写已推送分支（`--force-with-lease`）属于需批准的操作：先把本地改好，向用户说明为什么要改写、影响面多大，得到同意再推。

**`git commit --amend` 只提交索引内容**：改完文件必须先 `git add`，否则 amend 只换了提交消息，内容还是旧的（libgxps 踩过：路径化依赖和 man page 改动没进提交就推了）。amend 后用 `git show HEAD:<file>` 核对，推送后再用 `git show origin/<branch>:<file>` 复核一遍，别只看 `--stat`。

## 7. 构建依赖按源码实际声明改写，不要靠包名猜

改 `BuildRequires` 时以源码构建脚本为准，逐条对应，**源码里有什么就写什么**：

- `dependency('x', version: '>= a.b.c')`（meson）→ 写 `pkgconfig(x) >= a.b.c`。版本下限照抄源码声明的那一个（多个依赖共用同一个变量时照抄该变量值），不自己编也不省略。
- `find_program('prog')`：只是找程序、源码里没有对应 pkgconfig 模块的，写文件路径 `BuildRequires: /usr/bin/prog`，**不写包名**。例：`/usr/bin/g-ir-scanner`、`/usr/bin/g-ir-compiler`（meson `gnome.generate_gir` 生成 typelib 时要用，源码里不会显式 `find_program`，但装上 typelib 就必须有）、`/usr/bin/xsltproc`。
- 源码里 `required: false`、但**决定某个产物是否被构建**的依赖仍要显式列，不能因为"某个 devel 包会顺带提供"就省略。例：libgxps 的 `cairo-pdf`/`cairo-ps`/`cairo-svg` 决定 `xpstopdf`/`xpstops`/`xpstosvg` 是否构建，`libpng` 决定 `xpstopng`。
- 一次需要多个可执行文件的工具链（`gtk-doc` 这类）继续按包名写，不要拆成路径。
- `%files` 里的通配不要硬编码可选组件或压缩后缀：`%{_bindir}/xpsto*`、`%{_mandir}/man1/*.1*`（不要写 `.1.gz`）。
- License 更新为 SPDX 前先扫描源码取证：`COPYING` 的版本 + 各源文件头的 "either version X of the License, or (at your option) any later version" 决定 `-or-later`，`LGPLv2+` 这类旧写法要换成 `LGPL-2.1-or-later`。
- 废弃的 `%defattr` 直接删掉。

## 8. 多分支复用：先比 tree hash，再问用户要不要 /sync

同一个 CVE/修复往往要落到多个分支。**不要**逐分支搭基线试打补丁来验证可复用性——先比 tree hash：

```bash
git rev-parse origin/<branch>^{tree}      # 取 src-openeuler 上游分支，不是 fork
```

- tree 相同 = 工作树逐字节一致，结论等价于"按 spec 顺序搭基线 + `--fuzz=0` 逐个试打"，但省事得多；
- 对 `%autosetup`/`%prep` 层面是**充分条件**，连 spec 的 `Release` 号也一并覆盖（可据此判断 `Release` 递增是否原样成立，不必逐分支核对）；
- tree 包含 `.gitattributes`，所以若源码包走 LFS，tree 里只有指针，需另行确认内容；
- tree 不含提交历史（不同 commit 可以是同一个 tree，这不影响结论）。

**一旦发现较早分支与当前分支 tree 相同，就主动问用户是否需要同步到那些分支**——不要自己决定，也不要漏掉这一步。用户同意后，**直接在当前 PR 上评论 `/sync`**，一条命令把所有目标分支列全：

```
/sync <branch1> <branch2> <branch3>
```

规则与注意点：

- 仅**最后一个** `/sync` 命令生效，所以目标分支必须在同一条命令里列全。
- PR 处于 Open 时，同步**延迟到该 PR 被合入时**才执行；只有已 Merged 才立即执行。
- 因此是**两段式**：源 PR 合入 → 机器人为各目标分支创建同步 PR → 那些同步 PR 仍需各自评审合入。源 PR 合入 ≠ 早分支已修复。
- 发完可用 PR 评论接口（`atomgit_pr action=comment_view`）核对机器人回帖：它会给出目标分支表与"当前 PR 合并后，将创建同步 PR"，并可读到该仓库的保护分支表。
- tree **不同**的分支（版本不一致）不能靠 sync，必须单独提 MR（每分支各自改 `Version`/`Release`/`Source0`/changelog）。
