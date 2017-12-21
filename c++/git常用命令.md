#### 1.删除远程分支

先查看远程分支

```
git branch -r
```

使用下面两条命令删除远程分支

```
git branch -r -d origin/branch-name
git push origin :branch-name
```

#### 2.压缩多个Commit

```
git rebase -i HEAD~[number_of_commits]
```

如果你想要压缩最后两个commit，你需要运行下列命令。

```
git rebase -i HEAD~2
```

#### 3.查看文件的最新变动





