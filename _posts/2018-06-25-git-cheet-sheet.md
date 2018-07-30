---
layout: post
title:  "Advanced Git Cheet Sheet"
date:   2018-06-25 12:00:00 +0800
categories: tools
---

|code|descriptoin|
|---|---|
|`git commit --amend`|recommit|
|`git push <remote-name> :<branch-name>`|git delete remote branch|
|`git reset HEAD <file>`|unstage|
|`git checkout -- <file>`|discard changes in working directory|
|`git reset HEAD~ `|undo a commit|
|`git fetch origin git`<br>`reset --hard origin/master`|setting your branch to exactly match the remote branch|
为了更加形象地说明，我们假设现在有一个工作目录，里面包含了三个将要被暂存和提交的文件。 
    
    root
    ├── README
    ├── test.rb
    ├── LICENSE

```
$ git add README test.rb LICENSE
$ git commit -m 'The initial commit of my project'
```

### 暂存
1.  为每个文件计算校验和（SHA-1哈希算法）
1.  把当前版本文件快照保存到Git仓库中（Git使用blob对象保存）
1.  将校验和加入到暂存区域

### 暂存
1.  计算每个子目录的校验，在Git仓库中将这些校验和保存为树对象
1.  创建一个提交对象，包含：
    *  指向这个树对象（项目根目录）的指针
    *  一个指向暂存内容快照的指针
    *  作者的姓名和邮箱
    *  提交时输入的信息
    *  指向它的父对象的指针
       >首次提交产生的提交对象没有父对象，普通提交操作产生的提交对象有一个父对象，而由多个分支合并产生的提交对象有多个父对象

现在，Git 仓库中有五个对象：三个 blob 对象（保存着文件快照）、一个树对象（记录着目录结构和 blob 对象索引）以及一个提交对象（包含着指向前述树对象的指针和所有提交信息）。
