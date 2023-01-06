
# git

1. workspace(工作区)(add=>)
2. stage(暂存区)(commit=>| git reset --soft commit_id => 工作区)
3. .git(本地仓)(push=>|git reset --mixed commit_id => 暂存区| git reset --hard commit_id => 暂存区&工作区)
4. 远程仓

## 什么是git？

### 看图理解

1. 从中看出了哪些问题？
2. 从中知道了哪些问题的答案？

## .gitignore

1. 新规则只考虑新的文件**不向后兼容**
2. .gitignore配置文件是按行从上到下进行规则匹配的
3. 该文件会影响repo的所有人，如果只是个人习惯问题建议操作**.git/info/exclude**，语法和效果一样的



## git操作

* 保存
  - 工作区 -> 暂存区 add
  - 暂存区 -> 本地库 commit 
  - 本地库 -> 远程库 push
* 回滚
  - 暂存区 -> 工作区 reset|checkout file|rm
  - 本地库 -> 暂存区 reset|checkout HEAD
* 合并(也是保存)
  - merge(合并到当前分支)
  - rebase
  - tag -a version (commit_id)忽略的话就是Head
  - git tag(查)
* 创建|打开
  - branch
  - checkout -b
* 跟踪
  - fetch (origin)
  - pull
* 配置
  - remote add [shortname] [url]

- 设置用户名
- 设置邮箱
- 一个客户端设置多个用户
- ```shell
  git config --global user.name what
  git config --global user.emial what

  # 
  ssh-keygen -t rsa -C "demo@126.com" -b 4096 -f demo

  # ssh-copy-id -i demo.pub user@server
  # copy content
  pbcopy < ~/.ssh/demo.pub

  ssh-add ~/.ssh/demo

  vim ~/.ssh/config

  Host gitee.com
  HostName gitee.com
  User git
  PreferredAuthentications publickey
  Port 22
  IdentityFile ~/.ssh/demo
    
  ```

### 养成良好的习惯

1. 上班前pull|fetch
2. 下班前commit|push
3. 多用branch、patch
4. 不建议用git管理二进制文件(超过5M的)；

### check out

场景：
* 取消本地文件更改
* 取消本地分支更改

```shell
git checkout /patch/src/todo/
git checkout [current branch]
```

### git remote

```shell
# tracing远程分支
git remote add origin git@github.com:tianqixin/runoob-git-test.git
# untracing远程分支
git remote rm 
```

### git rm

场景：
* 删除tracked的文件
* --cached 只删除暂存区,不删除本地;不加删除本地和缓存区的

```shell
git rm -rf --cached /path/src/todo/
# 设置忽略跟踪(1)，忽略远程的变更，只保留本地文件；
git update-index --assume-unchanged /path/file
# 设置忽略跟踪(2)，这个本地会同步远程的，本地的变更不会推送给远程
git update-index --skip-worktree /path/file
#恢复跟踪
git update-index --no-assume-unchanged /path/to/files

# (1)assume-unchanged：这个会关闭文件与远程仓库的跟踪，认为这个文件远程仓库是不会修改，所以每次pull都是本地的文件
# (2)skip-worktree：这个不会关闭文件与远程仓库的跟踪，只是告诉Git不要跟踪对本地文件/文件夹的更改。如果远端仓库内容有变化，pull时会拉取最新的变化，并提示冲突，但因为没有跟踪本地更改，所以需要no-skip-worktree再合并最新的变化。

```

### git reset(本地回滚)

```shell
# 重置暂存区和当前fetch一致
git reset HEAD
# 重置工作区&暂存区和当前fetch保持一致
git reset --hard HEAD
# 重置工作区&暂存区和当前fetch的上一个版本保持一致
git reset --hard HEAD^
```

### git checkout

1. 切换分支
2. 回滚
  * 回滚到某个commit
  * 回滚某个commit中的文件
  * 回滚当前目录

```shell
# 
git checkout branch_name
# 
git checkout -b new_branch_name
#
git checkout HEAD commit_id
# 
git checkout HEAD file
#
git checkout --file
#
git checkout .
# 将分支并入到当前分支,默认全部commit保留(如果超前会abort)
git merge dev
# 将分支并入到当前分支,默认全部commit保留,并且不考虑fast-forward
git merge dev --no-diff
# 将分支并入当前分支,并且新创建一个commit,作为新的commit,相当于压缩dev的commit记录
git merge dev --squash
```

### 分支问题

```shell
# 上传远程(相当于merge到远程最前端)
git push --set-upstream origin local_branch:remote_branch
# 从远程拉取分支到本地
git checkout -b develop origin/develop
# 默认推送(获取创建一个新分支,没有的话创建有的话追加)
git push origin mater
# 删除远程分支
git push origin --delete xxx
```
