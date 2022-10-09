<div style="background:pink;font-size:26px">创建版本库</div>

1. 克隆远程版本库

   ```shell
   git clone <url>
   ```

2. 初始化本地版本库

   ```shell
   git init
   ```



<div style="background:pink;font-size:26px">修改和提交</div>

1. 查看状态

   ```shell
   git status
   ```

2. 查看变更内容

   ```shell
   git diff
   ```

3. 跟踪所有改动过的文件

   ```shell
   git add .
   ```

4. 跟踪指定的文件

   ```
   git add <file>
   ```

5. 文件改名

   ```shell
   git mv <old> <new>
   ```

6. 删除文件

   ```shell
   git rm <file>
   ```

7. 停止跟踪文件但不删除

   ```shell
   git rm --cached <file>
   ```

8. 提交所有更新过的文件

   ```shell
   git commit -m "commit message"
   ```

9. 修改最后一次提交

   ```shell
   git commit --amend
   ```

   

<div style="background:pink;font-size:26px">查看提交历史</div>

1. 查看提交历史

   ```shell
   git log
   ```

2. 查看指定文件的提交历史

   ```shell
   git log -p <file>
   ```

3. 以列表方式查看指定文件的提交历史

   ```shell
   git blame <file>
   ```



<div style="background:pink;font-size:26px">撤销</div>

1. 撤销工作目录中所有未提交文件的修改内容

   ```shell
   git reset --hard HEAD
   ```

2. 撤销指定的未提交文件的修改内容

   ```shell
   git checkout HEAD <file>
   ```

3. 撤销指定的提交

   ```shell
   git revert <commit>
   ```



<div style="background:pink;font-size:26px">分支与标签</div>

1. 显示所有本地分支

   ```shell
   git branch
   ```

2. 切换到指定分支或标签

   ```shell
   git checkout <branch/tag>
   ```

3. 创建新分支

   ```shell
   git branch <new-branch>
   ```

4. 删除本地分支

   ```
   gut branch -d <branch>
   ```

5. 列出所有本地标签

   ```shell
   git tag
   ```

6. 基于最新提交创建标签

   ```shell
   git tag <tagname>
   ```

7. 删除标签

   ```shell
   git tag -d <tagname>
   ```

   

<div style="background:pink;font-size:26px">合并与衍合</div>

1. 合并指定分支到当前分支

   ```shell
   git merge <branch>
   ```

2. 衍合指定分支到当前分支

   ```shell
   git rebase <branch>
   ```

   

<div style="background:pink;font-size:26px">远程操作</div>

1. 查看远程版本库信息

   ```shell
   git remote -v
   ```

2. 查看指定远程版本库信息

   ```shell
   git remote show <remote>
   ```

3. 添加远程版本库

   ```shell
   git remote add <remote> <url>
   ```

4. 从远程库获取代码

   ```
   git fetch <remote>
   ```

5. 下载代码及快速合并

   ```shell
   git pull <remote> <branch>
   ```

6. 上传代码及快速合并

   ```shell
   git push <remote> <branch>
   ```

7. 删除远程分支或标签

   ```shell
   git push <remote> :<branch/tag-name>
   ```

8. 上传所有标签

   ```shell
   git push --tags
   ```
