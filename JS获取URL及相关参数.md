前段时间写的一个[VIP视频免费播](http://www.hequanxi.com/tool/ggvip.html)的网站，想在里面加一个百度分享的功能，却发现百度分享出来的链接地址是当前地址的，就想了个办法做分享之前获取当前URL地址并设置一个新URL地址给浏览器，于是就解决了百度分享的问题。在这个过程中了解到了`window.location`这个对象的一些使用方法，今记录如下～

## 对象属性详细说明

该对象涉及了八个属性，涵盖了对RUL链接地址的大部分操作：

* `hash`        - 设置或获取href属性中在井号“#”后面的分段
* `host`        - 设置或获取location或URL的hostname和port号
* `hostname`    - 设置或获取location或URL的主机名称部分
* `href`        - 设置或获取整个URL为字符串
* `pathname`    - 设置或获取对象指定的文件名或路径
* `port`        - 设置或获取与URL关联的端口号
* `protocol`    - 设置或获取URL的协议部分
* `search`      - 设置或获取href属性中跟在问号“?”后面的部分

### 使用实例

假如目前的浏览器当前地址为：`http://blog.hequanxi.com:80/article/js-get-url.html?user=double#comment-1`

    window.location.hash     // #comment-1
    window.location.host     // blog.hequanxi.com:80
    window.location.hostname // blog.hequanxi.com
    window.location.href     // blog.hequanxi.com:80/article/js-get-url.html?user=double#comment-1
    window.location.pathname // /article/js-get-url.html
    window.location.port     // 80
    window.location.protocol // http:
    window.location.search   // ?user=double

