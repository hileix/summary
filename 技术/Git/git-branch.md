# git branch

分支相关的操作

## 1. 查看分支

git branch -a: 查看所有分支。

## 2. 创建分支

git branch <branch-name>: 添加名称为 `branch-name` 的分支。
git branch -b <branch-name>: 创建并切换分支，相当于以下两个命令的组合：

```shell
git branch <branch-name>
git checkout <branch-name>
```

## 3. 删除分支

git branch -D <branch-name>: 删除本地分支。

git push origin --delete <branch-name>: 删除远程分支。

## 4.
