---
title: Mac的基本操作
cover: https://nirvana-1304092626.cos.ap-chongqing.myqcloud.com/BgImage/12.png
categories: 
- [杂记,Mac]
tags:
- zsh
- macos
---

# 1.在目录间游走



## 命令一：pwd

获取当前所在路径



## 命令二：ls（list）

列出当前目录下的所有文件

### 其他用法1：ls -l(long)

**作用：**用长格式列出来



### 其他用法2: ls -a

**作用：**列出文件（包括隐藏的文件）



## 命令三：cd

### 1.cd（不带任何参数）

**作用：**返回家目录



### 2.cd ~

**作用：**返回家目录
**Tips：**可以利用cd ~/Music快速到达该目录



### 3.enenen,其他的话相信大家已经基本了解了这里就不细说了

# 2.其他



以上讲的所有指令，都是无害指令，这里的无害指的是不会对系统文件造成破坏，但是还有一些指令，可以瞬间删掉你的整个系统，若你知道这些指令的用法那也没问题，但若是不小心根据网上的教程用了从而误删了东西就不好了。内核为了确保你知道某些指令的用法，要求你必须在这些有风险的指令前加输 `sudo`，这就是所谓的**超级管理员指令**，它会在你密码确认后，赋予 sudo 之后的命令最高权限，可以进行任何操作。



比如我需要把电脑上的 Windows 分区彻底删除时，只需要输入下面这串代码，一瞬间整个磁盘分区就会被删除：



```plain
sudo diskutil eraseVolume free none diskx
```



关联阅读：[如何彻底移除你的 Boot Camp 分区？](https://sspai.com/post/43699)



到这里为止，终端的非常基础教学就已经讲完了，这只是略过了终端海量指令中最常见的一小部分，毕竟终端控制的是整个系统的内核，其指令库非常之大，没有办法全部讲完。那么，当你在网上见到一个新命令的时候，怎么知道它该如何用？你可以使用 man 指令**查看其用法**，比如，当你见到了一个磁盘管理命令 `diskutil`，可以使用 `man diskutil`来查看这个磁盘管理命令的具体用法说明，想要退出这个说明按下键盘 `Q` 即可。



![image-20211128145500312](/Users/nirvana/Library/Application Support/typora-user-images/image-20211128145500312.png)



## 常见玩法



- **关闭崩溃报错：**有时软件崩溃就已经很让人烦心了，重新打开后一遍遍弹出的问题报告更是烦不胜烦。这时你可以使用 `defaults write com.apple.CrashReporter DialogType none` 命令关闭这个问题报告。若你希望恢复其显示，可以输入 `defaults write com.apple.CrashReporter DialogType crashreport`。



![img](https://cdn.sspai.com/2018/07/11/b3e9b723abb44e5ecd72f67bc3bc602b.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)烦人的崩溃提醒



- **修改文件日期：** 有时你可能需要修改文件创建或修改日期，这时可以使用 `touch -t 199505090000 拖入文件`命令。这句命令中的数字表示 1995 年 5 月 9 日 00:00 分，你可以根据实际需要修改。



![img](https://cdn.sspai.com/2018/07/11/c3730bdda3de29135b5ffa25ca0bb813.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)任意修改文件日期



- **不要进入休眠状态：**当你临时不希望电脑进入休眠状态时，可以使用 `caffeinate` 命令让电脑时刻清醒。当你需要其恢复正常时，按下 `⌃Control - C` 即可停止该命令。



![img](https://cdn.sspai.com/2018/07/05/492e9c629f9a436bf5eb901dcff1faec.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)caffeinate



- **程序假死需要强退：**有时候程序假死了，强行退出也没用，这时可以使用 `killall` 命令。以微信为例，若想强退它，只需输入 `killall WeChat` 即可。



![img](https://cdn.sspai.com/2018/07/05/447a3552221a9f525f7b4dc1a2f8d2d2.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)killall



- **截图保存为 JPEG：**Mac 的默认截图格式为 PNG，若你想让默认的截图保存类型为 JPEG，可以输入如下指令 `defaults write com.apple.screencapture type jpg`，结果如下图所示，以后的截图都会变成 JPEG 格式。如果你希望撤销这条指令，输入 `defaults write com.apple.screencapture type png` 即可。



![img](https://cdn.sspai.com/2018/07/05/818f36f7ac9b98b31b162f19c9117a02.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)运行命令后所有截图均为 JPEG



-  关闭截图自动阴影：
  若你使用 Mac 自带的截图，会发现每次的窗口截图均会自动添加阴影，当你需要后期处理这些图片时，这些阴影可能会对你造成困扰。你可以使用下面这串命令来关闭截图阴影： 
  当你希望重新启动阴影时，可以输入： 
  查看下方的截图，你就会发现它和本文的其他截图不同，没有阴影。 

```plain
defaults write com.apple.screencapture disable-shadow -bool true; killall SystemUIServer
defaults write com.apple.screencapture disable-shadow -bool false; killall SystemUIServer
```



![img](https://cdn.sspai.com/2018/07/05/b948cadf6dd3c69602612f84907e1fa1.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)关闭阴影后的截图



-  显示隐藏文件夹：
  在你跟着网络上的教程进行一些操作时，难免会遇到让你寻找隐藏文件夹的情况，这时你可以使用： 
  来显示所有隐藏文件夹，当你不需要再显示时，输入： 
  即可恢复隐藏。查看下图，你会发现所有隐藏文件夹全部显示出来了。 

```plain
defaults write com.apple.finder AppleShowAllFiles -bool true; killall Finder
defaults write com.apple.finder AppleShowAllFiles -bool false; killall Finder
```



![img](https://cdn.sspai.com/2018/07/05/8d1c7d61ba8f29a57daf34063c1aeebb.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)显示隐藏文件夹



-  整理程序栏：
  你也许会发现，底部的程序栏越用越乱，有时候内容多的半天找不到所需程序。这时你可以考虑为其加入几个隐藏的分界符将其归为几个区域，输入： 
  即可添加一个空白分界符，需要几个重复运行几遍命令即可。当你不需要这个白分界符了，将它从程序栏拖走即可删除。 

```plain
defaults write com.apple.dock persistent-apps -array-add '{"tile-type"="spacer-tile";}'; killall Dock
```



![img](https://cdn.sspai.com/2018/07/05/00a801ba4720b85c9723002cc81e3c39.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)为程序栏加分界符



- **重置程序栏：**当你想要重新整理底部程序栏时，有时候从默认状态开始整理也许是个好办法。这是你可以使用 `defaults delete com.apple.dock; killall Dock` 命令将程序栏恢复为电脑刚刚激活时的状态。



![img](https://cdn.sspai.com/2018/07/11/84ffb9f3656b6e204709b9f0e87ca3a7.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)恢复后的状态



-  打印机械感十足的文字：
  当你想要打出机械感十足的文字时，可以玩玩 
  这句命令，只需要将代码结尾处的文字替换为你想打印的字即可。 ![img](https://cdn.sspai.com/2018/07/11/1a6ec9b9a65d4e338c3ca111fe7ceea0.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1) 

```plain
banner -w 80 legolas.me
```



### 默认设置 defaults



上面这些 defaults 开始的指令，实际修改的是**系统默认的 Plist 表单**，这些表单管理着系统中全部程序的默认设置，上面所做的修改无非是改了某些程序的默认设置罢了。



若你想查看还有哪些可以修改，可以在访达中按下键盘 `⌥Option`，点击「前往 - 资源库」，找到 Perference 文件夹，你会发现所有的 Plist 文件均在这里，你也可以根据便好手动修改。



![img](https://cdn.sspai.com/2018/07/05/8fb18e34727a1c83ac5d8c3266b1fe7c.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)Perference 文件夹



### 文件格式转换 textutil



textutil 是一个系统自带的，用于**处理文稿**的命令，其中我最常用的功能是其下属功能 convert，这句指令允许你将任何文件，在以下文件格式中互相转换 txt, html, rtf, rtfd, doc, docx, wordml, odt, webarchive。



若你手头有一篇 DOCX 文件需要被转换成 TXT，则可以输入 `textutil -convert txt 文件路径`，这句代码中的 txt 处，可以替换为任何你需要转换到的文件格式，文件路径则可以采用拖拽文件到终端的方法自动填充。



![img](https://cdn.sspai.com/2018/07/05/ee2b43c55114de065c1f4ecd47c19f12.gif)textutil -convert



### 磁盘处理 diskutil



磁盘处理也是常用的命令行操作之一，因为其命令需要根据具体情况具体对待，所以我只简单介绍其中无风险的两个，下方列举的其余操作均会直接处理磁盘内容，请务必清楚你在做什么，然后再输入命令。



若你的电脑采用的是 APFS 磁盘分区，则应使用 `diskutil apfs` 开头的命令；若你的电脑采用的是 HFS，HFS+ 磁盘分区，则应使用 `diskutil` 开头的命令；若你的电脑采用的是 coreStorage 磁盘分区，则应使用 `diskutil cs`开头的命令。



`diskutil list` 命令会将你的**现有磁盘状况**显示出来，如下图所示。



![img](https://cdn.sspai.com/2018/07/05/05f852dabb8a591d838472e03584436f.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)diskutil list



`diskutil cs list` 命令会将你的**现有的 Core Storage 逻辑分区状况**显示出来，如下图所示。



![img](https://cdn.sspai.com/2018/07/05/07d029fbc8cf7334d8acb817c4ff26c8.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)diskutil cs list



### 常用但有风险的命令 diskutil



- 查看分区上限：sudo diskutil resizeVolume /dev/disk1s3 limits
- 重置空间大小：sudo diskutil resizeVolume /dev/disk1s2 100GB

- 断开驱动器：sudo diskutil unmountDisk force /dev/disk1
- 彻底移除逻辑磁盘: diskutil unmount /Volumes/Macintosh\ HD

- 显示 GUID 分区结构： gpt -r show /dev/disk1
- 删除 EFI NO NAME：gpt remove -I 4 /dev/disk1

- 新增存储区块：gpt add -I 3 -b 1362424032 -s 1269536 -t 426F6F74-0000-11AA- AA11-00306543ECAC
- 新增分区：newfs_hfs -J -v “Recovery HD” /dev/disk0s3

- 物理 Core Storage 扩容: diskutil cs resizeDisk 11111111-2222-3333-4444-555555555555 980g
- 逻辑 Core Storage 扩容: diskutil cs resizeVolume 11111111-2222-3333-4444-555555555555 980g



### 自动安装 brew



Mac 有个很方便的**包管理器**，名叫 [Homebrew](https://brew.sh/)，它允许你自动安装许多应用程序。Homebrew 不是 terminal 的自带内容，需要运行这串命令来进行安装：



```plain
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```



![img](https://cdn.sspai.com/2018/07/11/22bff2670beeb76a7afc5ada6263ef85.png?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)使用 brew 命令前需要安装 Homebrew



安装完成后你可以直接使用 `brew cask install 软件英文名` 等命令来快速安装需要的软件。Homebrew 在少数派已经有不少介绍了，因此这里不再详述。



关联阅读：[9 条进阶命令，把 HomeBrew 打造成第三方应用的 App Store](https://sspai.com/post/43451)；[Mac 上高质量的开源应用，你可以在这里一次找到](https://sspai.com/post/45554)



### 文件删除 rm



这一指令本是 **删除文件** 用的，但正因为它可以不分敌我的删除任何文件，新手误用有极大风险，因此我不打算详细介绍这个。网上有一个臭名远扬的传言，欺骗别人用 `sudo rm -rfv /Cool` 来删除系统文件，从而使整个系统崩溃，这条命令绝对不要尝试。



我们来用前面学到的知识分析一下为什么这句命令会使得系统崩溃。首先 sudo 表示管理员命令并获取最高权限，`rm` 表示删除文件，用 `man rm` 命令查看手册，可以发现 -r 是用来修饰 rm 命令的，表示删除当前工作路径下的全部子文件，-f 也是修饰 rm 命令的，表示无视被保护的文件依旧执行删除。



前文在讲默认路径时提到，打开终端之后默认工作路径正是当前用户的根目录，因此执行 `sudo rm -rfv /Cool` 后，会从根目录开始，无视被保护的文件并且删除当前路径下的所有文件及子文件夹，包括系统文件，这样一来系统就废了。



既然谈到了这个代码的破坏力，我索性用与 Mac 同源的 fedora 系统在虚拟机上跑了一遍，下图是跑代码前：



![img](https://cdn.sspai.com/2018/07/06/62c6b970cecd5ce980645abf84604fc2.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)虚拟机 fedora 中运行破坏代码



下图是跑代码后，遇到了一个小问题，下面这张截图已经是在 Mac 上了。你仔细观察可以发现截图里的复制窗口前面的图标损坏了，这是因为当我在虚拟机中运行此破坏代码时，Parallel Desktop 实际并没有将 fedora 系统独立于沙盒中。



半分钟后我发现了这个问题但为时已晚，这个小实验直接破坏了我正在使用的 Mac 系统并造成超过 100 GB 文件丢失，许多系统软件已无法正常运行，万幸访达等核心功能还可以正常工作。为了修复这个错误，我不得不评估损失，备份文件以及重装系统。



![img](https://cdn.sspai.com/2018/07/06/96b876147c406c58dbc028c8d0cbb386.jpg?imageView2/2/w/1120/q/90/interlace/1/ignore-error/1)直接对 Mac 造成无法挽回的破坏



至于什么是**沙盒**，什么是**虚拟机**，这是另一个话题了，若你有兴趣可以在本文下方评论。简单来说，虚拟机可以在在工作电脑中模拟一个独立运行环境，使用它，可以允许你在一个系统的同时运行另一个系统。沙盒是一种保护机制，保证了当前在虚拟机中运行的任何内容不会影响工作机本身。



这个在虚拟机 Fedora 中运行的破坏代码的实验本来不应该破坏我正常使用的 Mac 系统，最终导致 Mac 被破坏时由于 Parallel Desktop 这个软件默认虚拟机不开启沙盒模式导致的惨剧。



### 其它命令



当然命令远不止上面所提到的这些，常见的还有 `cp`复制文件； `mv` 移动文件； `mkdir` 创建目录； `cat` 显示文件内容等等，这里就不再一一介绍了。若你有兴趣可以阅读下面这篇文章，里面有每个命令的详细介绍。



关联阅读：[每天一个linux命令](https://www.cnblogs.com/peida/archive/2012/12/05/2803591.html)



# 3.iTerm2



早就听说这个终端工具了，可以完全替代terminal。



## 配色



[Solarized](http://ethanschoonover.com/solarized), 是目前最完整的 Terminal/Editor/IDE 配色项目，几乎覆盖所有主流操作系统（Mac OS X, Linux, Windows）、编辑器和 IDE（Vim, Emacs, Xcode, TextMate, NetBeans, Visual Studio 等），终端（iTerm2, Terminal.app, Putty 等）。类似的项目还有 [Tomorrow Theme](https://github.com/chriskempson/tomorrow-theme)。



拿tomorrow-theme举例，下载Tomorrow-Night-Eighties.itermcolors文件，双击自动导入到iTerm2中，在Perferences->Profiles->Colors->Load Presets中可以看到对应的配色。修改即可。



## 中文乱码问题



确保Preferences->Profiles->Terminal->Terminal Emulation中的字符编码为UTF-8。



中文乱码的问题需要设置一下locale， 在对于的shell配置文件中，比如bash对应的就是`~/.bashrc`， zsh对应的就是`~/.zshrc`, 这里以zsh为例，打开.zshrc文件，修改其中`# You may need to manually set your language environment export LANG=en_US.UTF-8`：



```plain
# You may need to manually set your language environment
export LANG=en_US.UTF-8
```



接着重启一下终端，或者输入: `source ~/.zshrc`。



## 一些快捷操作



- `command+方向键`: 切换tab。
- `command+enter`: 全屏模式。

- `command+f`: 搜索，支持正则表达式。
- `command+d`: 垂直分屏。

- `command+shift+d`: 水平分屏。
- `command+[ 或 command +]`: 在最近使用的分屏直接切换。

- `command+t`: 打开新标签。
- `command+w`: 关闭新标签。

- `command+;`: 自动补全历史命令。
- `command+r`: 清除屏幕，相当与clear.

- `command+p/n`: 上一条/下一条命令，相当于方向键上和下。
- `ctrl+r`: 搜索命令历史。



### 编辑操作



基本的Emacs移动光标方式。还有一些很好的操作方式，我觉得都可以借鉴配置到SublimeText中。



- `ctrl+d`: 删除当前字符。
- `ctrl+h`: 删除之前的字符。

- `ctrl+u`: 删除整行。
- `ctrl+k`: 删除当前到文本末尾的字符。

- `ctrl+w`: 删除光标前的单词。
- `ctrl+t`: 交换当前光标和前一个位置，互换。

# 4.zsh



zsh是shell语言类型，兼容bash，提供强大的命令行功能，比如tab补全，自动纠错功能等。缺点就是配置太麻烦，好在有一个叫做`oh-my-zsh`的开源项目，很好的弥补了这一缺陷，只需要修修改改配置文件，就能很顺手。



## 安装zsh



安装方式我使用:`brew install zsh`。



替换bash的方式：`chsh -s /bin/zsh`。关闭终端，再次打开即为zsh。



注意：之前我们使用bash，我们为了使用brew安装的软件，修改了`~/.bash_prorile`文件，新的zsh自己也有配置文件，是`~/.zshrc`，需要将配置拷贝到`~/.zshrc`中。



或者在安装完oh-my-zsh后，执行`echo export PATH='/usr/local/bin:$PATH' >> ~/.zshrc`。



### oh-my-zsh



由于zsh的配置是很复杂的，所以有这个一个开源项目[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh?source=c)，帮助我们简化zsh的配置。



官网有自动安装的方法，我选择的是: `curl -L http://install.ohmyz.sh | sh`。



安装完成后，重启终端就能看到界面的变化了。zsh的配置文件是`~/.zshrc`，配色对应的是`ZSH_THEME`.



### zshrc



zshrc是zsh的配置文件，我会在此添加一些alias设置。比如:



```plain
alias st='open -a "Sublime Text"'
```



### oh-my-zsh插件



oh-my-zsh的强大之处还在于提供了完善的插件系统。相关的文件存储在`~/.oh-my-zsh/plugins`中，默认提供了100多种。。。



默认提供的插件是git,需要添加的话，修改`~/.zshrc`中`plugins=(git autojump)`即可。



#### 自动跳转



[z](https://github.com/rupa/z)和[autojump](https://github.com/joelthelion/autojump)。是两个可以实现自动跳转的插件，都是可以通过brew下载的。



我目前使用的是`autojump`，通过`brew install autojump`下载，并且在`~/.zshrc`中修改`plugins=(git autojump)`。重启终端。



使用就可以使用j来代替cd命令了，并可以添加自定义目录，具体使用说明参考autojump的文档或者`autojump --help`。



#### fasd



[fasd](https://github.com/clvv/fasd), 功能上和z, autojump差不多，功能和速度上更优。它会按照访问的频率记录下文件，帮助用户快速访问。



安装还是通过brew: `brew install fasd`，安装之后，我安装官网的步骤，执行了:`eval "$(fasd --init auto)"`, 且在zshrc中开启对应的配置：`plugins=(git fasd)`, 重启终端即可使用。



asd comes with some useful aliases by default:



```plain
alias a='fasd -a'        # any
alias s='fasd -si'       # show / search / select
alias d='fasd -d'        # directory
alias f='fasd -f'        # file
alias sd='fasd -sid'     # interactive directory selection
alias sf='fasd -sif'     # interactive file selection
alias z='fasd_cd -d'     # cd, same functionality as j in autojump
alias zz='fasd_cd -d -i' # cd with interactive selection
```



Example:



```plain
f foo           # list frecent files matching foo
a foo bar       # list frecent files and directories matching foo and bar
f js$           # list frecent files that ends in js
f -e vim foo    # run vim on the most frecent file matching foo
mplayer `f bar` # run mplayer on the most frecent file matching bar
z foo           # cd into the most frecent directory matching foo
open `sf pdf`   # interactively select a file matching pdf and launch `open`
```