---
title: 如何 cherry-pick PRs
translated: true
---

本文档解释了如何在 `Project-HAMi/HAMi` 仓库的发布分支上管理 cherry pick。一个常见的用例是将 PR 从 master 分支回移到发布分支。

> 本文档摘自 [Kubernetes cherry-pick](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-release/cherry-picks.md)。

## 先决条件

- 一个已合并到 `master` 分支的拉取请求。
- 目标发布分支已存在（例如 `release-v2.11`）。
- 已配置 Git 和 GitHub，`origin` 指向你的 fork，`upstream` 指向 `Project-HAMi/HAMi`。

## 哪些 PR 适合进行 Cherry Pick

与正常的 master 分支的合并量相比，发布分支的 PR 数量要少一个或两个数量级。这是因为发布分支的审查更为严格。重点在于关键的错误修复，例如：

- 数据丢失
- 内存损坏
- 崩溃、挂起
- 安全问题

仅影响 alpha 功能的功能性问题的错误修复（不是数据丢失或安全问题）不符合关键错误修复的标准。

如果你提议进行 cherry pick，但它不是一个明显的关键错误修复，请重新考虑。如果在反思后你仍希望继续，请通过补充你的 PR 来加强你的理由，例如：

- 详细描述问题的 GitHub issue

- 变更的范围

- 添加变更的风险

- 相关回归的风险

- 执行的测试，添加的测试用例

- 关键利益相关者的审阅者/批准者对变更为必要的回移的信心的证明

确保我们的整个社区积极参与项目的增强是至关重要的。如果某个已发布的功能未在特定提供商的平台上启用，这是一个需要在 `master` 分支中解决的社区失误，以便后续发布。这样的启用不会被回移到补丁发布分支。

## 发起 Cherry Pick

在本地 HAMi 仓库中，从目标发布分支创建一个回移分支。下面以 `release-v2.11` 为例。将 `MERGED_COMMIT_SHA` 替换为 `master` 上包含修复的提交 SHA。如果 PR 是 squash 合并的，请使用合并后的提交。

```bash
git fetch upstream
git switch -c backport-fix-v2.11 upstream/release-v2.11
git cherry-pick -x -s MERGED_COMMIT_SHA
git push -u origin backport-fix-v2.11
```

从 fork 的 `backport-fix-v2.11` 分支向 `Project-HAMi/HAMi:release-v2.11` 发起 PR，并链接原 PR。每个目标发布分支都使用独立的回移分支和 PR。

## Cherry Pick 审核

与其他 PR 一样，代码 OWNERS 会根据需要对 cherry pick PR 进行审核 (`/lgtm`) 和批准 (`/approve`)。

遵循通常的发布说明要求，并参考原 PR 补充上下文。

## Cherry Pick 故障排除

贡献者在发起 cherry pick 时可能会遇到以下一些困难。

- 如果 cherry pick 发生冲突，解决冲突并用 `git add` 暂存文件，然后运行 `git cherry-pick --continue`，再推送分支。

- 如果回移分支的 CI 失败，在该分支修复问题后，将更新后的分支推送到你的 fork。

## 不支持版本的 Cherry Pick

社区支持和补丁的版本需要讨论。
