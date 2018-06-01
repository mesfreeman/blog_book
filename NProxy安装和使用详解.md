说起`NProxy`不得不提起`Fiddler`，一款使用很广泛的网络抓包神器、静态资源重定向等众多功能与一身的神器，如果你还不晓得，请前去安利 --- [抓包利器Fiddler的使用](http://blog.dandy.fun/index.php/archives/167)。

那么`NProxy`又是什么呢？它一个支持多平台的前端调试利器，功能类似于`Fiddler`的静态资源重定向的功能，能够将捕获网络请求指向本地文件，从而在本地完成对线上资源的调试。为什么不用`Fiddler`呢，因为`Fiddler`虽然功能很强大，但它只局限于windows平台，像Mac/Linux与它注定了无缘。

### NProxy的使用用场景

Nproxy的使用场景可分为两种情况：

* 本地静态资源调用线上接口(测试接口、预上线接口等)
* 线上资源调试静态资源

我们在实际开发中，SPA（Single Page Application）时代提倡前后端分离，因此就会涉及前后面资源的调用问题。如：前端页面写好后想调用一下服务器的接口测试一下页面是否有问题，那么就需要把写的的页面上传到服务器上，然后再测试。有问题的话就要重复这个动作，给测试和调试带来了很多的不便。为什么不直接在本地调试呢，因为会涉及到跨域问题。那么`NProxy`就是来解决这个问题的，它可以拦截指定的`url`指向本地文件，从而完成本地的测试和调试工作。

## Mac安装和使用NProxy

### 安装

Mac上安装Nproxy需要用到软件包管理工具 -- homebrew，如果你的Mac上还没有装，请移步 -- [Mac安装homebrew](http://blog.dandy.fun/index.php/archives/192/)。有了它我们还需要先安装`node`，因为需要用到`node`的一个附带工具`npm`(即node package manager，用来搜索、下载、管理Node.js相关的套件)，安装过程很简单，只需要一条命令：

    brew install node

等待安装结束，此时`npm`也会一起安装上，下面介绍一下它的简单使用：

    npm help    查看npm帮助
    npm find    查找组件
    npm install 安装组件
    npm remove  删除组件

第一次使用`npm`命令时会出现如下警告，忽略即可

    npm WARN Building the local index for the first time, please be patient

**准备工作**都做好了，下面就是安装`NProxy`了，同样也是一条命令：

    npm install -g nproxy

安装结束会看到`/usr/local/lib/node_modules/nproxy`目录多了很多文件。

### 使用

第一步：在`NProxy`的安装目录下有一个叫`replace-rule.sample.js`的文件，它是一个示例文件，只需要安照上面的规则来配置你的重定向文件路径就可以了。如:

    // 新建配置文件(放在哪里都可以)
    vi replace-rule.js

    module.exports = [
    	{
    		pattern: 'weitu.html', // 线上访问路径
    		responder: '/Users/double/Documents/codes/sophie-frontend/web/static/pages/weitu.html' // 本地映射文件路径
    	}
    ];

说明：当浏览器访问到`weitu.html`时重定向到本地`/Users/double/Documents/codes/sophie-frontend/web/static/pages/weitu.html`文件，即实际访问的是本地文件。

第二步：启动`NProxy`

    nproxy -l replace-rule.js
    [INFO] NProxy started on 8989! // 说明已经对8989端口进行监听

第三步：设置浏览器代理

这也最最关键的一个地方，设置本机为代码服务器监听上面的8989端口，从而所有本机发布的请求都通过代理过滤，这样便可以按照既定的规则将指定的路径转发至本地环境。配置浏览器本地代理环境有两种方法：

1. 在`chrome`的设置中设置局域网代理，代理ip：127.0.0.1，端口8989
2. 安装`chrome`插件`switchyomega` -- [传送门](https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif)

小编配置参考（推荐这种方式）

![switchyomega](http://blog.dandy.fun/usr/uploads/2016/12/665344236.jpg)

### 测试效果

在浏览器中输入你要重写向的地址，查看`NProxy`下的监听，如出现以下效果就成功了：

    DoubledeMacBook-Pro:nproxy double$ nproxy -l replace-rule.js
    [INFO] NProxy started on 8989!
    [INFO] matched url: http://double.super.dandy.fun/weitu.html
    [INFO] matched url: http://double.super.dandy.fun/weitu.html

注意：要一直开着才可以哦