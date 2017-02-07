## 前言

本篇文章主要对PHP编码所涉及的内容做一个综合性的讨论。在PHP编程过程中，保证编码的统一规范至关重要，没有统一的字符编码规范，会导致浏览器界面显示错乱、文字乱码等情况。所谓的字符编码的统一规范，我个人认为主要有以下几个方面：

* 源数据编码
* 页面编码
* 数据库编码

## 源数据编码

我们在开发过程中，往往数据的来源不同，有的来自表单提交，有的来自其它网页抓取，有的来自于第三方接口调用。因此，它们的数据编码也会不一样。那么我们实际的使用过程中就要特别注意源数据的编码问题，避免因编码不一致导致的bug出现。

### 涉及函数

    string iconv(string $in_charset, string $out_charset, $str)

## 页面编码

页面编码的设置主要有两种形式：

1. 使用`<meta http-equiv="content-type" content="text/html;charset=xxx">`标签
2. 使用`header("content-type:text/html;charset=xxx");`

### 两者的区别？

**前者**属于HTML标签，它的作用是声明客户端的浏览器用什么字符集编码显示该页面，xxx可以为`GB2312`、`GBK`，`UTF-8`（和MySQL不同，MySQL是`UTF8`）等等。因此，大部分页面可以采用这种方式来告诉浏览器显示这个页面的时候采用什么编码，这样才不会造成编码错误而产生乱码。但是有的时候我们会发现有了这句还是不行，不管xxx是哪一种，浏览器采用的始终都是一种编码（ps:原因后面有说明）。

注意：`<meta>`仅仅是一个声明，它起作用表明服务器已经把HTML信息传到了浏览器。

**后者**属于PHP标签，它的作用是把括号里面的信息发到HTTP头。看着它的作用和`<meta>`标签基本相同，但不同的是HTTP头是服务器以HTTP协议传送HTML信息到浏览器前所送出的字串。而`<meta>`标签是属于HTML信息的，所以`header()`发送的内容先到达浏览器。通俗点就是`header()`的优先级高于`<meta>`（不知道可不可以这样讲）。

如果加入一个PHP页面既有`header("content-type:text/html; charset=xxx")`，又有`<mate http-equiv="content-type" content="text/html; charset=xxx">`，浏览器就只认前者http标头而不认meta了。当然这个函数只能在php页面内使用。

#### 那么这是为什么呢？

说到这里，我们就要谈一下Apache配置了。

Apache根目录的`conf`文件夹里，有整个Apache的配置文档`httpd.conf`。 

用文本编辑器打开`httpd.conf`，第708行（不同版本可能不同）有`AddDefaultCharset xxx，xxx`为编码名称。这行代码的意思：设置整个服务器内的网页文件https标头里的字符集为你默认的xxx字符集。有这行，就相当于给每个文件都加了一行header("content-type:text/html; charset=xxx")。这下就明白为什么明明`meta`设置了是`utf-8`，可浏览器始终采用`gb2312`的原因。

### 小结

优先级从高到低依次为：

* `header("content-type:text/html; charset=xxx")`
* `AddDefaultCharset xxx`
* `<meta http-equiv="content-type" content="text/html; charset=xxx">`

## 数据库编码

PHP 程序在查询数据库之前，首先执行`mysql_query("SET NAMES xxx")`;其中`xxx`是你网页的编码(`charset=xxx`)，如果网页中 `charset=utf8`，则`xxx=utf8`。几乎所有WEB程序，都有一段连接数据库的公共代码，放在一个文件里，在这文件里，加入`mysql_query("SET NAMES xxx")`就可以了。 

`SET NAMES`显示客户端发送的SQL语句中使用什么字符集。因此，`SET NAMES 'utf-8'`语句告诉服务器“将来从这个客户端传来的信息采用字符集`utf-8`”。它还为服务器发送回客户端的结果指定了字符集。（例如：如果你使用一个SELECT语句，它表示列值使用了什么字符集）。

> 能注意到以上三个问题，那么在实际开发过程中基本上就不会出现编码方面的问题了。


 





