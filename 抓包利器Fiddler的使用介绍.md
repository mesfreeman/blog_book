> Fiddler（中文名称：小提琴）是一个HTTP的调试代理，以代理服务器的方式，监听系统的Http网络数据流动，Fiddler可以也可以让你检查所有的HTTP通讯，设置断点，以及Fiddle所有的“进出”的数据（我一般用来抓包）,Fiddler还包含一个简单却功能强大的基于JScript .NET事件脚本子系统，它可以支持众多的HTTP调试任务。

## 使用场景

* 对网站数据抓包分析与调试
* 对模拟登录及数据采集过程分析
* 对网站部分请求重定向
* 对移动端抓包

## 使用说明

### 数据抓包

打开Fiddler并开启capturing，如下图：

![](http://oo5edb6t9.bkt.clouddn.com/14918100763832.jpg)

在浏览器中访问你需要抓包的网站，即可看到抓到的数据包信息如下图：

![](http://oo5edb6t9.bkt.clouddn.com/14918101119749.jpg)

### 断点调试

Fiddler最强大的功能莫过于设置断点了，设置好断点后，你可以修改httpRequest 的任何信息包括host, cookie或者表单中的数据。设置断点有两种方法:

第一种：打开Fiddler点击Rules->Automatic Breakpoint->Before Requests(这种方法会中断所有的会话)

> 如何消除命令呢？点击Rules->Automatic Breakpoint->Disabled

第二种：在命令行中输入命令:`bpu www.baidu.com`(这种方法只会中断www.baidu.com)

> 如何消除命令呢？在命令行中输入命令`bpu`

#### 示例

1. 在命令行输入`bpu http://passport.cnblogs.com/login.aspx`
2. 打开该登录界面，输入错误的帐号、密码提交登录，会话中断
3. 修改帐号密码后，点击`Run to Completion`登录成功

![断点调试](http://oo5edb6t9.bkt.clouddn.com/14918101427515.jpg)

### 模拟登录分析

当我们要模拟登录一个网站的时候，就需要对该网站做一个全面的分析，比如:是否有cookie产生？是否需要referer？登录时需要提交哪些参数等。那么我们就可以通过Fiddler抓包得到，如下图抓包分析河南理工大学图书馆登录过程：

![模拟登录](http://oo5edb6t9.bkt.clouddn.com/14918101928299.jpg)

说明：从抓包的数据我们可以看出以下信息：

* 登录提交地址：`http://218.196.244.90:8080/dz.php`
* referer信息：`http://218.196.244.90:8080/dz1.php`
* 需要提交的参数：`T1=311209040216&T3=&xm=%BA%%AB%CF%B2&tday1=71269&B1=%B2%E9%D1%AF`

有了这些模拟登录代码怎么写就显而易见了。

### 请求重定向

所谓重定向就是将页面原本需要调用的资源指向其他资源（可以是其它资源路径也可以是本地的一个文件资源）。比如当我们开发一个网站的时候将样式文件放在本地，再将请求重定向到本地，就可以完成样式的本地调试，不用提交到服务了。如下图：

![重定向](http://oo5edb6t9.bkt.clouddn.com/14918102380195.jpg)

### 移动端抓包

这也是Fiddler的强大之处，不仅可以对PC端的数据进行抓包分析，同时也可以对移动端的数据进行抓包分析。设置如下：

1. 打开Fiddler->Tools->Fiddler Options，选中`Decrpt HTTPS traffic`（支持截获HTTPS请求），选中`Allow remote computers to connect`（允许别的机器把HTTP、HTTPS请求发送到Fiddler上来），如下图：

    ![](http://oo5edb6t9.bkt.clouddn.com/14918102988016.jpg)  

    ![](http://oo5edb6t9.bkt.clouddn.com/14918103229384.jpg)

    **记得端口号为：8888**

2. 设置手机代理
    * 记手机与电脑处于同一个网段
    * 设置无理代码，ip为电脑ip，端口为：8888，如下图：
        
        ![设置代理](http://oo5edb6t9.bkt.clouddn.com/14918113971300.jpg)
    
    * 在你的手机浏览器中输入`http://上面的ip:8888`，点击`FiddlerRoot certificate`安装证书并添加至信任列表，如下图：
    
        ![安装证书](http://oo5edb6t9.bkt.clouddn.com/14918114271665.jpg)

3. 打开你想抓包的手机应用，然后就会在Fiddler里看到抓到的数据了

> 常用来盗用别的软件接口，如《掌上英雄联盟》软件里查询周免英雄的接口：`http://lol.qq.com/biz/hero/free.js?v=52`

## 拓展

Fiddler工具相当强大，这里只介绍了常用的功能，还有很多功能没有介绍，如果有兴趣可以自行百度，了解它的神秘面纱。

