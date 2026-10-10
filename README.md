# 软件工程与实践｜Git 基础操作作业

> **实验已完成，教学平台尚未提交（2026-10-10）。** 使用独立 Git 仓库实际完成本地操作、制造并解决合并冲突、GitHub 真实远程 push/pull 与多分支推送。正式仓库：<https://github.com/imsanjin-dev/software-engineering-git-lab>。

## 作业来源和交法

- 教学平台：`软件工程与实践作业`，2026-09-23 17:54 发布，页面提示「需要在线提交作业，点击去完成作业」。
- 截图列出十项 Git 练习，但未明确给出截止时间、必须上传的文件类型或报告模板。
- 推荐交付方式：在 GitHub/Gitee 建立独立的作业仓库，在仓库 `README.md` 中写操作过程，再在教学平台提交**仓库链接**。如果提交界面只允许附件，则可上传 Markdown 转成的 PDF、Git 操作截图或教师允许的压缩包；以「去完成」后的实际字段为准。
- 课程原始课件：同目录的 `Git介绍及使用.pptx`（第 16–23 页介绍分支流程与核心命令）。
- 本次先在隔离演示环境练习，随后在本机独立 Git 仓库重新执行操作并正常推送到 GitHub；提交历史、分支、冲突输出与远程拉取日志直接保留在公开仓库。LifeOS 主仓库不参与分支冲突练习。

## 十项任务的对应操作

| # | 要求 | 操作 / 证据 | 当前状态 |
| --- | --- | --- | --- |
| 1 | 本地仓库初始化 | `git init -b main`，首个 Commit `bc8ad59` | 已验证 |
| 2 | 添加与撤销暂存 | `git add`、`git rm --cached`、`git restore --staged`、`git checkout --` | 已验证 |
| 3 | 本地提交、查看日志 | `git commit`、`git log --oneline --graph --all` | 已验证 |
| 4 | GitHub/Gitee 创建远程仓库 | [GitHub 仓库](https://github.com/imsanjin-dev/software-engineering-git-lab)（Public） | 已完成 |
| 5 | 关联远程 | `git remote add origin https://github.com/imsanjin-dev/software-engineering-git-lab.git` | 已验证 |
| 6 | 推送 | `git push -u origin main`、`git push origin feature-config feature-docs`，远端显示 3 个分支 | 已验证 |
| 7 | 拉取 | 第二份 GitHub 克隆推送 `REMOTE_CHANGE.txt`，第一份执行 `git pull --ff-only origin main` 快进更新 | 已验证 |
| 8 | 创建 / 切换分支 | `git switch -c feature-config`、`git switch main`、`git switch -c feature-docs` | 已验证 |
| 9 | 冲突及解决 | 两分支修改 `config.txt` 同一行，`git merge feature-config` 触发 `CONFLICT (content)`；解决提交 `a2f58e9`，原始输出在 `docs/merge-conflict-output.txt` | 已验证 |
| 10 | 分支合并 | `git merge --no-ff feature-docs -m "Merge feature-docs into main"`，合并提交 `92e7c8d` | 已验证 |

## 操作流程和容易错的点

### 1. 初始提交、暂存、撤销

```bash
git init -b main
git add README.md config.txt
git rm --cached config.txt            # 首次 commit 之前撤销暂存
git add config.txt
git commit -m "Initialize Git practice repository"

# 已有 HEAD 之后，修改 config.txt，再执行：
git add config.txt
git restore --staged config.txt       # 从暂存区撤回，但不丢弃工作区修改
git checkout -- config.txt           # 丢弃 config.txt 尚未提交的工作区修改（谨慎）
git log --oneline
```

### 2. 分支冲突（已实操）

```bash
git switch -c feature-config
# 将 config.txt 的 mode=initial 改成 mode=feature
git add config.txt
git commit -m "Change config on feature branch"
git switch main
# main 修改同一行成 mode=main，完成提交
git add config.txt
git commit -m "Change config on main branch"
git merge feature-config             # 实际出现 CONFLICT (content)
```

Git 插入的冲突标记类似：

- `<<<<<<< HEAD`：当前 `main` 分支的内容开始
- `mode=main`
- `=======`：冲突两侧的分隔标记
- `mode=feature`
- `>>>>>>> feature-config`：另一分支的内容结束

此次演示将文件整理成 `mode=merged`，删除所有标记，然后：

```bash
git add config.txt
git commit -m "Resolve config merge conflict"
git switch -c feature-docs
# 新增 docs/usage.md 后 git add、git commit
git switch main
git merge --no-ff feature-docs -m "Merge feature-docs into main"
git log --graph --oneline --all
```

本次实验 **真实触发了冲突并完成解决提交**（`a2f58e9`）；不用在最终文件里保留冲突标记。

### 3. GitHub 远程仓库推送与拉取（已实操）

在线仓库地址：<https://github.com/imsanjin-dev/software-engineering-git-lab>

```bash
git remote add origin https://github.com/imsanjin-dev/software-engineering-git-lab.git
git push -u origin main
git push origin feature-config feature-docs
git ls-remote --heads origin

# 在第二份独立克隆中添加 REMOTE_CHANGE.txt、提交并推送到 GitHub
git add REMOTE_CHANGE.txt
git commit -m "Add update from second clone"
git push origin main

# 回到第一份工作仓库，拉取第二份克隆推送的更新
git pull --ff-only origin main
```

拉取返回 `Fast-forward`，本地 `HEAD` 与 `origin/main` 均指向提交 `7c8486c`（执行时）；原始拉取输出见 `docs/remote-pull-output.txt`。之后编辑 README 并再次推送不影响这一实验记录。

当前 README 即为独立在线作业仓库的说明，实验在本地实际执行后使用正常 `git push` 上传，没有强制推送。

## 教学平台提交前核对

- [x] 在独立 Git 仓库实操初始化、暂存/撤销、提交、分支和合并
- [x] 实际复现 `CONFLICT` 并完成解决提交
- [x] GitHub Public 仓库已创建、实际 `git push`，并推送 3 个分支
- [x] 在第二份克隆中推送变更，第一份克隆实际 `git pull` 成功
- [x] README 已包含实际远程地址、10 项记录、命令及可验证的 Git 历史
- [ ] 点击教学平台「去完成」核对具体提交方式并提交，确认显示「已完成」或「已提交」

**教学平台提交文本建议：**

> Git 基础操作作业已完成。仓库：https://github.com/imsanjin-dev/software-engineering-git-lab 。README 包含 10 项操作说明；Git 提交历史包含分支创建、冲突解决和分支合并记录。

**注意：** Git 操作由 AI 辅助在本机环境执行并核验，不能等同于学生本人已独立掌握。建议提交前自行复述关键步骤，理解暂存区、push/pull、分支及冲突解决。教学平台未收到提交确认前，整个课程任务保持待提交状态。
