# git stash

一般用于暂时将 `工作区和暂存区的改动` 保存起来，然后去另外一个分支修改代码。再次回到本分支时，可以还原上次的修改。

## 1. 查看保存的内容

git stash list: 查看已保存的内容列表。

## 2. 保存内容

git stash save 'message': 保存当前修改的内容，并且加上注释（message）。

## 3. 恢复保存的内容

git stash pop

## 参考

https://blog.csdn.net/daguanjia11/article/details/73810577
