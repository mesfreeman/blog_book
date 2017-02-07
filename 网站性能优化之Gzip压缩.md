### 简介

gzip是HTML压缩算法的一种，HTTP压缩是在Web服务器和浏览器间传输压缩文本内容的方法。HTTP压缩工作原理是：

1. Web服务器接收到浏览器的HTTP请求后，检查浏览器是否支持HTTP压缩； 
如果浏览器支持HTTP压缩，Web服务器检查请求文件的后缀名；
2. 如果请求文件是HTML、CSS等静态文件，Web服务器到压缩缓冲目录中检查是否已经存在请求文件的最新压缩文件；
3. 如果请求文件的压缩文件不存在，Web服务器向浏览器返回未压缩的请求文件，并在压缩缓冲目录中存放请求文件的压缩文件；
4. 如果请求文件的最新压缩文件已经存在，则直接返回请求文件的压缩文件；
5. 如果请求文件是ASPX等动态文件，Web服务器动态压缩内容并返回浏览器，压缩内容不存放到压缩缓存目录中。

### 系统说明

* 系统：Ubuntu 14.04.4
* 服务器：Apache 2.4.7

### 在线检测工具

* [网页gzip压缩检测](http://www.gzip.zzbaike.com/)

### 开启`Gzip`压缩涉及的两个模块：`deflate`和`filter`。

* 进入`Apache`的`mod-enablesd`目录，查看两个模块是否已经软链接到该目录，如果没有先把它软链接到该目录。

    ![软链接](http://i.imgur.com/4ETBS8v.png)

    如果看到这两项表示已经软链接成功。
    
* 通过`vi deflate.load`编辑该模块，启动该模块（即去掉前面的`#`）。如下图所示：

    ![vi](http://i.imgur.com/3SwRbzo.jpg)

* 通过`vi filter.load`编辑该模块，去掉前面的`#`。如下图所示：

    ![filter](http://i.imgur.com/Bjzu5NI.jpg)

### 配置`deflate`模块。

通过`vi deflate.conf`，编辑该模块的配置文件，写入如下内容：

    # start gzip compression
    <IfModule mod_deflate.c>
        <IfModule mod_filter.c>
            DeflateCompressionLevel 6 # 压缩率（从1到9,不建议设置太高会占用cup资源）
            SetOutputFilter DEFLATE
            # no compression file
            SetEnvIfNoCase Request_URI .(?:gif|jpe?g|png)$ no-gzip dont-vary
            SetEnvIfNoCase Request_URI .(?:exe|t?gz|zip|bz2|sit|rar)$ no-gzip dont-vary
            SetEnvIfNoCase Request_URI .(?:pdf|doc|avi|mov|mp3|rm)$ no-gzip dont-vary
            # compression file
            AddOutputFilterByType DEFLATE text/html text/plain text/xml text/css text/javascript
            AddOutputFilterByType DEFLATE application/x-javascript
            AddOutputFilterByType DEFLATE application/xhtml+xml
            AddOutputFilterByType DEFLATE application/xml
            AddOutputFilterByType DEFLATE application/rss+xml
            AddOutputFilterByType DEFLATE application/atom_xml
            AddOutputFilterByType DEFLATE application/json
            AddOutputFilterByType DEFLATE application/x-httpd-php application/x-httpd-fastphp
            AddOutputFilterByType DEFLATE image/svg+xml
        </IfModule>
    </IfModule>

### 重启Apache服务器

执行`sudo service apache2 restart`,当看到如下所示，表示服务器已重启。

![restarr Apache](http://i.imgur.com/t8iQtUG.jpg)

### 测试Gzip压缩是否开启成功

随便访问一下该服务器下的网站，如果在网站的响应头里出现`Content-Encoding:gzip`则表示`Gzip`压缩开启成功，如下图所示：

![Gzip](http://i.imgur.com/SvERQ0N.jpg)

### 测试效果

* 网站未启动Gzip压缩

![未压缩时](http://i.imgur.com/Fe3lNyo.jpg)

* 网站启动Gzip压缩

![网站启动Gzip压缩](http://i.imgur.com/5FkjQr9.jpg)

### 扩展

`mod_gzip`与`mod_deflate`的区别？

1. 版本差异：`mod_gzip`属于第三方模块，在`Apache 1.x`系列时没有内建网页压缩技术。而在`Apache 2.x`时，把网页压缩考虑进去，内建了`mod_deflate`模块，用于取代`mod_gzip`模块。
2. 压缩质量：`mod_deflate`压缩速度略快而`mod_gzip`压缩比略高。一般默认情况下，`mod_gzip`会比`mod_deflate`多出4%~6%的压缩量。
3. 对服务器资源的占用：`mod_zip`对服务器CPU的占用要高一些。所以在高流量的服务器上，建议使用`mod_deflate`，在低流量的服务器上使用`mod_gzip`。