---
title: Git回滚，冲突处理，恢复修改
tags:
  - Git
  - 转载
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2023/abstract-1292261_640.jpg
categories: 操作配置
abbrlink: 61901abb
date: 2023-03-14 17:30:00
---
### 原理图

Workspace：工作区  (git add之前)
Index / Stage：暂存区  ( git add但是没有commit)
Repository：本地仓库（git commit的目的地）
Remote：远程仓库 （git push的目的地）

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/image.65sgnkagh600.webp)

关于回滚：revert 与 reset
git reset （会清除回退目标版本后的记录，慎用）
假如我们的系统现在有如下几个提交：

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/20190318113133103.6b17uopptl40.webp)

其中：A 和 B 是正常提交，而 C 和 D 是错误提交。现在，我们想把 C 和 D 回退掉。而此时，HEAD 指针指向 D 提交（5lk4er）。我们只需将 HEAD 指针移动到 B 提交（a0fvf8），就可以达到目的。

只要有 git 基础的朋友，一定会想到 git reset 命令。完整命令如下：

```js
git reset --hard a0fvf8
// 小乌龟操作： 
tortoiseGit: 显示日志，选择要回退的版本 -- 重置{branchname}到这个版本 -- 选择hard
```

此时本地项目已经回退，远端还没回退

命令运行之后，HEAD 指针就会移动到 B 提交下，如下图示：

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/20190318113119713.2aytnf8jemkg.webp)

而这个时候，远程仓库的 HEAD 指针依然不变，仍在 D 提交上。所以，如果直接使用 `git push` 命令的话，将无法将更改推到远程仓库。直接推送会提示本地分支版本比远端后，推送失败

```js
$ git push
To gitee.com:kubernete/Performance-Test-Management-demo.git
 ! [rejected]        master -> master (non-fast-forward)
error: failed to push some refs to 'git@gitee.com:kubernete/Performance-Test-Management-demo.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

此时，只能使用 `-f` 选项将提交强制推到远程仓库：

```js
git push -f {branchname}
```

采用这种方式回退代码的弊端显而易见，那就是会使 HEAD 指针往回移动，从而会失去之后的提交信息。将来如果突然发现，C 和 D 是多么绝妙的想法，可它们已经早就消失在历史的长河里了。

### git revert(推荐使用)

git revert的作用通过反做创建一个新的版本，这个版本的内容与我们要回退到的目标版本一样，但是HEAD指针是指向这个新生成的版本，而不是目标版本。

使用 git revert 命令来实现上述例子的话，我们可以这样做：先 revert D，再 revert C （有多个提交需要回退的话需要由新到旧进行 revert）：

```js
git revert 5lk4er
 
git revert 76sdeb
```

对应tortoiseGit上 操作为还原此版本做出的变更

这里会生成两个新有提交：D' 和 C'，如下图示：

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/20190318113133103.6b17uopptl40.webp)

这里只有两个提交需要 revert，我们可以一个个回退。但如果有几十个呢？一个个回退肯定效率太低而且容易出错。我们可以使用以下方法进行批量回退：

```js
git revert OLDER_COMMIT^..NEWER_COMMIT
```

这时，错误的提交 C 和 D 依然保留，将来进行甩锅的时候也有依可循。而且，这样操作的话 HEAD 指针是往后移动的，可以直接使用 `git push` 命令推送到远程仓库里。而这种做法，正是企业所鼓励的。

假如现在有三个提交，但很不巧的是，那个错误的提交刚好位于中间。如下图示：

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/20190318113142311.z7chcqoe480.webp)

这时，直接使用 git reset 命令将 HEAD 指针重置到 A 提交显然是不行的，因为 C 提交是正确的，需要保留的。先把 C 提交 及 B 提交全部回退，再使用 cherry-pick 命令将 C 提交重新再生成一个新的提交 C''，这样就实现了将 B提交回退的需求。完整的过程如下：

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2023/20190318113151214.1f4b45p744ww.webp)

通过以上对比可以发现

git reset 与 git revert 最大的差别就在于，git reset 会失去后面的提交，而 git revert 是通过反做的方式重新创建一个新的提交，而保留原有的提交。

在企业里，应尽量使用 git revert 命令，能不用 git reset 命令尽量不用。

恢复未commit 与 commit的修改

(1) git checkout

恢复某个已修改的文件（撤销未提交的修改）：

$ git checkout file-name

例如：git checkout src/com/android/.../xxx.java

比如修改的都是java文件，不必一个个撤销，可以使用

$ git checkout *.java

撤销所有修改

$ git checkout .

(2) git revert

撤销某次操作，此次操作之前和之后的commit和history都会保留，并且把这次撤销作为一次最新的提交

   * git revert HEAD                  撤销前一次 commit
   * git revert          是提交一个新的版本，将需要revert的版本的内容再反向修改回去，版本会递增，不影响之前提交的内容
   * git revert HEAD^               撤销前前一次 commit
   * git revert commit-id （比如：fa042ce57ebbe5bb9c8db709f719cec2c58ee7ff）撤销指定的版本，撤销也会作为一次提交进行保存。

### 关于冲突

 当pull出现冲突时

#### 方式一：

1.将修改项存入栈中

2.pull 拉取远程代码到本地

3.从栈中取出修改项

4.接下来diff一下此文件看看自动合并的情况，并作出相应修改。

git stash: 备份当前的工作区的内容，从最近的一次提交中读取相关内容，让工作区保证和上次提交的内容一致。同时，将当前的工作区内容保存到Git栈中。

git stash pop: 从Git栈中读取最近一次保存的内容，恢复工作区的相关内容。由于可能存在多个Stash的内容，所以用栈来管理，pop会从最近的一个stash中读取内容并恢复。

git stash list: 显示Git栈内的所有备份，可以利用这个列表来决定从那个地方恢复。

git stash clear: 清空Git栈。此时使用gitg等图形化工具会发现，原来stash的哪些节点都消失了。

#### 方式二：

1.git add *

2.git commit -m 先提交本地修改

3.git pull

4.修改冲突文件

转载自：[https://blog.csdn.net/qq_37674086/article/details/115314595](https://blog.csdn.net/qq_37674086/article/details/115314595)