## http2.0简介

为了解释这个问题，将从以下几个方面进行阐述：

* 定义
* 产生的背景
* 解决目标

### 定义

http2.0即超文本传输协议2.0，是下一代http协议。它的前世是http1.0与http1.1这两个兄弟。它最大的特点是：在不改动http的语义、方法、状态码、UIR及首部字段等等核心的情况下，却能致力于突破上一代标准的性能限制，改进传输性能，实现低延迟和高吞吐量。是在应用层与传输层新增一个二进制分帧层，因此称之为http2.0。

### 产生的背景

http协议是一个非常成功的协议，诞生之初主要应用于web端内容获取，因此消息格式是实施简单性和可访问性的优化，而不是应用程序的性能。

如：web应用，而不是web网页。以及多媒体和移动APP、多设备的支持、[SPA](https://www.zhihu.com/question/31782625)等
![QQ20170114-110216](http://blog.hequanxi.com/usr/uploads/2017/01/1383467061.jpg)

随着互联网的发展和web2.0的诞生，页面需要有更多的内容展示、排版更加精美、交互更加复杂，而如今的Web……
![QQ20170114-110554](http://blog.hequanxi.com/usr/uploads/2017/01/1431128482.jpg)

影响一个网络请求的因素主要有两个：带宽和延迟，随着网络带宽的不断提高，影响网络请求的就只剩下延迟了。而http1.0连接无法复用，因此导致每次请求都要经历三次握手和慢启动，延迟一直降不下来。后来http1.1增加了持久连接，对pc端的浏览器体验帮助很大，但对移动APP端成效不大，因为APP端的请求分散时间跨度相对较大。

http1.0与http1.1的弊端及应对方式：

* 一个TCP连接上同时只能有一个请求/响应
    * 开多个TCP（域名散列）
    * 减少请求数（合并请求或资源）
* Http头部没有压缩
    * 减少请求数
    * 启用Cookie-Less域名
* 需要遵守一个请求一个响应的模式，无法推送重要资源
    * 重要CSS放在头部，JS放在底部
    * 资源内联

**这些优化只是其中的冰山一角，但其它也不再阐述，因为在http2.0大部分问题已不复存在**

**小结：**由此可见http1.0和http1.1虽然存在这么多的问题，业界也想出了各种优化的手段，但这些方法都是尝试绕开协议本身的缺陷，都有种隔靴搔痒、治标不治标的感觉。直到2012年google如一声惊雷提出了[SPDY](http://www.geekpark.net/topics/158198)的方案，大家才开始从正面看待和解决老版本http协议本身的问题，这也直接加速了http2.0的诞生。（ps：实际上，http2.0是以Google黑科技SPDY为原型进行讨论和标准化的）

### 解决目标

生逢其世造英雄，它的到来必定是为了解决http1.0与http1.1解决不了问题，而且要拥有向后兼容的特性，即：在不改动http的语义、方法、状态码、UIR及首部字段等核心的情况下，却能致力于突破上一代标准的性能限制，改进传输性能，实现低延迟和高吞吐量。

因此它有一些重要的前提：

* 客户端向server发送request这种基本模型不会变
* 老的scheme不会变，使用http://和https://的服务和应用不会要做任何更改，不会有http2://
* 使用http1.x的客户端和服务器可以无缝的通过代理方式转接到http2.0上
* 不识别http2.0的代理服务器可以将请求降级到http1.x

## http2.0详述

### http2.0协议

1. RFC 7540 - [Hypertext Transfer Protocol Version 2(HTTP/2)](https://httpwg.github.io/specs/rfc7540.html)
2. RFC 7541 - [HPACK:Header Compression for HTTP/2](https://httpwg.github.io/specs/rfc7541.html)

### 引入的新功能

既然不改动http已有的核心框架，又要突破上一代标准的限制，就是势必要在原来的基础上引入新功能：

* 二进制分帧层
* 服务器推送
* 单一TCP连接
* 多路复用
* 优先级控制
* 并行双向字节流的请求和响应
* 头部压缩

#### 二进制分帧层

如果以降低延迟为目标，应用层的http和传输层的tcp都有可调整的空间，但tcp作为更底层的协议已存在数十年之久，那是动不得的，会被业界打屁屁的。因此选择了在应用层和传输层之间增加了一个二进制分帧层。

在二进制分帧层上，http2.0会将所有传输的消息分割为更小的消息和帧，并对它们采用二进制格式的编码，其中http1.x的首部信息会被封装到headers帧，而我们的requese body则封装到data帧里面。

![QQ20170114-155638](http://blog.hequanxi.com/usr/uploads/2017/01/4082032387.jpg)

#### 服务器推送

服务器推送是http2.0新增的一个强大的新功能，是指在客户端请求之前发送数据的机制。比如当你请求一个主页时，服务器可能在响应主页请求的同时，还会额外的响应一些logo/img/css等，因为它知道客户端会用到这些东西。相当于**一个HTML文档内嵌了所有的资源**，不过与之相比，服务器推送有一个很大的优势：可惜缓存！

![QQ20170116-100328](http://blog.hequanxi.com/usr/uploads/2017/01/2779728326.jpg)

因此**内嵌资源**的优化手段就变成了多些一举了！

#### 单一TCP连接

在连接无法复用的情况下，每次请求都经历三次握手和慢启动，三次握手在高延迟的场景下较明显，慢启动则对文件大请求影响较大，两者给网络请求带来了很大的影响。因此为解决这个问题http2.0采用了单一的TCP连接，减少三次握手和慢启动的次数，从而降低延迟同时提高了带宽的利用率。

单一TCP连接的方式，使到从上到下的层面都得到了好处：

1. 可以减少服务链接压力,内存占用少了,连接吞吐量大了
2. 由于TCP连接减少而使网络拥塞状况得以改观
3. 慢启动时间减少,拥塞和丢包恢复速度更快

因此**资源合并减少请求**的优化手段就变成了多此一举了！

#### 多路复用

多路复用的意思是在一条连接上，我们可以发起无数个请求，并且响应也可以同时返回。

#### 优先级控制

单一TCP连接多路复用，同时带来了另一个问题，**有可能导致关键请求被阻塞**，因此引入了优先级的概念，允许给每个请求设置优先级。比如一个网站，先加载首页内容，让用户先看到，再加载样式表、最后加载js等

#### 并行双向字节流的请求和响应

虽然http2.0变成了单一TCP连接，但提供了并行双向字节流的请求和响应。在http2.0上，客户端和服务器可以把消息分解为互不依赖的帧，然后乱序发送，最后在另一端把它们重新组合。而且还可以一边发送一边响应。

![QQ20170116-093746](http://blog.hequanxi.com/usr/uploads/2017/01/3662721514.jpg)

因此**域名分区**的优化手段就变成了多此一举了！

#### 头部压缩

http2.0在客户端和服务器端使用“首部表”来跟踪和存储之前发送的键-值对，对于相同的数据，不再通过每次请求和响应发送;通信期间几乎不会改变的通用键-值对只需发送一次。

如果首部发生变化了，那么只需要发送变化了数据在Headers帧里面，新增或修改的首部帧会被追加到“首部表”。首部表在 http2.0的连接存续期内始终存在，由客户端和服务器共同渐进地更新。

![QQ20170116-103728](http://blog.hequanxi.com/usr/uploads/2017/01/3840382976.jpg)

### 服务器及客户端支持情况

#### Web Server Support

![QQ20170116-113942](http://blog.hequanxi.com/usr/uploads/2017/01/3745318774.jpg)

已支持大部分主流服务器，这里有一份详细的[HTTP/2服务器支持清单](https://github.com/http2/http2-spec/wiki/Implementations)。

#### Client Support

![QQ20170116-115644](http://blog.hequanxi.com/usr/uploads/2017/01/843655090.jpg)


主流客户端也已经纷纷支持，详情见[HTTP/2 protocol](http://caniuse.com/#feat=http2).

#### 网站必须支持https

因为客户端和server之间在确立使用http1.x还是http2.0之前，必须要确认对方是否支持http2.0，所以这里必须要有一个协商的过程。最简单的是一问一答的形式，但会多一个RTT延迟，修改http1.x的初衷就是为了降低延迟，因此是这个RTT是无法接受的。google制定SPDY的时候也遇到了这个问题，他们的办法是强制SPDY走https，在SSL层完成这个协商过程。所以http2.0也采用了相同的方式，虽然没有强制http2.0走ssl层，但大部分浏览器厂商只实现了基于https的2.0协议，因此要想使用http2.0协议就必须让网站支持https。

那么要部署http2.0就必须先让**网站支持https**，因此我们先大概了解一下https的部署与配置

## https简介

全称超文本传输安全协议，是一种网络安全传输协议。在计算机网络上，HTTPS经由超文本传输协议进行通信，但利用SSL/TLS来对数据包进行加密。HTTPS开发的主要目的，是提供对网络服务器的身份认证，保护交换数据的隐私与完整性。这个协议由网景公司（Netscape）在1994年首次提出，随后扩展到互联网上。

## https的部署与配置

部署与配置https，有两种方式：一种是使用自签名证书，另一种是使用第三方CA机构签署证书。第一种随便使用，只是没有经过官方认可的机构认证而已，因此浏览器无法自动识别其安全性，均会弹出警告。后一种则是正规的签名证书，有发证机构签名，浏览器能自动识别。

**自签名证书**
![QQ20170117-114906](http://blog.hequanxi.com/usr/uploads/2017/01/1204282968.jpg)

**第三方CA机构签署证书**

![QQ20170117-114906](http://blog.hequanxi.com/usr/uploads/2017/01/1996970808.jpg)

### 一、使用用自签名证书

#### 生成自签名证书

    cd /etc/apache2
    sudo mkdir ssl
    cd ssl
    sudo openssl req -x509 -newkey rsa:1024 -keyout bbs.pem -out bbs.pem -nodes -days 999

此时会让你填入一些个人信息，运行结束后会在当前目录生成`bbs.pem`文件，如下：

    double@iZ28klb08jmZ:/etc/apache2/ssl$ sudo openssl req -x509 -newkey rsa:1024 -keyout bbs.pem -out bbs.pem -nodes -days 999
    [sudo] password for double:
    Generating a 1024 bit RSA private key
    ........++++++
    ...........++++++
    writing new private key to 'bbs.pem'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:CA
    State or Province Name (full name) [Some-State]:SZ
    Locality Name (eg, city) []:SZ
    Organization Name (eg, company) [Internet Widgits Pty Ltd]:SZ
    Organizational Unit Name (eg, section) []:SZ
    Common Name (e.g. server FQDN or YOUR name) []:SZ
    Email Address []:double.he@qq.com
    double@iZ28klb08jmZ:/etc/apache2/ssl$ ll
    total 12
    drwxr-xr-x  2 root root 4096 Jan 17 11:31 ./
    drwxr-xr-x 10 root root 4096 Jan 17 10:09 ../
    -rw-r--r--  1 root root 1905 Jan 17 11:32 bbs.pem

#### 配置虚拟主机

    NameVirtualHost *:443

    <VirtualHost *:443>
        SSLEngine on
        SSLCertificateFile /etc/apache2/ssl/bbs.pem

        ServerName bbs.hequanxi.com
        ServerAdmin mesfreeman@126.com
        DocumentRoot /data/services/sites/bbs.hequanxi.com/web
        ErrorLog /data/services/sites/bbs.hequanxi.com/log/error.log
        CustomLog /data/services/sites/bbs.hequanxi.com/log/access.log combined

        <Directory /data/services/sites/bbs.hequanxi.com/web>
            Options FollowSymLinks MultiViews
            AllowOverride All
            Require all granted
        </Directory>
     </VirtualHost>

#### 重启Apache

    sudo apache service apache2 restart

> 至此自签名https配置完成。不过一般不推荐使用这种，由于浏览器不能自动识别会给用户带来一定的错觉，极大的影响用户体验。

### 二、使用第三方CA机构签署证书

> 第三方CA机构有收费的也有免费的，下面就以免费的第三方CA机构 -- `Let's Encrypt` 来展示介绍。

#### 生成签名证书

Let's Encrypt提供了自己的签名生成工具 - [Certbot](https://certbot.eff.org/),支持命令行操作，有兴趣可以研究一下。为了更简单的生成签名证书文件，GitHub有一个开源的脚本可以快速的生成，下面我们就来介绍它的使用 - [lets-encrypt](https://github.com/xdtianyu/scripts/tree/master/lets-encrypt)

1）下载脚本及配置文件

    wget https://raw.githubusercontent.com/xdtianyu/scripts/master/lets-encrypt/letsencrypt.conf
    wget https://raw.githubusercontent.com/xdtianyu/scripts/master/lets-encrypt/letsencrypt.sh
    chmod +x letsencrypt.sh

2）修改配置文件（只需要修改 DOMAIN_KEY DOMAIN_DIR DOMAINS 为你自己的信息）

    ACCOUNT_KEY="letsencrypt-account.key"
    DOMAIN_KEY="www.hequanxi.com.key"
    DOMAIN_DIR="/data/services/sites/www.hequanxi.com/web"
    DOMAINS="DNS:www.hequanxi.com,DNS:hequanxi.com"
    #ECC=TRUE
    #LIGHTTPD=TRUE

> 注：执行过程中会自动生成需要的 key 文件。其中 ACCOUNT_KEY 为账户密钥， DOMAIN_KEY 为域名私钥， DOMAIN_DIR 为域名指向的目录，DOMAINS 为要签的域名列表， 需要 ECC 证书时取消 #ECC=TRUE 的注释，需要为 lighttpd 生成 pem 文件时，取消 #LIGHTTPD=TRUE 的注释

3) 运行脚本

    ./letsencrypt.sh letsencrypt.conf

> 注：需要已经绑定域名到`/data/services/sites/www.hequanxi.com/web`目录，即通过`http://www.hequanxi.com 或 http://hequanxi.com`可以访问到该目录，用于域名的验证。

运行结果如下：

    double@iZ28klb08jmZ:/etc/apache2/letsencrypt-ssh$ sudo ./letsencrypt.sh letsencrypt.conf
    Generate account key...
    Generating RSA private key, 4096 bit long modulus
    .....................................................++
    e is 65537 (0x10001)
    Generate domain key...
    Generating RSA private key, 2048 bit long modulus
    .....................................+++
    ...................................................+++
    e is 65537 (0x10001)
    Generate CSR...www.csr
    Parsing account key...
    Parsing CSR...
    Registering account...
    Registered!
    Verifying www.hequanxi.com...
    www.hequanxi.com verified!
    Signing certificate...
    Certificate signed!
    New cert: www.chained.crt has been generated

4）添加定时任务，自动更新证书

    0 0 1 * * /etc/apaches/letsencrypt-ssh/letsencrypt.sh /etc/apache2/letsencrypt-ssh/letsencrypt.conf >> /var/log/lets-encrypt.log 2>&1

#### 配置虚拟主机

    NameVirtualHost *:443

    <VirtualHost *:443>
        SSLEngine on
        SSLCertificateFile /etc/apache2/letsencrypt-ssh/www.chained.crt
        SSLCertificateKeyFile /etc/apache2/letsencrypt-ssh/www.hequanxi.com.key

        ServerName www.hequanxi.com
        ServerAdmin mesfreeman@126.com

        // 省略了部分配置 ……

    </VirtualHost>

#### 重启Apache

    sudo apache service apache2 restart

## http2.0的部署与配置

### 开启apache的http2模块

说起开启apache的模块，你肯定会情不自禁的敲下如下命令：

    sudo a2enmod http2

不好意思，会报错的：

    ERROR: Module http2 does not exist!

不是说好了apache 2.4以后就内置http2.0协议的么？其实这个不怪apache，原因是因为Ubuntu是一个[LTS](http://www.cnblogs.com/yymn/p/4686322.html)版本，要以稳定为主，而mod_http2是试验性的，所以不给予加入。是不是想哭了，不过没关系，正所谓道高一尺、道高一丈，我们可以通过添加[PPA](http://www.onesl.com/web/smkj/2011040302.html)（个人软件集）来解决，如下：

    double@iZ28klb08jmZ:~$ sudo add-apt-repository ppa:ondrej/apache2
    This branch follows latest Apache2 packages as maintained by the Debian Apache2 team with couple of compatibility patches on top.
    It also includes some widely used Apache 2 modules (if you need some other feel free to send me a request).
    BUGS&FEATURES: This PPA now has a issue tracker: https://deb.sury.org/#bug-reporting
    PLEASE READ: If you like my work and want to give me a little motivation, please consider donating: https://deb.sury.org/#donate
     More info: https://launchpad.net/~ondrej/+archive/ubuntu/apache2
    Press [ENTER] to continue or ctrl-c to cancel adding it

    gpg: keyring `/tmp/tmpz44frzue/secring.gpg' created
    gpg: keyring `/tmp/tmpz44frzue/pubring.gpg' created
    gpg: requesting key E5267A6C from hkp server keyserver.ubuntu.com
    gpg: /tmp/tmpz44frzue/trustdb.gpg: trustdb created
    gpg: key E5267A6C: public key "Launchpad PPA for Ondřej Surý" imported
    gpg: Total number processed: 1
    gpg:               imported: 1  (RSA: 1)
    OK

> 注：这里用到了`python-software-properties`package，是需要提前安装的，不然无法使用`add-apt-repository`命令。

然后更新apache2

    sudo apt update
    sudo apt dist-upgrade

更新结束后，apache就会更新到最新版本，看我的：

    double@iZ28klb08jmZ:~$ apache2 -v
    Server version: Apache/2.4.25 (Ubuntu)

这时候看一下模块里面是不是就有`http2`模块了，接下来就是开启这个模块了：

    sudo a2enmod http2

### 配置虚拟主机

http2.0的配置区分http和https协议，如果你使用80端口（http）来访问你的网站，则配置如下：

    <VirtualHost xxxx:80>
        Protocols h2c http/1.1
        ……
    </VirtualHost>

如果你使用443端口（https）来访问你的网站，则配置如下：

    <VirtualHost xxxx:443>
        Protocols h2 http/1.1
        ……
    </VirtualHost>

### 重启Apache

    sudo apache2 restart

> 此时不出意外，就大功告成了！！！

## http2.0中需要改进或去掉的优化方式

1）域名散列

* http/2针对同域名只使用一个TCP连接
* 域名散列会导致更多的TCP连接，浪费CPU和内存
* 域名散列会破坏http/2的优先级，降低头部压缩效果

2）资源合并

* http/2支持多路复用，可以基于一个TCP连接并发多个请求
* 资源合并会造成缓存利用率低，增加系统复杂性
* 资源合并会浪费CPU和内存，造成整体可用时间变慢

3）资源内联

* http/2支持Server Push，可以在发送页面之前推送必要的资源
* 资源内联会造成无法利用缓存，浪费后续访问用户的带宽
* 多页面公用的资源，多次内联也是一种浪费

> 因此如果你的网站在http/1.x的时候做了以上的这些优化，那么当你把网站升级为http/2以后，这些优化相当http/2就变在了多此一举，还有可能带来一些其实方面的问题，要即时去掉或针对性改进。

## http2.0中应继续使用的优化方式

1）减少DNS查询
2）使用CDN
3）开启GZIP压缩
4）开启页面缓存

## 拓展

### 测试https网站安全性

配置好https之后最好对自己的网站进行一个全面的测试，来检查一下配置是否有遗漏的地方，这里有一个比较好用的网站 - [SSL Labs](https://www.ssllabs.com/ssltest/analyze.html)。我[网站的评测](https://www.ssllabs.com/ssltest/analyze.html?d=www.hequanxi.com)才是个`A-`。

![QQ20170118-155809](http://blog.hequanxi.com/usr/uploads/2017/01/2953363752.jpg)

### http/1.x与http/2加载速度对比

这里有一个网站Dome，对http/1.x与http/2加载速度做了一个对比 - [传送门](https://http2.akamai.com/demo)。

![QQ20170118-161338](http://blog.hequanxi.com/usr/uploads/2017/01/4138689844.jpg)

### 如何识别网站开启了http2.0？

谷歌浏览器内置的有http2.0查看器，在地址输入`chrome://net-internals/#http2`即可查到，也可以安装谷歌插件[HTTP/2 and SPDY indicator](https://chrome.google.com/webstore/detail/http2-and-spdy-indicator/mpbpobfflnpcgagjijhmgnchggcjblin)，如果网站使用了http/2，则图标会变颜色。

![QQ20170118-162415](http://blog.hequanxi.com/usr/uploads/2017/01/3568844182.jpg)

## 参考文章

1. [ubuntu下apache布置https](http://www.metsky.com/archives/561.html)
2. [免费的https证书的使用](https://imququ.com/post/letsencrypt-certificate.html)
3. [申请let’s encrypt证书的shell](https://github.com/xdtianyu/scripts/blob/master/lets-encrypt/README-CN.md)
4. [小巧的开源工具申请let's encrypt证书](https://github.com/diafygi/acme-tiny)
5. [http2.0的奇妙生活](http://www.alloyteam.com/2015/03/http2-0-di-qi-miao-ri-chang/)
6. [http2.0的简单总结](https://linjunzhu.github.io/blog/2016/03/10/http2-zongjie)
7. [http2讲解](https://ye11ow.gitbooks.io/http2-explained/content/part3.html)
8. [在 Ubuntu 上 开启 Apache2 HTTP/2 协议](https://blog.lutty.me/linux/2016-05/enable-apache2-http2-in-ubuntu.html)
9. [ubuntu的LTS版本是什么意思？](http://www.cnblogs.com/yymn/p/4686322.html)
10. [ubuntu如何添加PPA源](http://www.onesl.com/web/smkj/2011040302.html)


