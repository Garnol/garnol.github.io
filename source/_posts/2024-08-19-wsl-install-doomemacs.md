---
title: WSL2 Ubuntu环境下安装doomemacs笔记
date: 2024-08-19 15:56:00
categories:
 - 笔记
tags:
 - Emacs
---

基本目标是在Windows 11上安装doomemacs，同时令其有较好的中文支持以便于日常写作。

<!-- more -->

在Windows上有多种方法可以安装doomemacs：

|安装方式|优点|缺点|
|---|---|---|
|直接在Windows上安装|最方便，emacs与Windows集成度最高|emacs在Windows上性能较差；__doomemacs的doom脚本对Windows的支持很差__ |
|使用wsl2|比较方便地在Windows上使用Linux，且文件互通|wsl2的一些方面还不完善，需要繁琐的配置；中文输入是个问题
|虚拟机|最省心的emacs环境|文件互通麻烦，使用体验存疑|

考虑到与Windows的集成、Emacs的性能与后续管理更新的便捷，最后选择了在wsl2上的Ubuntu 22安装doomemacs。

参阅doomemacs的[安装教程](https://github.com/doomemacs/doomemacs/blob/master/docs/getting_started.org)与安装过程中的提示信息就可以解决一些问题。基本使用、插件管理等方面可以参考[DT的视频](https://www.youtube.com/watch?v=37H7bD-G7nE)。

下面是遇到的一些场外问题。

# 更新Ubuntu到最新的LTS版本

最新的Ubuntu LTS（本文写作时是24）软件仓库中的软件和库版本更高，可以解决不少问题。

# 安装较新版本的Emacs

**参考上一条，最新的Ubuntu LTS的软件仓库提供了Emacs 29，以下内容可以忽略了**

Ubuntu官方源不提供较新版本(28.x, 29.x)的Emacs，需要另寻他路。

~~最简单的方法是用snap：~~

```
snap install emacs --classic
```

~~但snap需要运行snapd服务，这需要systemd或其替代。WSL只在近期才引入了systemd，可能需要[手动打开](https://learn.microsoft.com/en-us/windows/wsl/systemd#how-to-enable-systemd)。~~

本文成文时，wsl2中启用systemd的话会在后续使用GUI界面时产生问题。所以最后选择了手工构建emacs安装，这样的好处是可以加上`--with-native-compilation`提高性能。

可以参考[这个脚本](https://gitlab.com/mslot/src_installs/-/blob/master/emacs_install_ubuntu.sh)，基本解决了构建过程中我遇到的问题。考虑到网络问题，也可以选择不用git，而是去emacs官网直接下载源码。

更详细的教程可以参考emacs wiki的[Gcc Emacs](https://www.emacswiki.org/emacs/GccEmacs)这篇文章及其相关的文档。


# git的证书问题

~~具体原因不明，总之在git clone的时候会出现`server certificate verification failed. CAfile: none CRLfile: none`的错误。包括在`doom install`的时候。~~

~~解决方法是关掉验证：~~

 1. ~~设置环境变量`GIT_SSL_NO_VERIFY=true`~~
 2. ~~`git config http.sslVerify "false"`~~

应该是因为`doom`[没有使用系统的git配置](https://discourse.doomemacs.org/t/installation-on-windows-behind-a-proxy-fails/3234)，可以通过设置环境变量令其使用指定git配置

> Doom intentionally ignores system git config. To bypass this, set `DOOMGITCONFIG` to the location of the git config file you want it to use.
> `DOOMGITCONFIG=~/.config/git/config doom sync`

# GUI卡顿问题

使用GUI运行时，发现有十分明显的卡顿情况。缓解方法可以参考Github上的[这个Issue](https://github.com/doomemacs/doomemacs/issues/2217)：

1. ~~使用`(setq display-line-numbers-type nil)`移除行号~~，但行号还是比较重要的，这条pass。
2. 将`init.el`中的`modeline`换为轻量的`(modeline +light)`
3. 使用更适合emacs的字体。doomemacs官方推荐Fira Code。`config.el`中提供了字体配置模板。记得先安装字体。

# 中文显示问题

wsl默认没有配置中文语言环境，因此GUI上的中文会显示乱码。需要先配置一下中文环境并安装中文字体。

字体的安装可以直接复制Windows的字体：

```
sudo cp /mnt/c/path/to/VictorMono.ttf /usr/local/share/fonts/
sudo fc-cache -fv
```

或者直接建个软连接之类的：

```
sudo mkdir /usr/share/fonts/win11 # to differentiate self-built font links from system font files 
sudo ln -s /mnt/c/Windows/Fonts/* /usr/share/fonts/win11
```

# 剪切板互通问题

WSL的emacs GUI跟Windows剪切板不互通，这点可以安装emacs的`xclip`插件解决。原理应该是在WSL中调用Windows的powershell获取剪切板内容。

注意如果开启systemd的话，可能会导致WSL无法调用Windows的可执行程序。

# 中文输入问题

wsl的emacs有几种方法可以输入中文：

1. 在终端上打开时，可以直接通过终端输入。
2. 使用emacs的中文输入法插件。doomemacs提供了`chinese`模块。
3. 在wsl上安装输入法。

由于我个人想用GUI，因此需要在2和3之间挑一个。考虑到3很麻烦，而且wsl的支撑程度与美观上都存疑，还是用2吧。

doomemacs官方提供的chinese模块没有维护且安装较麻烦，因此选择安装`emacs-rime`配合`posframe`实现中文输入。可以直接参考`emacs-rime`的[文档](https://github.com/DogLooksGood/emacs-rime?tab=readme-ov-file)

## 基本配置

需要注意的是，rime需要能打开功能选单来实现简繁切换、输入法切换等实用功能。但功能选单默认的快捷键``C-` ``已经被`doomemacs`的`popup`模块占用了，需要换个快捷键。例如：

在rime的配置文件`default.custom.yaml`中加入：

```
patch:
  schema_list:
    - schema: luna_pinyin
    - schema: pinyin_simp
    - schema: terra_pinyin
  menu/page_size: 7 # 每页显示7个候选字词。
  switcher:
    hotkeys:
      - Control+apostrophe # 更换为C-'
```

在doom的`config.el`中加入：

```
;; 在默认值的基础上加入了C-'
(setq rime-translate-keybindings
  '("C-f" "C-b" "C-n" "C-p" "C-g" "C-'" "<left>" "<right>" "<up>" "<down>" "<prior>" "<next>" "<delete>"))
```

然后重新部署rime并重新启动emacs，即可在输入法输入时使用`C-'`打开功能选单了。

## 安装更好的输入法

**本节默认了在Windows和Emacs中同时使用rime，因此更多地考虑了两者如何互通。如果只在Emacs中使用rime的话，可以忽略掉Windows/小狼毫相关的内容。**

rime默认提供的输入法较为简陋，可以安装配置和词库更好，且开箱即用的[雾凇](https://github.com/iDvel/rime-ice)或其衍生的[白霜](https://github.com/gaboolic/rime-frost)。

rime的配置分为[共享文件夹和用户文件夹](https://github.com/rime/home/wiki/Configuration#%E4%BD%8D%E7%BD%AE%E5%8F%8A%E7%B5%84%E7%B9%94%E6%96%B9%E5%BC%8F)，大致就是全局配置和本地配置。考虑到wsl与Windows在文件系统上的集成，可以将上述输入法放在Windows的全局配置路径下，然后基于此在wsl/Emacs和Windows上分别再定制配置。

### 安装

Windows上，小狼毫的共享文件夹是`小狼毫安装目录\data`。上述两个输入法任选其一将其放进去即可。

### wsl上的配置

首先确保`emacs-rime`的依赖`librime-dev`版本足够高（1.8.5以上），且安装了lua插件`librime-plugin-lua`。这是因为雾凇[需要lua脚本实现功能](https://github.com/iDvel/rime-ice/issues/840#user-content-fnref-1-367eb9c824fd743cc94da8bf428b4107)。

然后，参考[官方文档](https://github.com/DogLooksGood/emacs-rime/tree/master?tab=readme-ov-file#%E6%8C%87%E5%AE%9A-rime-%E5%85%B1%E4%BA%AB%E7%9B%AE%E5%BD%95%E5%92%8C%E7%94%A8%E6%88%B7%E7%9B%AE%E5%BD%95)，将`emacs-rime`使用的共享目录设置为Windows小狼毫的共享文件夹。当然，也可以选择在默认的共享文件夹再安装一份输入法。

最后，在用户文件夹添加自定义的配置，应参考[官方文档](https://github.com/rime/home/wiki/CustomizationGuide)。例如，可以在`default.custom.yaml`中添加：

```
patch:
  schema_list:
    - {schema: melt_eng}
    - {schema: rime_ice}
  menu/page_size: 7
```

### 词库同步

参考官方文档的[这一节](https://github.com/rime/home/wiki/UserGuide#%E5%90%8C%E6%AD%A5%E7%94%A8%E6%88%B6%E8%B3%87%E6%96%99)，可以让`emacs-rime`和小狼毫使用同一同步路径，以方便地在两者之间同步词库。