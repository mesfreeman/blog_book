> 模拟登录顾名思义，就是利于程序去模拟人工访问或登录网站的行为。要了解模拟登录我们不得不了解一下函数`CURL`。

### 什么是CURL？

CURL是一个利用URL语法规定来传输文件和数据的工具，支持很多协议，如HTTP、FTP、TELNET等。最爽的是，PHP也支持CURL库。使用PHP的CURL库可以简单和有效地去抓网页。你只需要运行一个脚本，然后分析一下你所抓取的网页，然后就可以以程序的方式得到你想要的数据了。无论是你想从从一个链接上取部分数据，或是取一个XML文件并把其导入数据库，那怕就是简单的获取网页内容，CURL 是一个功能强大的PHP库。

### 建立CURL请求的基本步骤

    // 第一步：初始化  
    curl_init();
    // 第二步：设置属性
    curl_setopt();
    // 第三步：执行并获取结果
    curl_exec();
    // 第四步：捕获异常
    curl_error();
    // 第五步：释放句柄
    curl_close();

## CURL的两种请求：GET和POST

### GET请求

CURL的GET请求是用来模拟HTTP的GET请求的，适应于可以通过GET方式访问的网页。代码示例如下：

    // 初始化
    $ch = curl_init();
    // 设置抓取的url
    curl_setopt($ch, CURLOPT_URL, 'http://www.baidu.com');;
    // 设置获取的信息以文件流的形式返回，而不是直接输出。
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    // 执行命令
    $data = curl_exec($ch);
    // 捕获异常
    $err = curl_error($ch);
    // 释放句柄
    curl_close($ch);
    // 显示数据
    var_dump($err, $data);

### POST请求

CURL的POST请求可以模块网页里的POST表单提交，同时也可以模拟GEt方式的请求,因此适用范围广范。代码示例如下：

    // 初始化并设置抓取的url
    $ch = curl_init('http://www.baidu.com/login.php');
    // 设置获取的信息以文件流的形式返回，而不是直接输出。
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    // 设置POST方式提交
    curl_setopt($ch, CURLOPT_POST, 1);
    // 设置POST要提交的参数
    $postParam = array(
        "username" => "coder",
        "password" => "12345",
    );
    curl_setopt($ch, CURLOPT_POSTFIELDS, $postParam);
    // 执行命令
    $data = curl_exec($ch);
    // 捕获异常
    $err = curl_error($ch);
    // 释放句柄
    curl_close($ch);
    // 显示数据
    var_dump($err, $data);
 
### 常用参数

设置头文件的信息作为数据流输出

    CURLOPT_HEADER

设置获取的信息以文件流的形式返回

    CURLOPT_RETURNTRANSFER

当根据Location重定向时，自动设置header中的Referer信息

    CURLOPT_AUTOREFERER

手动设置Referer信息

    CURLOPT_REFERER

根据Location重定向地址，递归访问

    CURLOPT_FOLLOWLOCATION

设定 HTTP 请求中"Cookie: "部分的内容

    CURLOPT_COOKIE

添加Cookie文件，即该文件里包含cookie数据。与上面不同之处是上面设置的字符串，这个需要一下完整的文件路径

    CURLOPT_COOKIEFILE

连接结束后，保存Cookie信息的文件

    CURLOPT_COOKIEJAR

设置HTTP头字段的数组

    CURLOPT_HTTPHEADER

设置POTS请求要提交的参数

    CURLOPT_POSTFIELDS

设置模拟登录过期时间

    CURLOPT_TIMEOUT

## 福利来了

对于经常用的操作小编对代码进行了一层封装处理，基本上能满足80%以上的网站模拟，分享给大家：
    
    /**
     * curl通用方法 <double.he@qq.com>
     *
     * @param string $url        地址
     * @param string $refererUrl 上一级地址
     * @param string $postParam  提交参数
     * @param string $fromCookie 要带的cookie文件
     * @param string $toCookie   生成的cookie文件
     * @param string $mode       模式：“phone”：模拟手机端，“pc”：模拟电脑端
     * @return array
     */
    public function curl_https($url, $refererUrl = null, $postParam = null, $fromCookie = null, $toCookie = null, $mode = 'phone')
    {
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, TRUE);
        curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE);
        curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, FALSE);

        if ($refererUrl !== null) {
            curl_setopt($ch, CURLOPT_REFERER, $refererUrl);
        }

        if ($postParam !== null) {
            curl_setopt($ch, CURLOPT_POST, TRUE);
            curl_setopt($ch, CURLOPT_POSTFIELDS, $postParam);
        }

        if ($fromCookie !== null) {
            curl_setopt($ch, CURLOPT_COOKIEFILE, $fromCookie);
        }

        if ($toCookie !== null) {
            curl_setopt($ch, CURLOPT_COOKIEJAR, $toCookie);
        }

        if ($mode === 'phone') {
            curl_setopt($ch, CURLOPT_USERAGENT, "Mozilla/5.0 (iPhone; CPU iPhone OS 9_1 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13B143 Safari/601.1");
        } else {
            curl_setopt($ch, CURLOPT_USERAGENT, "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.75 Safari/537.36");
        }

        curl_setopt($ch, CURLOPT_FOLLOWLOCATION, 1);
        curl_setopt($ch, CURLOPT_TIMEOUT, 30);
        $data = curl_exec($ch);

        if ($data === false) {
            $err = 'Curl error: ' . curl_error($ch);
        }

        curl_close($ch);

        // 组装返回数据
        $result = array(
            'url'           => $url,
            'refererUrl'    => $refererUrl,
            'postParam'     => $postParam,
            'fromCookie'    => $fromCookie,
            'toCookie'      => $toCookie,
            'mode'          => $mode,
            'err'           => $err,
            'data'          => $data,
        );

        return $result;
    }

## FAQ

#### 爬取内容与想象的不一致？

答：可能是请求的Url不对或者网页里有重定向，可通过抓包来确认。如果以上两种情况都不是，那么该网页有些内容可能是通过js动态生成的，因为js是在浏览器中才能解析并生成数据，所以爬取不到由它生成的内容

#### POST请求模拟登录不成功？

答：这个原因有很多方面，总结下来有以下几种情况：

* post参数提交不完整，有的网站反爬策略会在隐藏标签里加入参数，可通过查看源代码确认
* post参数提交不正确，有的网站反爬策略会加入动态字段，每次访问时都会有变化
* 没有设置cookie,网页提交时在请求头里有cookie信息，而你模拟时提交相应cookie
* 有验证码校验，但你提交的验证码cookie与提交时的不一致。解决方法：先模拟请求验证码并把cookie保存下来，再带上cookie和验证码去模拟登录
* 没有设置referer，网页提交时在请求头里有referer信息，而你模拟时没有设置referer

> PHP模拟登录相关的操作小编暂时只想到了这些，如果你有想补充的地方欢迎在下方留言。