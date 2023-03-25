---
title: git-操作
urlname: git-操作
top: false
categories:
  - 工具
tags:
  - git
author: jesonlin
date: 2022-12-17 11:14:23
update: 2022-12-22 15:14:19
---

Git是目前世界上最先进的分布式版本控制系统，所有的版本控制系统，只能跟踪文本文件的改动，而无法跟踪二进制文件的改变。

<!-- more -->

## 安装和配置
1. 安装（cetos7为例）
    ```
    yum install git
    <!-- 验证 -->
    git --version
    ```
2. 配置
    ```
    <!-- 账号和邮箱 -->
    git config --global user.name "jesonlin"
    git config --global user.email "jesonlin@outlook.com"

    <!-- 颜色 -->
    git config --global color.ui true

    <!-- 设置别名 -->
    git config --global alias.st status
    git config --global alias.ci commit
    git config --global alias.br branch
    <!-- 撤销暂存区git reset HEAD file -->
    git config --global alias.unstage 'reset HEAD'
    git config --global alias.last 'log -1'
    git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
    ```
3. 查看配置信息
    ```
    git config --list

    <!-- 每个仓库下的配置位置 -->
    .git/config

    <!-- 全局配置位置 -->
    ~/.gitconfig
    ```
4. 忽略特殊文件
    ```
    vim .gitignore

    <!-- 官方提供各类忽略文件模版 -->
    https://github.com/github/gitignore

    <!-- 在线生成.gitignore -->
    https://gitignore.itranswarp.com/

    <!-- 检查某个文件被哪条规则忽略 -->
    git check-ignore -v <文件名>

    <!-- 被忽略情况下强制提交 -->
    git add -f <文件名>

    <!-- 不排除某些文件 -->
    vim .gitignore
    !.gitignore
    ```

## 常用方法
0. 版本库
    ```
    工作区：就是你在电脑里能看到的目录。
    暂存区：英文叫 stage 或 index。一般存放在 .git 目录下的 index 文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。
    版本库：工作区有一个隐藏目录 .git，这个不算工作区，而是 Git 的版本库。
    ```
    ![版本库](/repository.jpg)
    ```
    # 针对工作区的修改
    1. git add: 将工作区修改添加到暂存区
        <!-- 添加/删除指定文件 -->
        git add/rm <file>

        <!-- 添加新文件/修改过的文件，不包括删除文件 -->
        git add .

        <!-- --update update tracked files 添加有更新变化的被追踪的文件，不包括新添加的文件 -->
        git add -u

        <!-- --all，添加所有修改和新增的文件 -->
        git add -A . 
    2. git checkout: 撤销工作区修改，将暂存区覆盖工作区
        <!-- 撤销工作区指定文件修改，用暂存区相应内容替换工作区内容 -->
        git checkout -- <file>

        <!-- 撤销工作区所有修改，用暂存区替换工作区，不影响新增 -->
        git checkout .
    ```
    ```
    # 针对暂存区的修改
    3. git commit: 将暂存区修改添加到版本库
        git commit -m "初始化"
    4. git reset: 撤销暂存区修改，将版本库覆盖暂存区
        <!-- 版本库覆盖暂存区<文件> -->
        git reset HEAD <file>

        <!-- 版本库覆盖暂存区和工作区<文件> -->
        git reset --hard HEAD <file>
    ```
1. 查看提交日志/命令
    ```
    <!-- 提交日志 -->
    git log --pretty=oneline

    <!-- 所有命令（包含回退/前进） -->
    git reflog
    ```
2. 版本回退/前进
    ```
    <!-- --hard 表示工作区和暂存存都重置，无则表示只重置暂存区 -->
    
    <!-- 回退上个版本 -->
    git reset --hard HEAD^

    <!-- 回退上个版本，保留修改 -->
    git reset --soft HEAD^

    <!-- 回退前10个版本 -->
    git reset --hard HEAD~10

    <!-- 跳转到指定版本（包括回退和前进，只要记得commit的id） -->
    git reset --hard 【COMMIT_ID】

    <!-- 查看最后一次提交 -->
    git config --global alias.last 'log -1'
    ```
3. 比较差异
    ```
    <!-- 比较工作区和暂存区的修改 -->
    git diff <文件名>

    <!-- 比较工作区和上一次commit后的修改 -->
    git diff HEAD <文件名>

    <!-- 比较暂存区和上一次commit后的修改 -->
    git diff --cached
    ```


## 分支管理
0. 查看分支
    ```
    <!-- 查看分支(-a 包括远端分支) -->
    git branch -a
    ```
1. 创建分支
    ```
    <!-- 创建分支 -->
    git branch <新分支名>

    <!-- 切换分支 -->
    git checkout <新分支名>
    git switch <新分支名>

    <!-- 创建并切换到分支 -->
    git checkout -b <新分支名>
    git switch -c <新分支名>
    ```
2. 推送分支
    ```
    git push origin <本地分支名>:<远端分支名>
    ```
3. 删除分支
    ```
    <!-- 删除本地分支 -->
    git branch -d <本地分支名>

    <!-- 删除远端分支 -->
    git push origin :<远端分支名>
    git push origin --delete <远端分支名>
    ```
4. 合并分支
    ```
    <!-- 默认使用Fast forward，看不出来曾经做过合并 -->
    git merge <待合并的分支名>
    <!-- 使用普通模式，可以看出曾经合并过 -->
    git merge --no-ff -m "" <待合并分支名>
    ```
    ![普通模式合并分支](/merge-no-ff.png)
5. 分支冲突
    ```
    <!-- Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容 -->
    <!-- 手动修改后提交 -->
    git add
    git commit -m 
    <!-- 查看提交日志 -->
    git log --graph --pretty=oneline --abbrev-commit
    ```
6. 分支策略
    1. master分支是主分支，因此要时刻与远程同步；
    2. dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
    3. bug分支只用于在本地修复bug，就没必要推到远程了；
    4. feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。
    ![分支策略](/merge-strategy.png)


## 实际开发（分支、多人开发冲突）
0. 分支基本情况
    master  
    dev（当前已有修改）
1. stash (保存现场）
    临时需要修复bug
    ```
    <!-- 1. 保存dev现场 -->
        git stash

    <!-- 2. 切换到master分支 -->
    git checkout master

    <!-- 创建临时修复bug分支 -->
    git checkout -b issue-101

    <!-- 提交修改 -->
    git add -A .
    git commit -m "fix buf 101"

    <!-- 切换到master分支 -->
    git checkout master

    <!-- 合并bug分支 -->
    git merge --no-ff -m "merge bug fix 101" issue-101

    <!-- 切换回dev分支 -->
    git checkout dev

    <!-- 查看之前保存的现场 -->
    git stash list

    <!-- 还原旧的现场 -->
    git stash apply
    <!-- 还原旧的指定现场 -->
    git stash apply stash@{0}
    <!-- 删除旧的现场 -->
    git stash drop
    <!-- 还原并删除 -->
    git stash pop
    ```
2. cherry-pick (重放bug修复到dev分支)
    ```
    <!-- dev 提交bug修复的提交id -->
    git cherry-pick <bug分支上修复bug的commit_id>

    <!-- cherry-pick 重放指定提交 -->
    a - b - c -d    master
        |
        e - f - g   dev
    git checkout master
    git cherry-pick f
    <!-- 将dev的f提交重放到master分支 -->
    a - b - c -d - f   master
        |
        e - f - g   dev    

    <!-- cherry-pick 重放指定多个提交 -->
    git cherry-pick <A-commit_id> <B-commit_id>
    <!-- cherry-pick 重放指定多个连续提交(A,B] -->
    git cherry-pick <A-commit_id>..<B-commit_id>
    <!-- cherry-pick 重放指定多个连续提交[A,B] -->
    git cherry-pick <A-commit_id>^..<B-commit_id>
    ```
3. 多人协作
    ```
    <!-- 1. 试图推送自己的修改 -->
    git push origin <分支名>

    <!-- 2. 如果推送失败，使用pull试图合并 -->
    git pull
    <!-- 如果pull提示没有tracking，则关联本地和远程分支名 -->
    git branch --set-upstream-to <本地分支名> origin/<远程分支名>

    <!-- 3. 如果合并有冲突，解决冲突并提交 -->

    <!-- 4. 没有冲突或者冲突解决后，推送 -->
    git push origin <分支名>
    ```
4. rebase (变基操作把本地未push的分叉提交历史整理成直线)
    ```
    origin/master    a - b - c
                         |
    local/master         d - e
    <!-- 1. 本地已有1+未push得提交 -->
    （d 和 e）

    <!-- 2. 直接提交会报冲突 -->
    git push 

    <!-- 3. 先把c拉取到本地 -->
    git pull 

    <!-- 4. 此时本地提交比远程超前3个提交 -->
    git log --graph --pretty=oneline --abbrev-commit

    <!-- 5. 变基，改变本地两个未提交和远程新提交顺序-->
    git rebase

    原本分叉的提交现在变成一条直线了，Git把我们本地的提交“挪动”了位置，放到远程新提交的后面，符合整体变更顺序。
    ```
    ![变基前本地提交记录](/rebase-1.jpg)
    ![变基后本地提交记录](/rebase-2.jpg)


## 标签（版本库快照。）
1. 标签
    ```
    <!-- 查看标签列表 -->
    git tag

    <!-- 查看标签 -->
    git show <标签名>

    <!-- 打标签 -->
    git tag -a <标签名> -m "xxx" <commit_id>

    <!-- 推送指定/所有标签 -->
    git push origin <标签名>
    git pusH origin --tags

    <!-- 删除本地标签 -->
    git tag -d <标签名>

    <!-- 删除远程标签 -->
    git push origin :refs/tags/<标签名>
    ```


## 仓库
1. 创建本地仓库
    ```
    mkdir project
    cd project
    git init
    touch README.md
    git add README.md
    git commit -m "init git"
    ```
2. 远程仓库
    1. 本地仓库关联远程仓库
        ```
        <!-- 本地仓库关联远程仓库 -->
        git remote add origin git@github.com:linjinzhong/blog.git

        <!-- 解除关联 -->
        $ git remote rm origin

        <!-- 推送 -->
        $ git push -u origin master
        ```
    2. 克隆远程仓库
        ```
        <!-- 克隆远程仓库，默认只有master分钟 -->
        git clone git@github.com:linjinzhong/blog.git
        <!-- 克隆后希望拉取非master分支 -->
        git checkout -b dev origin/dev
        <!-- 直接克隆非master分钟 -->
        git clone -b ac git@gitlab.yopoint.vip:ac/YoPointSwift.git
        ```
    3. 权限
        ```
        ssh-keygen -t rsa

        <!-- 一路回车 -->
        ll /root/.ssh/

        <!-- 将/root/.ssh/id_rsa.pub内容拷贝到git上 -->
        cat /root/.ssh/id_rsa.pub

        <!-- git ssh地址 -->
        https://github.com/settings/keys
        ```


## REFERENCE
[git-官网](https://git-scm.com/)
[廖雪峰-Git教程](https://www.liaoxuefeng.com/wiki/896043488029600)
[菜鸟-Git教程](https://www.runoob.com/git/git-tutorial.html)
[Git-Cheat-Sheet](https://github.com/linjinzhong/blog/blob/source/source/_posts/git/git-cheat-sheet.pdf)
