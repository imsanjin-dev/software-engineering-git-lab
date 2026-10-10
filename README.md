# 软件工程与实践 Git 基本操作实验报告

实验日期：2026 年 10 月 10 日  
GitHub 仓库：https://github.com/imsanjin-dev/software-engineering-git-lab

## 一、实验目的

熟悉 Git 的基本使用方法，包括仓库初始化、暂存和提交、远程仓库操作、分支管理、冲突解决及分支合并。

## 二、实验过程

### 1. 初始化本地仓库

新建练习目录，在目录中初始化 Git 仓库：

~~~bash
git init -b main
git status
~~~

初始化后生成 `.git` 目录，默认分支为 `main`。

### 2. 添加文件到暂存区与撤销修改

创建 `README.md` 和 `config.txt`，将文件加入暂存区。分别测试首次提交前取消暂存、已有提交后取消暂存，以及撤销工作区修改：

~~~bash
git add README.md config.txt
git rm --cached config.txt
git add config.txt
git commit -m "Initialize Git practice repository"

# 修改 config.txt 后
git add config.txt
git restore --staged config.txt
git checkout -- config.txt
~~~

`git restore --staged` 只取消暂存，不删除工作区修改；`git checkout --` 会丢弃文件中尚未提交的修改，使用时需要注意。

### 3. 提交到本地仓库并查看日志

使用 `git commit` 保存版本，使用 `git log` 查看提交记录：

~~~bash
git add README.md config.txt
git commit -m "Initialize Git practice repository"
git log --oneline
~~~

首个提交为 `bc8ad59`。后续修改也分别进行了提交，可通过提交日志追踪版本变化。

### 4. 创建远程仓库

在 GitHub 创建公开仓库 `software-engineering-git-lab`，不预先添加 README，以便推送已有的本地提交。

仓库地址：https://github.com/imsanjin-dev/software-engineering-git-lab

### 5. 关联本地仓库与远程仓库

将 GitHub 仓库添加为 `origin`：

~~~bash
git remote add origin https://github.com/imsanjin-dev/software-engineering-git-lab.git
git remote -v
~~~

可以查看远程仓库的获取与推送地址。

### 6. 推送本地代码到远程仓库

推送主分支与练习分支：

~~~bash
git push -u origin main
git push origin feature-config feature-docs
git ls-remote --heads origin
~~~

远程仓库中包含 `main`、`feature-config` 和 `feature-docs` 三个分支。

### 7. 从远程仓库拉取更新

另外克隆一份仓库，在第二份仓库里新增 `REMOTE_CHANGE.txt`，提交并推送：

~~~bash
git clone https://github.com/imsanjin-dev/software-engineering-git-lab.git
git add REMOTE_CHANGE.txt
git commit -m "Add update from second clone"
git push origin main
~~~

回到原工作仓库执行：

~~~bash
git pull --ff-only origin main
~~~

拉取时显示 `Fast-forward`，`REMOTE_CHANGE.txt` 成功同步到本地。完整输出保存在 [docs/remote-pull-output.txt](docs/remote-pull-output.txt)，对应提交为 `7c8486c`。

### 8. 创建和切换分支

通过 `git switch` 创建并切换练习分支：

~~~bash
git switch -c feature-config
git switch main
git switch -c feature-docs
git switch main
git branch
~~~

不同分支可以分别进行修改和提交，互不直接影响。

### 9. 制造并解决合并冲突

在 `feature-config` 分支中将 `config.txt` 的内容改为 `mode=feature` 并提交；回到 `main`，将同一行改成 `mode=main` 并提交，然后执行合并：

~~~bash
git switch feature-config
git add config.txt
git commit -m "Change config on feature branch"

git switch main
git add config.txt
git commit -m "Change config on main branch"
git merge feature-config
~~~

由于两个分支修改了同一行，Git 无法自动判断保留哪一份内容，终端提示：

~~~text
Auto-merging config.txt
CONFLICT (content): Merge conflict in config.txt
Automatic merge failed; fix conflicts and then commit the result.
~~~

打开 `config.txt`，确认需要保留的内容，将其统一为 `mode=merged`，删除冲突标记，之后重新暂存并提交：

~~~bash
git add config.txt
git commit -m "Resolve config merge conflict"
~~~

冲突解决提交为 `a2f58e9`，原始报错见 [docs/merge-conflict-output.txt](docs/merge-conflict-output.txt)。

### 10. 合并不同分支的代码

在 `feature-docs` 分支增加 `docs/usage.md` 并提交，切换回主分支后合并：

~~~bash
git switch main
git merge --no-ff feature-docs -m "Merge feature-docs into main"
git log --graph --oneline --all
~~~

`--no-ff` 保留了明确的合并提交。该次合并提交为 `92e7c8d`，可以通过提交图看到两条分支的合并关系。

## 三、实验结果与总结

本次练习完成了仓库初始化、暂存区操作、本地提交、远程仓库关联、推送和拉取、分支创建与切换、合并冲突处理及分支合并。GitHub 仓库保留了相关提交记录与三个分支，可核对版本变化过程。

通过冲突实验可以看出，Git 能够自动合并不冲突的修改，但当两个分支修改同一处内容时，需要先手动处理冲突，再提交合并结果。`git add`、`git commit` 和 `git push` 分别对应暂存、提交本地版本和同步到远程仓库，不能混为同一步操作。
