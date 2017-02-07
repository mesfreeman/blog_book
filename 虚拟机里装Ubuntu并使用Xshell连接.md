### 装Ubuntu系统

从网下自行下载一个Ubuntu系统镜像（也可以点击[这里](https://yunpan.cn/cBheNWdvEjwbb)下载，访问密码 c449）。然后在VM虚拟机里新建一个虚拟机，安装过程很简单在这里就不多说了，不会的自行百度。

安装成功之后会看到如下界面：

![界面](http://i.imgur.com/7NhyRY7.jpg)


### 使用Xshell连接Ubuntu

说明：

前提保证两者在同一个网段中，并互相能够ping通。

查看该虚拟机的ip地址：

1. 按下`ctrl + alt + F1`进入系统命令行界面，如下图所示：

    ![命令行界面](http://i.imgur.com/DVbUuoj.jpg)

2. 在命令行界面输入`ifconfig -a`查看虚拟`ip`地址，如下图所示：

    ![ip](http://i.imgur.com/NePwun0.jpg)

用Xshell连接虚拟机：

1. 打开Xshell，新建会话，如下图所示：

    ![会话](http://i.imgur.com/39h9UdP.jpg)

2. 连接虚拟机，如果出现下面的情况，可能是因为没有安装或开启SSH服务

    ![错误情况](http://i.imgur.com/jDe4htY.jpg)

3. 安装SSH服务
   
    在命令行界面输入`sudo apt-get install -y openssh-server`回车。

4. 开启SSH服务

    在命令行界面输入`sudo service ssh start`回车。

5. 查看是否开启成功

    在命令行界面输入`ps –e | grep ssh`回车，如果看到`6455 ? 00:00:00 sshd`，表示开启成功。 

6. 尝试再次连接，如果出现如下界面，则表示连接成功。

    ![连接成功](http://i.imgur.com/pGaqhTX.jpg) 

至此，大功告成！

> 亲测可用，如果有什么问题欢迎在下方给我留言