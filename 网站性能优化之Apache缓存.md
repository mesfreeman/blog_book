### 简介

当直接在浏览器中输入一个URL，或者点击一个链接的时候，那么浏览器缓存就会起作用，如果缓存没有过期，那么浏览器会从本地读取资源，不会发起HTTP请求，如果缓存过期，那么浏览器会发起新的浏览器请求。按`ctrl+F5`，浏览器会清空本地缓存，重新请求资源。

`mod_expires`理论上可以减少10%左右的重复请求，让重复的用户对指定的页面请求结果都CACHE在本地，根本不向服务器发出请求。

### 说明

* `Expires`：HTTP/1.0的缓存头
* `Cache-Control:max-age`：HTTP/1.1的缓存头

注：`Expires`指定了资源过期的绝对时间，GMT格式，`Cache-Control:max-age`指定了资源过期的相对时间，单位是秒。在支持HTTP/1.1的浏览器上，如果发送两个头，那么Cache-Control: max-age会覆盖掉Expires；
在支持HTTP/1.0的浏览器上，即使发送了两个头，但是只有Expires会起作用，所以为了兼容老的浏览器，还是要同时发送这两个头。

### 开启Apache缓存涉及的`expires`模块

只需要一条命令而已，如下：

    sudo a2enmod expires

### 配置`expires`模块

查看`mod-enabled`目录是否有`expires.conf`文件，如果没有，查看`mod-available`目录是否有，如果也没有。在`mod-available`目录新建该文件。并写入如下配置：

    <IfModule mod_expires.c>
        ExpiresActive  On

        ExpiresDefault "access plus 10 days"
        ExpiresByType  image/x-icon "access plus 60 days"
        ExpiresByType  image/gif    "access plus 30 days"
        ExpiresByType  image/jpeg   "access plus 30 days"
        ExpiresByType  image/png    "access plus 30 days"
        ExpiresByType  text/css     "access plus 30 days"
        ExpiresByType  application/x-javascript  "access plus 30 days"
        ExpiresByType  application/javascript    "access plus 30 days"
        ExpiresByType  text/html                 "access plus 1 minutes"
    </IfModule>

说明：

* `<IfModule expires_module></IfModule>`来包含指令为了避免在`expires_module`模块未开启的情况下还要去执行。
* `ExpiresActive`是指是否启用页面缓存功能，启用为`On`,反之为`Off`。
* `ExpiresDefault`是指设定预设的过期时间。
* `ExpiresByType`是指设定缓存的文件的类型及缓存时间。
* `access plus 10 days`是指该文件浏览时起缓存10天。

写入配置：

![写入配置](https://pic.dandy.fun/14918135235940.jpg)

### 在`mod-enabled`目录建立软链接

    sudo ln -s ../mods-available/expires.conf

### 重启Apache

执行`sudo service apache2 restart`,当看到如下所示，表示服务器已重启。

![](https://pic.dandy.fun/14918135989346.jpg)

### 测试开启是否成功

![测试结果](https://pic.dandy.fun/14918136179135.png)

由上图可以看出，该`css`文件被缓存了一个月，也就是上面配置时的30天，所以开启缓存成功。

### 拓展

对于缓存文件时间的写法有两种：

1. 文字叙述型，如上配置所示。英文单词包括`years`、`months`、`weeks`、`days`、`hours`、`minutes`、`seconds`；
2. 代码加上秒数型，如：`ExpiresByType image/png A604800`（A相当于access）。

起算时间有三种：

* 分别是`access`、`now`、`modification`。
* 其中`access`与`now`意义相同，而`modification`指的是网页文件的“最后编辑时间”。

### 配置范例

范例一

* 如上

范例二

    <IfModule expires_module>
        ExpiresActive On
        ExpiresDefault A86400
        ExpiresByType image/x-icon A2592000
        ExpiresByType application/x-javascript A2592000
        ExpiresByType text/css A2592000
        ExpiresByType image/gif A604800
        ExpiresByType image/png A604800
        ExpiresByType image/jpeg A604800
        ExpiresByType text/plain A604800
        ExpiresByType application/x-shockwave-flash A604800
        ExpiresByType video/x-flv A604800
        ExpiresByType application/pdf A604800
        ExpiresByType text/html A900
    </IfModule>

范例三

    <IfModule mod_expires.c>
        ExpiresActive On
        ExpiresDefault A0
        <FilesMatch “\.(flv|ico|pdf|avi|mov|ppt|doc|mp3|wmv|wav)$”>
            ExpiresDefault A9030400  # 1 年
        </FilesMatch>
        <FilesMatch “\.(jpg|jpeg|png|gif|swf)$”>
            ExpiresDefault A604800   # 1 星期
        </FilesMatch>
        <FilesMatch “\.(txt|xml|js|css)$”>
            ExpiresDefault A10800    # 3 小时
        </FilesMatch>
    </IfModule>

