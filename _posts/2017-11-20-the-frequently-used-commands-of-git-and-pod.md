---
layout: post
title: "git 和 pod 常用命令"
author: "chenxing"
date: 2017-11-20
tag: CMD
---


在 CocoaPods 创建自己的公开库和私有库时，我们会经常使用到 git 和 pod 命令，以下是我创建公开库和私有库过程中常用到的一些命令，希望这些能够帮助到您。


## git 命令

```
mkdir xx #创建一个空目录，xx指目录名
pwd #显示当前目录的路径。
cat xx #查看xx文件内容

git --help #帮助命令

# git global setup
git config --global user.name "xiaoming" #全局用于提交显示的名字
git config --global user.email "xiaoming@163.com" #全局用于提交显示的邮箱地址
git config user.name "xiaoming" #当前项目用于提交显示名字
git config user.email "xiaoming@163.com" #当前项目用于提交显示的邮箱地址

git init #把当前的目录变成可以管理的git仓库，生成隐藏.git文件
git status #查看仓库状态

git add . #他会监控工作区的状态树，使用它会把工作时的所有变化提交到暂存区，包括文件内容修改(modified)、被删除的文件以及新文件(new)。
git add -u #他仅监控已经被add的文件（即tracked file），他会将被修改的文件提交到暂存区。add -u 不会提交新文件 (untracked file)。(git add --update的缩写)
git add -A #上面两个功能的合集（git add --all的缩写）
git commit -m "描述" #从暂存区提交代码到远程库
git push origin master #把master分支推送到远程库对应的远程分支上
git push -u origin master -f #强制提交
git push origin --delete xx #删除远程分支xx

git tag -a '1.0.1' -m '描述' #添加版本为1.0.1的tag
git tag  #查看tag
git push --tags #将本地创建的tag推到远程库
git tag -d '1.0.1' #删除版本为1.0.1的tag
git push origin :1.0.1 #从远程库删除版本为1.0.1的tag

git remote set-url origin https://token@github.com/chenxing640/xxx.git/ #设置用token提交
git remote #不带参数，列出已经存在的远程分支
git remote -v #列出详细信息，在每一个名字后面列出其远程url，-v 选项(译注: 此为 -verbose 的简写，取首字母)。
git remote add [shortname] [url] # 添加一个新的远程仓库，可以指定一个简单的名字，以便将来引用。
git remote add origin2 git@github.com:tianqixin/runoob-git-test.git #添加仓库origin2
git remote rm [别名] #删除远程仓库你可以使用命令
git remote rm origin2 #删除仓库origin2

git clone https://github.com/chenxing640/DYFToast.git #从远程库中克隆
git clone https://github.com/chenxing640/DYFToast.git Toast #从远程库中克隆到指定的目录 (Toast)

git log #查看历史记录
git reflog #查看历史记录的版本号id

git diff xx #查看xx文件修改了那些内容

git rm -r --cached .idea/ #将.idea/目录下所有文件从缓存中删除，不删除物理文件
git rm --f .idea/ #将.idea/目录下所有文件从缓存中删除，还会删除物理文件

git branch name #创建分支
git branch #列出分支
git branch -l #列出分支list
git branch -a #列出所有分支
git branch –d dev #删除dev分支
git branch –D dev #强制删除dev分支

git checkout master #切换回master分支
git checkout –b dev #创建新分支并立即切换到该分支下，然后使用`git push origin dev`提交远程分支
#未使用 git add 缓存代码，用来放弃掉所有还没有加入到缓存区的修改
git checkout -- filepathname #放弃单个文件修改,注意不要忘记中间的"--"，不写就成了检出分支了
git checkout . #放弃所有的文件修改
git checkout master #选择或切换到master分支
git merge dev #将dev分支合并到当前分支(master)中

# Git 有两个命令用来提取远程仓库的更新。
git fetch <远程主机名> #将某个远程主机的更新全部取回本地
git fetch <远程主机名> <分支名> #如果只想取回特定分支的更新，可以指定分支名
git fetch origin master #取回origin主机的master分支
git merge hotfix #从远端仓库提取数据并尝试合并到当前hotfix分支

git pull <远程主机名> <远程分支名>:<本地分支名> #将远程主机的某个分支的更新取回，并与本地指定的分支合并
git pull origin next #如果远程分支是与当前分支合并，则冒号后面的部分可以省略

#开发分支（dev）上的代码达到上线的标准后，要合并到 master 分支
git checkout dev
git pull origin dev
git checkout master
git merge dev
git push origin master

#当 master 代码改动了，需要更新开发分支（dev）上的代码
git checkout master 
git pull origin master
git checkout dev
git merge master 
git push origin dev

#已经使用 git add 缓存了代码，用来清除 git 对于文件修改的缓存
git reset HEAD filepathname # (比如：git reset HEAD readme.md) 来放弃指定文件的缓存
git reset HEAD . #放弃所有的缓存

#已经用 git commit 提交了代码，
git reset --hard HEAD^ #回退到上一个版本 (即回退到上一次commit的状态)
git reset --hard HEAD^^ #回退到上上一个版本 (如果想回退到往上100个版本，使用git reset –hard HEAD~100 )
git reset --hard commitid #用来回退到任意版本，使用 git log 命令来查看git的提交历史。git log 的输出可以看到第一行就是 commitid

git stash #保存当前工作进度，将工作区和暂存区恢复到修改之前
git stash save message #作用同上，message为此次进度保存的说明
git stash list #显示保存的工作进度列表，编号越小代表保存进度的时间越近
git stash pop stash@{num} #恢复工作进度到工作区，此命令的stash@{num}是可选项，在多个工作进度中可以选择恢复，不带此项则默认恢复最近的一次进度相当于git stash pop stash@{0}
git stash apply stash@{num} #恢复工作进度到工作区且该工作进度可重复恢复，此命令的stash@{num}是可选项，在多个工作进度中可以选择恢复，不带此项则默认恢复最近的一次进度相当于git stash apply stash@{0}
git stash drop stash@{num} #删除一条保存的工作进度，此命令的stash@{num}是可选项，在多个工作进度中可以选择删除，不带此项则默认删除最近的一次进度相当于git stash drop stash@{0}
git stash clear #删除所有保存的工作进度
```


## pod 命令

```
pod init #生成Podfile文件

pod spec create DYFToast #生成pod库配置文件 (DYFToast.podspec)

pod lib lint #从本地验证你的pod能否通过验证
pod spec lint DYFToast.podspec --allow-warnings --verbose #从本地和远程验证你的pod能否通过验证，--allow-warnings 允许警告，--verbose 打印详细日志
pod spec lint --sources='https://github.com/CocoaPods/Specs' #验证私有库能否通过时，应该要添加--sources选项，不然会出现找不到repo的错误

pod repo push 本地repo名 podspec名 --sources='https://github.com/CocoaPods/Specs' #在私有库引用了私有库的情况下，在验证和推送私有库的情况下都要加上所有的资源地址，不然pod会默认从官方repo查询。

pod trunk push DYFToast.podspec #发布pods
pod trunk push DYFToast.podspec --allow-warnings #发布pods，--allow-warnings 允许警告

pod trunk me #查看注册信息
pod trunk info xxx #查看某个库的信息，包括拥有者、各版本版本号及发布时间。
pod trunk add-owner xxx e-mailAddress #将某个pod 添加一个维护者
pod trunk remove-owner xxx e-mailAddress #移除某一个维护者
pod trunk delete xxx version #删除某个发送过的pod
pod trunk deprecate xxx #将某个pod 失效
pod trunk me clean-sessions #移除本机sessions
pod trunk me clean-sessions --all #移除除了本机之外的所有sessions
```


## pod模板

通过执行`pod lib create LibXy(pod lib create LibXy --template-url=https://github.com/CocoaPods/pod-template.git)`时，下载一个pod模板，然后更改.podspec文件的配置定制化自己的pod。

```
What platform do you want to use?? [ iOS / macOS ]
 > iOS

What language do you want to use?? [ Swift / ObjC ]
 > Swift

Would you like to include a demo application with your library? [ Yes / No ]
 > YES

Which testing frameworks will you use? [ Quick / None ]
 > None

Would you like to do view based testing? [ Yes / No ]
 > No
```


## 强烈推荐阅读

1、[《 Git 教程 | 菜鸟教程 》](https://link.jianshu.com?t=https://www.runoob.com/git/git-tutorial.html)
2、[《 入门教程 | Git基础 》](https://link.jianshu.com?t=https://git-scm.com/book/zh/v1/Git-基础)
3、[《 Git教程 | 易百教程  》](https://link.jianshu.com?t=https://www.yiibai.com/git)

