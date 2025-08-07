---
title: git 常用操作整理
categories:
  - 代码笔记
tags:
  - git
archive: false
hide: false
date: 2025-08-08 01:14:20
---
## **Github 上提交 pr 操作**

  

1. 先fork仓库到自己仓库

2. 拉取自己仓库代码

3. 拉出自己的开发分支

4. 【**重点**】开发完成后，保证自己的分支只提交过一个commit（这里的commit指的是自己提交的coommit，多的commit则要进行压缩合并操作）

5. 【**重点】**先同步远程仓库代码，也就是上自己仓库中找到同步的远程仓库，点一下**“sync”**，拉取自己仓库的主分支代码（这里的主分支默认为main），保证主分支代码是最新的

6. 【**重点】**然后进行 rebase 操作，进入到自己的本地开发分支下面，执行 `git rebase main`,这样会把自己的开发分支基于main分支再映射一遍，可能会有冲突，如果到这里没有冲突，就可以提交到自己远程仓库，发起pr操作

7. 【**重点】解冲突：**这里执行完rebase后，如果有冲突编辑器会按顺序让你确认需要提交的最终文件，假如a.go、b.go、c.go这3个文件有冲突，则先对a.go进行解冲突，解完后执行 `git add .` ,将解完的冲突提交到暂存区，执行 `git rebase --continue` 继续解下一个冲突, 按这样解完所有的冲突后就可以执行 push 动作，接着就可以到远程操作提交自己的pr，描述下自己的更改都做了什么了！

  

## **追加文件修改到上一次提交**

  

1. 修改你需要追加的文件

2. 执行 `git add .`

3. 执行 `git commit --amend` 后进入修改上一次的commit message页面，修改完成后wq后保存退出

4. 如果不需要修改上一次的commit message，则直接执行 `git commit --amend --no-edit`

  

## **代码回滚**

  

- **reset**：将当前的 HEAD 指针指向该 commitID，并将**该 commit 之后的修改的文件**移动到**工作区**
- **reset --hard**：【注意】将当前的 HEAD 指针指向该 commitID，**撤销该 commit 之后的所有文件更改**
- **reset --soft**：将当前的 HEAD 指针指向该 commitID，并将**该 commit 之后的修改的文件移动到暂存区**修改完后查看 commit 没问题后执行 `git push -f` 强制推送到远端
- **revert**：还原某次提交的文件到**该次提交前的状态**
- 回滚指定 commit 中的文件，场景：很早之前的一个commit中的一个文件发现修改错了，想要检出来重新提交

```Shell
git checkout <commit-hash> -- <file-path>
```

  

## **压缩提交的两种方式**

  

1. 使用**代码回滚**中的 **reset --soft** 到指定要压缩的 commit 的前一个commit，这样所有的更改都会到暂存区，再重新编辑提交信息后进行提交。

2. 使用 `git log` 查看要压缩的所有的提交的**前一个commit**,复制该commit ID，然后执行`git rebase -i [commit ID]`,进入压缩界面，将第一行的pick留下，其他行改为小写的s，然后wq保存退出，进入更改commit message界面，修改成你的message，然后wq保存退出，就ok了。

  

## **合并冲突**

  

## **合并过程中遇到冲突但是不想处理冲突时需要退出**

  

执行 `git merge --abort`

  

## **检出 a 分支上的提交到 b 分支**

  

假设我们现在需要把 a 分支上的一些提交（如哈希值为 32 到 35 的提交）检出到 b 分支：

```Shell
// 首先我们切换到 b 分支
$ git choutout b

// 如果想搞成[]区间，使用 git cherry-pick 32^..35 相当于[32 35]包含32
$ git cherry-pick 32^..35
```

  

`git cherry-pick --continue`：用户解决代码冲突后，第一步将修改的文件重新加入暂存区（git add .），第二步使用下面的命令，让 Cherry pick 过程继续执行。

`git cherry-pick --abort`：发生代码冲突后，放弃合并，回到操作前的样子。

`git cherry-pick --quit`: 发生代码冲突后，退出 Cherry pick，但是不回到操作前的样子。

  

📝参考：[https://blog.csdn.net/muzidigbig/article/details/122321393](https://blog.csdn.net/muzidigbig/article/details/122321393)

  

## 合并分支时忽略空白行的冲突(使用频率低）

  

- Xignore-all-space: 在比较行时 **完全忽略** 空白修改
- Xignore-space-change:将一个空白符与多个连续的空白字符视作等价的

```Shell
$ git merge -Xignore-space-change [branch name]
```

  

## **查看当前所有目录文件的 sha-2 值**

  

```Shell
git ls-files -s
```

📝参考：

[https://blog.csdn.net/weixin_44154094/article/details/114337077](https://blog.csdn.net/weixin_44154094/article/details/114337077)

[动图展示 10 大 Git 命令，让你轻松掌握Git-CSDN博客](https://blog.csdn.net/weiwosuoai/article/details/141179308?spm=1001.2014.3001.5501)