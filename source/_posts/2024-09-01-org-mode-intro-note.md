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

实际上，大多数问题都可以通过Org-Mode的文档解决，只是个人目前没有时间精力通读文档，因而也只能用到在翻，必然会有些缺漏之处。

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

# 不要滥用TODO

虽然是后加的，但是应该放第一个。

起因是我希望能够将“每天在某个时间段学习”这样的任务放进TODO list里。然而在`SCHEDULE`上如何设置多个时间段方面犯了难。这时候刚好看到文档8.3节提到：

> **Important**: Scheduling an item in Org mode should not be understood in the same way that we understand scheduling a meeting. Setting a date for a meeting is just a simple appointment, you should mark this entry with a simple plain timestamp, to get this item shown on the date where it applies. This is a frequent misunderstanding by Org users. In Org mode, scheduling means setting a date when you want to start working on an action item.

即，配合`SCHEDULE`的TODO适合用于“在特定时间直接进行具体行动”的任务，而对于我所需求的“在某个时间段学习”这样的任务，完全可以不作为TODO，而是普通的条目直接加个时间戳就行：

```
* 学习
<2024-09-14 Sat 08:00-12:00 +1d>
<2024-09-14 Sat 14:00-18:00 +1d>
```

比如这样。`org-agenda`中也会记录这样的安排。

这也许是因为从GTD作为切入点学习Org-mode，导致下意识地把各种任务都想作为TODO处理，有些思维定势了。

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

## 如何自动记录完成时间？

参考文档的`5.3.1`节。

# 习惯与org-agenda

可以用org-mode记录习惯。

~~参考官方文档的`8.3.2`节，可以为TODO添加不断重复的DEADLINE，这样就能作为习惯记录使用。~~

参考官方文档`5.3.3`，给出了一个较为详细的习惯设置步骤。

这时就需要`org-agenda`来组织各种TODO形成日程安排。`org-agenda`可以收集各个org文件中的项目，将其组织在一起进行管理。

使用`org-agenda`之前，需要将要管理的org文件添加到`agenda files`中。可以参考官方文档的`11.1`节，将单个文件加入；也可以在配置文件中指定默认查找的路径：

```
(setq org-directory "~/org/")
```

然后参考官方文档的`11.3.1`节，打开Weekly/Daily agenda，即可实现日程安排。