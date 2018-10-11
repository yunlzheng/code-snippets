# Git

## 命令行

创建分支

```
git checkout -b develop master
```

合并分支

```
git checkout master
git merge --no-ff develop
```

永久删除文件

```shell
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch path-to-your-remove-file' --prune-empty --tag-name-filter cat -- --all

# 提交变更
git push origin master --force --all

# GC本地
rm -rf .git/refs/original/
git reflog expire --expire=now --all
git gc --prune=now
git gc --aggressive --prune=now
```

## Git分支管理模型

* Github Flow
* Gitlab Flow
* GitFlow

## Rebase Vs Merge

![](../../_images/git-flow.png)

Merge

![](../../_images/git-merge-2.png)

Rebase

![](../../_images/git-rebase.png)

## 其它

* Git大文件管理： [git-lsf](https://git-lfs.github.com/)
