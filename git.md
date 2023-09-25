# git
## 基本操作
* 显示git版本：git --version
* 设置用户签名：git config --global user.name 用户名
   设置邮箱：git config --global user.email 邮箱
   查看所有配置：git config list
* 克隆仓库：git clone 仓库地址
* 初始化仓库：git init 
* 查看本地库状态：git status
* 添加到暂存区：git add 文件名 （. 表示添加所有新增文件）
* 提交到本地库：git commit -m’描述’ 文件名（默认所有暂存区的文件）
* 查看版本信息：git reflog 
* 查看版本详细信息：git log
* 版本回退：
`git reset --soft… HEAD^ //回退到上一版本`
`git reset [--soft | --mixed | --hard] [版本号]`
   + –mixed
1 使用完 reset --mixed 后，执行 git add 将这些改变过的文件內容加入 暂存区（index）中，再执行 git commit 将 Index 暂存区中的內容提交至仓库（ Repository ）中，这样一样可以达到合并 commit 节点的效果
2 也可以用于发现add错误了，可以执行git reset HEAD来重置暂存区（index）的文件
   + –soft
1 使用完reset --soft后，commit_id之后提交的内容会被放入暂存区（index）中，此时已经add了，可以直接执行commit，将commit_id之后的提交作为一个commit来统一进行提交，减少不必要的log记录
   + –hard
1 使用 git reset --hard HEAD（HEAD也可以是某个commit_id) 来强制恢复 git 管理的文件夹的內容及状态；此时目标节点(commit_id)后的所有提交都会被删除
```
 git log // 查询要回滚的 commit_id
 // 这里的commit_id是当前提交错误的想要撤回的commit之前的那个正确的commit
 // 执行前，先将本地代码切换到对应分支
 git reset --hard commit_id // HEAD 就会指向这个commit_id的提交记录
```
这种操作存在一个问题，服务器上的代码虽然被还原了，但假如有多个人在这个分支上开发，他们本地的版本依然是比服务器上的版本高的，所以，别人再重新提交(push)代码的话，你撤销的操作又会被重新，你上面的操作也就白操作了。解决办法是，让别人把本地的分支先删掉，然后重新从服务器上拉取分支,或者你在对方提交完所有本地代码之后对方没有再进行修改代码，这个时候你提交了，发现你提交的有问题，再进行reset操作，然后让对方拉取（pull）最新的代码也ok，最主要的就是对方本地没有你要reset的代码即可
2 误删恢复
如果回滚代码之后发现复制错了 commit_id，或者误删了某次 commit 记录，也可以通过下方代码恢复：
```vim
git relog // 复制要恢复操作的前面的 hash 值
git reset --hard hash // 将 hash 换成要恢复的历史记录的 hash 值
```

## 分支操作
* 查看分支：git branch -v
* 创建分支：git branch 分支名
* 切换分支：git checkout 分支名
* 合并分支：git merge 分支名
  + 冲突产生的表现:后面状态为 MERGING
`Layne@LAPTOP-Layne MINGW64 /d/Git-Space/SH0720 (master|MERGING) `

  + 冲突产生的原因:
合并分支时，两个分支在同一个文件的同一个位置有两套完全不同的修改。Git 无法替
我们决定使用哪一个。必须人为决定新代码内容。
查看状态(检测到有文件有两处修改)
  + 解决冲突
  编辑有冲突的文件，删除特殊符号，决定要使用的内容
`特殊符号:<<<<<<< HEAD 当前分支的代码 ======= 合并过来的代码 >>>>>>> hot-fix `
## 远程仓库操作
* 查看当前所有远程地址别名：git remote -v
* 起别名：git remote add 别名 仓库地址
* 推送到远程仓库：
   + git push 别名 分支
   + git push 远程库地址 分支名（默认master）
* 从远程仓库拉取change：git pull 远程库地址 分支名（默认master）
* 克隆仓库：git clone 远程仓库地址
* 
   