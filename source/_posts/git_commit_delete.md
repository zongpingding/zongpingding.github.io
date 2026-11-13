---
title: Delete Git commit
tags: Git
categories: Computer
date: 2024-08-25 00:00:00
---


## 操作
git 仓库删除所有提交历史记录，成为一个干净的新仓库,把旧项目提交到 GitHub 上，但是会有一些历史记录，这些历史记录中可能会有项目密码等敏感信息。如何删除这些历史记录，形成一个全新的仓库，并且保持代码不变呢？

```shell
1.Checkout
git checkout --orphan latest_branch

2. Add all the files
git add -A

3. Commit the changes
git commit -am "commit message"

4. Delete the branch
git branch -D master

5.Rename the current branch to master
git branch -m master
   
6.Finally, force update your repository
git push -f origin master
```


## 说明
但是此时本地的 git commit 记录仍然存在，对应的 `.git` 文件仍然很大. 如果你的仓库托管在 Github 上，那么这个仓库的 `activity` 上仍然会显示你的提交历史记录。如果你不想让别人看到你的提交历史记录，那么你可能需要向 Github 发起一个请求，请求他们帮你清除提交历史记录。

> 不想这么麻烦，删库吧 :-)
