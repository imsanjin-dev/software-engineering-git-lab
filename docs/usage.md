# 分支合并练习

本文件用于记录 `feature-docs` 分支的修改与合并。在该分支提交文件后，切换回主分支执行合并。

```bash
git switch feature-docs
git add docs/usage.md
git commit -m "Add usage notes on feature branch"
git switch main
git merge --no-ff feature-docs -m "Merge feature-docs into main"
```

通过 `git log --graph --oneline --all` 可以查看合并记录，对应合并提交为 `92e7c8d`。
