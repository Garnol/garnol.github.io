---
title: Org-Mode个人入门学习笔记
date: 2024-09-01 10:44:40
categories:
 - 笔记
tags:
 - Emacs
 - Org-Mode
---

本文是我个人在尝试学习入门使用Emacs/Org-Mode过程中的笔记，更倾向于尝试记录我自己的学习过程，以及其中遇到的相关问题和解决方法。

理想情况下将在日后持续更新。

需要注意的是，我使用的是doomemacs，其快捷键、配置方式与传统Emacs有很大不同。

<!-- more -->

# 前言

## 为什么学习Org-Mode？

1. 我想学习使用Emacs。学习的最好方法就是实践。学习使用Org-Mode可能是一个比较好的途径，可以帮助我学习如何习惯使用Emacs、阅读文档、编写配置等等。
2. Org-Mode作为一个功能极其强大丰富，且拓展性极强的笔记模式，本身也很值得学习使用。无论是做GTD、写作、还是更进一步地“管理生活”，都很可能会有用武之地。和Emacs一起学习一举两得。

## 怎么学？

> [__Learning Emacs: Where to Start?__](https://www.reddit.com/r/emacs/comments/14k9nsy/comment/jprer3b/)
> Start by creating a list and naming the file "tasks.org" or whatever you like. Whenever you encounter anything new, a problem, or a question, add it to the list. You will slowly learn about TODOs, task scheduling, org-agenda, org-babel, etc. Do not feel overwhelmed by the amount of stuff you need to learn; simply keep adding notes. If you've never written any Lisp before, don't be repelled. I promise you, you will be soon very surpised by how powerfull and flexible Lisp can be. Maybe you even would want to learn other Lisps, like Clojure. Learn structural editing and eval commands early on.
> There is no "basic text editing in Emacs" in the traditional sense, there's no "there should be one, and preferably only one - obvious way to do it...". Instead, you build your "obvious way" of text editing to make the process efficient, simple, and gratifying for you.

参考这则建议，我决定从GTD入手。先从最基本的TODO list开始，根据使用过程中产生的不同需求，进一步学习相关的使用和配置方法。

## 学习资源

仔细查阅[Org-Mode的官网](https://orgmode.org/)就能找到大量的学习资源，包括官方的文档、教程、以及[Worg](https://orgmode.org/worg/)等。

# 最基本的TODO list

找个地方创建`task.org`文件，使用Emacs打开，以如下方式组织：

```
* Task
** TODO task1
** TODO task2
.......
```

使用`org-todo`命令切换待办状态。

然后把日常中所有遇到的问题想到的事都放进去。

## 如何调整标题的层级？

例如，最开始是这样：

```
* TODO task1
* TODO task2
.......
```

现在想增加一个Task目录，将这些TODO转变为二级标题放进Task里。

可以使用`org-refile命令`