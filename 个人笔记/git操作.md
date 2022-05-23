## 1、git cherry-pick

​	`git cherry-pick`命令的作用就是将指定的提交应用于其它分支（能够把另一个分支的一个或多个提交复制到当前分支）

#### 步骤：

- 先`git log`查看要转移的提交的哈希值
- 再切换到想要复制到的分支
- 使用`git cherry-pick  hashA  hashB`执行复制
- 最后`git push`提交复制后的提交

