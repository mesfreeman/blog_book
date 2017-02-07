### homebrew

homebrew是Mac OSX上的软件包管理工具，类似于Red hat下的`yum`,Ubuntu下的`apt-get`，有了它就能还方便的在mac上安装和卸载软件。

### 安装homebrew

安装homebrew很是简单，打开mac平台终端软件，运行如下命令：

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    
等待安装结束，大概会出现如下现象（有可能时间会长一些）

    ==> This script will install:
    /usr/local/bin/brew
    /usr/local/Library/...
    /usr/local/share/doc/homebrew
    /usr/local/share/man/man1/brew.1
    /usr/local/share/zsh/site-functions/_brew
    /usr/local/etc/bash_completion.d/brew
    
### 使用方法

运行如下命令

    brew --help

就可以看到它的使用说明了，常用的有：

* brew search 软件名：查找该软件
* brew install 软件名：安装该软件
* brew remove 软件名：删除该软件

注：如果找不到你需要的软件包时可以运行`brew update`或`brew upgrade`，来更新一下软件包看是否能找到。顺便说一下`update`与`upgrade`的区别：前者可以翻译为更新，在原有的基础上增加新的功能，或者对已有的功能进行改善。后者可以翻译为升级，增加一些原来没有的东西。可以理解为`update`是量上面的变化，而`upgrade`是质上面的变化。

### 实例

    // 安装rz/sz
    DoubledeMacBook-Pro:double$ brew search lrzsz
    lrzsz ✔
    brew install lrzsz
    …………