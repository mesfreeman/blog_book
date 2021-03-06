> 在做开发过程中，我们经常在不同的服务器之间来回的切换的，需要经常输入 ssh -p xx xxx@xxxx 来连接服务。为了简化这个过程，其实我们可以写一个简单的脚本来实现。

注：前提服务器是通过SSH免密码登录的方式，如果不是，请看我的上一篇文章[Linux下SSH免密码登录](http://blog.dandy.fun/archives/linux-ssh-authLogin.html)

## 第一步，整理日常使用的服务器

整理出你在工作中经常使用到的服务器及它的连接方式，如下：

    ssh -p 20 double@192.168.10.123
    ssh -p 20021 double@192.168.11.111
    ssh -p 20022 double@123.156.7.71

整理好这个之后，你还要为它们每个服务器起个别名（ip地址这么长你都能记住，别名你肯定也能记住的），便于说明问题我就随便起了，分别为`server01`、`server02`、`server03`。

## 第二步，编写Shell脚本

编写 Shell 脚本，主要用到了Shell的两个方面，一是参数传递与接收，二是流程控制，会了这两你就能写出来了，是不是很简单，如下：

    # !/bin/sh

    # 一个快速连接服务器的脚本
    if [ $1 = "server01" ]
    then
       ssh -p 20 double@192.168.10.123
    elif [ $1 = "server02" ]
    then
        ssh -p 20021 double@192.168.11.111
    elif [ $1 = "server03"]
    then
        ssh -p 20022 double@123.156.7.71
    else
        echo "This server not config."
    fi

脚本大功告成，将文件命名为`go.sh`，`$1`是我们运行脚本进要传递的参数。接下来，我们将写的脚本运行一下，看是否正常，如下：

    sh go.sh server01

执行后，看一下是否能正常访问到`192.168.10.123`服务器，如果不能请 Review 上述过程。

## 第三步，将脚本添加到系统自定义命令里

将脚本添加到系统自定义命令，需要在自家的`.bashrc`文件里添加一个别名，如下：

    vim ~/.bashrc
    alias go='sh /home/double/shell/go.sh'
    :wq

说明：`alias`是自定义别名，脚本必须是全路径。

![](https://pic.dandy.fun/14940398616167.jpg)

> 以后我们就能在各个服务器里快速的切换了，go go go

## 拓展

如果我们自定义的命名有很多时，我们可以将这些命令单独放在一个文件里，然后在`.bashrc`里包含它。Shell包含文件常用的有两种方式：

1. `source 文件`，如：`source .bashrc`
2. `. 文件`，如：`. .bashrc`，注意中间有空格哦

