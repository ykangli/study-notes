---
title: Git学习笔记
date: 2022-02-03 15:53:41
img: https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/2020-03-git-github-course-1024x576.jpg
---

# Git学习笔记

## Git Commit

Git 仓库中的提交记录保存的是你的目录下所有文件的快照，就像是把整个目录复制，然后再粘贴一样，但比复制粘贴优雅许多！

Git 希望提交记录尽可能地轻量，因此在你每次进行提交时，它并不会盲目地复制整个目录。条件允许的情况下，它会将当前版本与仓库中的上一个版本进行对比，并把所有的差异打包到一起作为一个提交记录。

Git 还保存了提交的历史记录。这也是为什么大多数提交记录的上面都有父节点的原因 —— 我们会在图示中用箭头来表示这种关系。对于项目组的成员来说，维护提交历史对大家都有好处。

实际操作一下，看看提交记录是怎样的。右边展示了一个（小型）Git 代码库。当前有两个提交记录 —— 初始提交 `C0` 和其后可能包含某些有用修改的提交 `C1`。

![image-20220203160058760](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203160058760.png)

此时执行 `git commit`后：

![image-20220203160141577](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203160141577.png)

我们刚才修改了代码库，并把这些修改保存成了一个提交记录 `C2`。`C2` 的父节点是 `C1`，父节点是当前提交中变更的基础。

## Git Branch

Git 的分支也非常轻量。它们只是简单地指向某个提交纪录 —— 仅此而已。所以许多 Git 爱好者传颂：早建分支！多用分支！

这是因为即使创建再多的分支也不会造成储存或内存上的开销，并且按逻辑分解工作到不同的分支要比维护那些特别臃肿的分支简单多了。在将分支和提交记录结合起来后，我们会看到两者如何协作。现在只要记住使用分支其实就相当于在说：“我想基于这个提交以及它所有的父提交进行新的工作。”

目前状态如下：

![image-20220203160301341](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203160301341.png)

接下来，我们将要创建一个到名为 `newImage` 的分支。 执行 `git branch newImage`:

![image-20220203160348072](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203160348072.png)

新创建的分支 `newImage` 指向的是提交记录 `C1`。

现在执行`git commit`:

![image-20220203160429885](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203160429885.png)

为什么 `main` 分支前进了，但 `newImage` 分支还待在原地呢？！这是因为我们没有“在”这个新分支上，看到 `main` 分支上的那个星号（*）了吗？这表示当前所在的分支是 `main`。

现在咱们告诉 Git 我们想要切换到新的分支上

![image-20220203160532244](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203160532244.png)

```
git checkout <name>
```

下面的命令会让我们在提交修改之前先切换到新的分支上

`git checkout newImage`

`git commit`

![image-20220203160619999](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203160619999.png)

这就对了！我们的修改已经保存到新的分支里了。

**注意**：在 Git 2.23 版本中，引入了一个名为 `git switch` 的新命令，最终会取代 `git checkout`，因为 `checkout` 作为单个命令有点超载（它承载了很多独立的功能）。 

如果你想**创建一个新的分支同时切换到新创建的分支**的话，可以通过 `git checkout -b <your-branch-name>` 来实现。

## 分支与合并

太好了! 我们已经知道如何提交以及如何使用分支了。接下来咱们看看如何将两个分支合并到一起。就是说我们新建一个分支，在其上开发某个新功能，开发完成后再合并回主线。

### Git Merge

咱们先来看一下第一种方法 —— `git merge`。在 Git 中合并两个分支时会产生一个特殊的提交记录，它有两个父节点。翻译成自然语言相当于：“我要把这两个父节点本身及它们所有的祖先都包含进来。”

我们准备了两个分支，每个分支上各有一个独有的提交。这意味着没有一个分支包含了我们修改的所有内容。咱们通过合并这两个分支来解决这个问题。

我们要把 `bugFix` 合并到 `main` 里

![image-20220203160846947](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203160846947.png)

此时执行`git merge bugFix`：

![image-20220203160942439](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203160942439.png)

首先，`main` 现在指向了一个拥有两个父节点的提交记录。假如从 `main` 开始沿着箭头向上看，在到达起点的路上会经过所有的提交记录。这意味着 `main` 包含了对代码库的所有修改。

还有，看见各个提交记录的颜色变化了吗？为了帮助学习理解，我引入了颜色搭配。每个分支都有不同的颜色，而每个提交记录的颜色是所有包含该提交记录的分支的颜色混合之后的颜色。

所以，`main` 分支的颜色被混入到所有的提交记录，但 `bugFix` 没有。下面咱们让它也改变一下颜色。

![image-20220203161059572](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203161059572.png)

此时执行以下命令：

`git checkout bugFix`

`git merge main`

因为 `main` 继承自 `bugFix`，Git 什么都不用做，只是简单地把 `bugFix` 移动到 `main` 所指向的那个提交记录。

现在所有提交记录的颜色都一样了，这表明每一个分支都包含了代码库的所有修改！大功告成！

![image-20220203161159155](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203161159155.png)

### Git Rebase

第二种合并分支的方法是 `git rebase`。Rebase 实际上就是取出一系列的提交记录，“复制”它们，然后在另外一个地方逐个的放下去。

Rebase 的优势就是可以创造更线性的提交历史，这听上去有些难以理解。如果只允许使用 Rebase 的话，代码库的提交历史将会变得异常清晰。

还是准备了两个分支；注意当前所在的分支是 bugFix（星号标识的是当前分支）

我们想要把 bugFix 分支里的工作直接移到 main 分支上。移动以后会使得两个分支的功能看起来像是按顺序开发，但实际上它们是并行开发的。咱们这次用 `git rebase` 实现此目标

![image-20220203161306271](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203161306271.png)

此时执行`git rebase main` :

![image-20220203161517867](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203161517867.png)

现在 bugFix 分支上的工作在 main 的最顶端，同时我们也得到了一个更线性的提交序列。

注意，提交记录 C3 依然存在（树上那个半透明的节点），而 C3' 是我们 Rebase 到 main 分支上的 C3 的副本。

现在唯一的问题就是 main 还没有更新，下面咱们就来更新它吧……

现在我们切换到了 `main` 上。

![image-20220203161632301](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203161632301.png)

此时执行`git rebase bugFix`:

![image-20220203161713269](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203161713269.png)

由于 `bugFix` 继承自 `main`，所以 Git 只是简单的把 `main` 分支的引用向前移动了一下而已。

## Git Fetch

Git 远程仓库相当的操作实际可以归纳为两点：向远程仓库传输数据以及从远程仓库获取数据。既然我们能与远程仓库同步，那么就可以分享任何能被 Git 管理的更新（因此可以分享代码、文件、想法、情书等等）。

如何从远程仓库获取数据，它就是 `git fetch`。当我们从远程仓库获取数据时, 远程分支也会更新以反映最新的远程仓库。

在解释 `git fetch` 前，我们先看看实例。这里我们有一个远程仓库, 它有两个我们本地仓库中没有的提交。

![image-20220203155737157](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203155737157.png)

此时执行 `git fetch`后，状态如下：

![image-20220203155833869](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203155833869.png)

就是这样了! `C2`,`C3` 被下载到了本地仓库，同时远程分支 `o/main` 也被更新，反映到了这一变化

### git fetch 做了些什么

`git fetch` 完成了仅有的但是很重要的两步:

- 从远程仓库下载本地仓库中缺失的提交记录
- 更新远程分支指针(如 `o/main`)

`git fetch` 实际上将本地仓库中的远程分支更新成了远程仓库相应分支最新的状态。

如果你还记得上一节课程中我们说过的，远程分支反映了远程仓库在你**最后一次与它通信时**的状态，`git fetch` 就是你与远程仓库通信的方式了！希望我说的够明白了，你已经了解 `git fetch` 与远程分支之间的关系了吧。

`git fetch` 通常通过互联网（使用 `http://` 或 `git://` 协议) 与远程仓库通信。

### git fetch 不会做的事

`git fetch` **并不会改变你本地仓库的状态**。它不会更新你的 `main` 分支，也**不会修改你磁盘上的文件**。

理解这一点很重要，因为许多开发人员误以为执行了 `git fetch` 以后，他们本地仓库就与远程仓库同步了。它可能已经将进行这一操作所需的所有数据都下载了下来，但是**并没有**修改你本地的文件。我们在后面的课程中将会讲解能完成该操作的命令 :D

所以, 你可以将 `git fetch` 的理解为单纯的下载操作。

## 偏离的工作

### 应用场景

假设你周一克隆了一个仓库，然后开始研发某个新功能。到周五时，你新功能开发测试完毕，可以发布了。但是 —— 天啊！你的同事这周写了一堆代码，还改了许多你的功能中使用的 API，这些变动会导致你新开发的功能变得不可用。但是他们已经将那些提交推送到远程仓库了，因此你的工作就变成了基于项目**旧版**的代码，与远程仓库最新的代码不匹配了。

这种情况下, `git push` 就不知道该如何操作了。如果你执行 `git push`，Git 应该让远程仓库回到星期一那天的状态吗？还是直接在新代码的基础上添加你的代码，亦或由于你的提交已经过时而直接忽略你的提交？

因为这情况（历史偏离）有许多的不确定性，Git 是不会允许你 `push` 变更的。实际上它会强制你先合并远程最新的代码，然后才能分享你的工作。

![image-20220203164347886](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203164347886.png)

此时若执行 `git push`:

看见了吧？什么都没有变，因为命令失败了！`git push` 失败是因为你最新提交的 `C3` 基于远程分支中的 `C1`。而远程仓库中该分支已经更新到 `C2` 了，所以 Git 拒绝了你的推送请求。

那该如何解决这个问题呢？很简单，你需要做的就是**使你的工作基于最新的远程分支**。

有许多方法做到这一点呢，不过最直接的方法就是通过 rebase 调整你的工作。咱们继续，看看怎么 rebase！

此时执行:

`git fetch `

`git rebase o/main`

`git push`

上面三条命令相当于 `git pull --rebase; git push`

![image-20220203164655199](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203164655199.png)

我们用 `git fetch` 更新了本地仓库中的远程分支，然后用 rebase 将我们的工作移动到最新的提交记录下，最后再用 `git push` 推送到远程仓库。

还可以用 merge 替换 rebase。

![image-20220203164347886](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220203164347886.png)

此时执行:

`git fetch `

`git merge o/main`

`git push`

上面三条命令相当于 `git pull; git push`

我们用 `git fetch` 更新了本地仓库中的远程分支，然后**合并**了新变更到我们的本地分支（为了包含远程仓库的变更），最后我们用 `git push` 把工作推送到远程仓库

**命令：**

`git pull` 就是 fetch 和 merge 的简写，类似的 `git pull --rebase` 就是 fetch 和 rebase 的简写！

