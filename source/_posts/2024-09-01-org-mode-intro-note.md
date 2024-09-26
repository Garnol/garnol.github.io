---
title: Org-Mode与GTD个人入门学习笔记
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

# TODO组织与GTD

当积攒了不少TODO后，一个重要的问题是，如何组织这些待办事项，以更好地管理与执行。这就需要一些GTD的思想了。

请注意：以下只是个人的理解。归根到底，这方面没有最优，只有最适合。

本质上来说，可以把GTD看作一种对待办事项的分类方法。其基本流程为：

> __Capture__ anything that crosses your mind, nothing is too big or small.
> __Clarify__ what you’ve captured into clear and concrete action steps.
> __Organize__ and put everything into the right place.
> __Review__, update, and revise your lists.
> __Engage__ Get to work on the important stuff.

## 个人的工作流

### Inbox

将日常生活中，遇到的要做的事、想做的事、乃至片段的灵感等等，都放进一个Inbox的列表中，以供后续整理。

### 分类

将事务分为`Reference`, `Action`, `Calendar`, `Habit`, `Someday`几类。通过以下问题进行分类：

#### 该事务是可执行的吗？

如果不是，放入`Reference`。

例如，“《空之境界：矛盾螺旋》的演出手法很有意思”就可以算作`Reference`；而“看《空之境界：矛盾螺旋》”则是可执行的事务。

#### 可以直接去做吗？

如果认为事务不能直接去做，需要一些前置条件等，则将其放入`Someday`；否则放入`Action`。

例如，在写到此处时，我想玩《异星工厂》，但是想等一两个月后其DLC发售再开始玩，那么我就可以将“玩《异星工厂》”放入`Someday`。

#### 有时间要求吗？

对于需要在特定时间执行的事务，将其放入`Calendar`；对于习惯类、需要反复执行的事务，将其放入`Habit`

#### “下一步”是什么？

对于`Action`中的项目，为了完成这件事，所需要、可以直接着手去做的、不会花费过多的时间精力的“下一步”是什么？

如果“下一步”可以直接完成这件事，将其标为TODO；否则，将事务标为PROJ，在其子项目中加入“下一步”作为TODO。

例如：

```
* Action
** TODO 在博客中添加GTD相关的内容 :Writing:
** PROJ 通关《黑神话：悟空》:Game:
*** TODO 玩《黑神话：悟空》，打败虎先锋
```

需要注意的是，对“下一步”的定义是灵活的。如果感到“读完一本书”作为下一步耗费时间太多，可以考虑将其作为项目，拆解为“读一章”、“读一页”等等。

对于`Action`中的项目，可以再根据其所需的环境、精力、必要工具等客观条件进行分类。我使用Org-Mode的tag，为各项TODO加上`Work`, `Novel`, `Game`, `Writing`等标签，大致区分执行所需的时间与精力，以方便在执行时进行挑选。

### 整理

每隔一定时间，比如每天一次，对各个类别的事务进行整理：

- `Inbox`：按照上述方法分类。
- `Reference`：看看是否有引起兴趣，可以转化为行动的。
- `Action`：清除已完成事务。对于多步的项目，如果缺少“下一步”，为其补上。
- `Someday`：看看是否可以着手去做了。

### 执行

最重要的一步。将待办事项组织整理就是为了更方便地去执行。时常打开agenda去做吧。

## 配置

在实际执行时，需要用agenda更好地组织展示各项TODO。需求为：

1. 能够展示当天的日程。
2. 展示所有TODO，排除习惯与PROJ项目。
3. 在TODO前展示其标签，表示分类，并且按标签进行排序。

为此需要定义一个新的视图：

```
(setq org-agenda-hide-tags-regexp ".+")  ;; 隐藏默认的标签显示
;; GTD视图，显示本日日程和所有的TODO项目
(setq org-agenda-custom-commands
      '(("t" "Today's schedule and TODOs (without habits)"
         ((agenda "" ((org-agenda-span 'day)  ;; 仅显示当天
                      (org-agenda-start-day "+0")  ;; 从今天开始
                      (org-deadline-warning-days 0)))  ;; 不显示即将到期的项目
          (todo "TODO"
                ((org-agenda-overriding-header "Next")
                 (org-agenda-prefix-format "%-12:T")  ;; 将标签显示在前面
                 (org-agenda-skip-function
                  '(org-agenda-skip-entry-if 'regexp "habit"))  ;; 排除习惯
                 (org-agenda-sorting-strategy '(tag-down))))  ;; 按标签排序
          ))))

```
