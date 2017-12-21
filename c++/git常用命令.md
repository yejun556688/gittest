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

```
git blame [file_name]
```

#### 4.暂存文件的部分修改

```
git add -p [file_name]
```

我们来演示一下在`file_name`文件中添加了3行文字，但只想提交第一行和第三行。先看一下`git diff`显示的结果：

![](/assets/c3f16cfb-7009-4365-b128-ee2b55662f39.png)

然后再看看在 add 命令中添加 -p 参数是怎样的？

