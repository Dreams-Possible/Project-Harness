---
name: git-commit
description: 引导用户完成规范的团队 Git 协作流程：创建分支、同步主分支、提交、推送并创建合并请求，以及后续修改的 amend 流程
---

# git-commit 技能说明

## 使用场景

需要按规范完成分支、同步、提交、推送和合并请求流程时使用。

> **重要原则**：在引导用户完成任何 Git 操作时，如果遇到任何不确定的情况（环境异常、冲突、操作结果不符合预期）或流程被打断，必须立即询问用户，不得自作主张继续操作。

---

## 什么是合并请求

GitLab 将合并请求称为 **MR（Merge Request）**，GitHub 将其称为 **PR（Pull Request）**；两者的作用相同：

- 它是将一个开发分支（如 `feature/zhang-login`）合并进主分支（如 `master`）的**正式申请**
- 合并请求通常需要其他团队成员进行代码审查（Code Review）并批准后，才能真正合并
- 合并请求是团队协作中控制代码质量和保护主分支的核心机制

---

## 提交类型规范

| 类型 | 说明 |
|------|------|
| `feature` | 新增业务功能、界面或协议支持 |
| `fix` | 修复 Bug |
| `change` | 功能行为变更（非新增、非 Bug），包括代码格式调整 |

### 标题格式

```
<type>(<scope>): <中文简洁描述>
```

- `type`：feature / fix / change 三选一
- `scope`：发生变更的模块或组件名称（小写英文，允许连字符）
- 中文简洁描述：不超过 20 个汉字，说明做了什么

### Body 格式

```
软件版本: X.Y.Z

[feature]
1. <scope>: <中文描述>
2. <scope>: <中文描述>

[fix]
1. <scope>: <中文描述>

[change]
1. <scope>: <中文描述>
```

Body 规则：
- 软件版本行必须在 Body 第一行
- 只列出本次提交中实际有变更的类型分组
- 每个分组内的条目按模块（scope）排列
- 如本次提交只涉及一个类型，Body 可以只有一个分组

---

## 完整 Git 协作流程

### 流程 A：创建工作分支

**在开始任何代码修改之前**，必须先确认当前在正确的工作分支下。

```bash
# 创建并切换到新分支
git checkout -b <type>/<dev_id>-<commit_context>

# 确认当前所在分支（当前分支前会有 * 标记）
git branch
```

**参数说明**：
- `checkout -b`：`checkout` 是切换分支的命令，`-b`（branch 的缩写）表示"创建并立即切换"到新分支，等同于先 `git branch <name>` 再 `git checkout <name>` 两步合一
- `<type>`：分支类型，与提交类型保持一致（`feature` / `fix` / `change`）
- `<dev_id>`：开发人员代号（通常是姓名缩写或工号，小写英文）
- `<commit_context>`：本次提交的主要内容，用连字符连接的英文短语，简洁描述要做什么
- `git branch`（不带参数）：列出所有本地分支，确认当前已在新建的分支上

分支命名示例：`feature/zhang-add-login`、`fix/li-serial-crash`、`change/wang-refactor-ui`

询问用户（如果用户未告知任何信息）：
> 请提供您的开发者代号和本次工作的主要内容（英文关键词），我来帮您生成分支名。

---

### 流程 B：同步主分支

**任何推送操作前都必须执行此流程**，包括首次推送和后续的 amend 推送。

```bash
# 从远端下载最新提交记录（不修改当前分支）
git fetch origin

# 查看远端默认分支
git symbolic-ref --short refs/remotes/origin/HEAD

# 将本地分支的提交移植到最新默认分支之后
git rebase origin/<default_branch>
```

**参数说明**：
- `fetch origin`：`fetch` 从远端仓库下载最新的提交和分支信息，但**不会**自动修改当前本地分支的内容。`origin` 是远端仓库的默认别名，指向团队共享的 Git 服务器
- `symbolic-ref`：查看远端声明的默认分支；若无法确定，必须询问用户。
- `rebase origin/<default_branch>`：`rebase`（变基）将当前分支上尚未合并进默认分支的提交，重新排列到远端默认分支最新状态之后，使提交历史保持线性。

> 如果 rebase 过程中出现冲突，git 会暂停并提示冲突文件——**遇到此情况立即告知用户，不要自行解决冲突（除非用户明确要求）**。

---

### 流程 C：修改代码

在当前分支下完成代码修改。分支上允许有多次本地提交，无需每次改动都合并为一条。

---

### 流程 D：提交代码

按本技能的"提交消息构造流程"（见下方）完成代码暂存和提交。

---

### 流程 E：推送并创建合并请求

```bash
# 推送本地分支到远端，并建立追踪关系
git push -u origin <branch_name>
```

**参数说明**：
- `push`：将本地提交上传到远端仓库
- `-u`（`--set-upstream` 的缩写）：将本地分支与远端同名分支建立追踪关联。建立后，以后在该分支下直接执行 `git push` 即可，无需再每次都写 `origin <branch_name>`
- `origin`：远端仓库别名
- `<branch_name>`：要推送的分支名（填写流程 A 中创建的分支名）

推送成功后，根据远端服务创建合并请求，目标分支使用项目的默认分支。

#### GitLab：创建 MR

可以前往 GitLab 网页手动创建 MR；也可以在推送时附加参数自动创建 MR：

```bash
git push -u origin <branch_name> \
  -o merge_request.create \
  -o merge_request.target=<default_branch>
```

**额外参数说明**：
- `-o`（`--push-option` 的缩写）：向远端服务器传递自定义选项，这些选项由服务器端（GitLab）识别和处理，git 本身不解析
- `merge_request.create`：告诉 GitLab 在推送完成后自动创建一个新的 MR
- `merge_request.target=<default_branch>`：指定 MR 的目标合并分支为项目默认分支

#### GitHub：创建 PR

推送成功后，前往 GitHub 网页创建 PR。若本地已安装并登录 GitHub CLI，也可以执行：

```bash
gh pr create --base <default_branch> --head <branch_name>
```

询问用户：
> 您使用 GitLab 还是 GitHub？GitLab 是否需要自动创建 MR；GitHub 是否使用网页或 GitHub CLI 创建 PR？

---

### 流程 F：合并请求已提交后又修改了代码（amend 流程）

如果合并请求已经创建，又有新的代码修改需要补充进同一次提交：

**第一步：同步主分支（必须，过程与流程 B 完全相同）**

```bash
git fetch origin
git rebase origin/<default_branch>
```

**第二步：暂存新的修改**

```bash
git add .
```

**第三步：修改最近一次提交**

```bash
# 如果提交消息也需要同步修改（会打开编辑器）
git commit --amend

# 如果提交消息不变，只更新代码内容
git commit --amend --no-edit
```

**参数说明**：
- `commit --amend`：修改最近一次提交，将当前暂存区的内容合并进去，并允许编辑提交消息。**这不会创建新提交，而是替换掉最近那条提交**
- `--no-edit`：跳过编辑提交消息的步骤，直接保留原来的提交消息

**第四步：强制更新远端分支**

```bash
git push --force-with-lease
```

**参数说明**：
- `push --force-with-lease`：强制推送（覆盖远端分支历史），用于推送 amend 后的提交。比 `--force` 更安全：如果远端分支上存在其他人新推送的提交（即本地未拉取的内容），`--force-with-lease` 会拒绝推送并报错，防止误覆盖他人工作

**注意事项**：
- 强制推送后，GitLab 的 MR 或 GitHub 的 PR 可能需要刷新页面才能显示最新内容
- 分支上允许保留多次本地提交，最终合并时是保留全部提交、压缩为一次（squash）还是 rebase 线性合并，取决于合并请求管理者的决定，不由提交者控制

---

## 提交消息构造流程

（对应完整流程中的流程 D）

### 步骤 1：查看当前变更

```bash
git diff --staged
```

如果暂存区为空，向用户提示：

> 暂存区没有变更。请先执行 `git add <文件>` 将要提交的文件加入暂存区，再重新开始提交流程。

然后**终止执行**，不再继续后续步骤。

### 步骤 2：询问必要信息

向用户询问：

1. **软件版本号**：本次提交对应的软件版本，格式为 `X.Y.Z`
2. **变更分类**（如果 diff 内容涉及多个模块或变更较复杂）：逐条询问每个变更属于哪种类型（feature / fix / change）以及所属的 scope

### 步骤 3：辅助分类

根据 diff 内容主动建议类型和 scope，供用户确认或修改：

- 新增了原来没有的函数、类、接口、页面、协议 → 建议 `feature`
- 修复了原有功能的错误行为（与预期不符） → 建议 `fix`
- 修改了原有功能的行为但不是 Bug（重构、改参数、调整格式、优化逻辑） → 建议 `change`

scope 建议：优先使用目录名或模块名（小写英文）；多个相关文件属于同一模块时，用该模块名作为统一 scope。

### 步骤 4：构造提交消息

**情况 A：只涉及一种类型**

```
<type>(<scope>): <中文简洁描述>

软件版本: X.Y.Z

[<type>]
1. <scope>: <中文描述>
```

**情况 B：涉及多种类型**

标题使用变更数量最多的那种类型；Body 按所有涉及的类型分组列出：

```
<主要type>(<主要scope>): <中文简洁描述>

软件版本: X.Y.Z

[feature]
1. <scope>: <中文描述>

[fix]
1. <scope>: <中文描述>

[change]
1. <scope>: <中文描述>
```

### 步骤 5：展示预览并请求确认

向用户展示完整提交消息预览（原文格式，不做省略）：

> 以上是本次提交的消息预览，请确认是否执行提交？
> - 输入"确认"或"yes"执行提交
> - 输入"修改"或"no"返回修改

用户确认后执行：

```bash
git commit -m "<完整提交消息>"
```

### 步骤 6：确认提交成功

```bash
git log --oneline -1
```

向用户展示提交的 hash 和标题，确认提交完成。

---

