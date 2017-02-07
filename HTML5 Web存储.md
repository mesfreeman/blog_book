## HTML5 Web本地存储

### 简介

之前在页面存储都是有cookie来完成，但缺点就是不适合大量数据的存储且效率不高，HTML5为了解决这一问题提供了两种在客户端存储数据的新方法：

* `localStorage` - 没有时间限制的数据存储
* `sessionStorage` - 针对一个`session`的数据存储

### localStorage

`localStorage`存储没有时间限制，只有不手动清除浏览器缓存或调用自身的清除方式，它就会一直保存在本地。

#### 代码实例

    <script>
        // 设置web存储
        localStorage.setItem('test', 'hello localStorage');
        console.log(localStorage.getItem('week1')); // 输出 hello localStorage
        // 清除key为test的web存储
        localStorage.removeItem('test');
        // 清除所有web存储
        localStorage.clear();
        console.log(localStorage.getItem('week1')); // 输出 null
    </script>

### sessionStorage

`sessionStorage`存储有时间限制，是一个基于会话的本地存储，生命周期为会话开始到结束，即页面关闭后该存储会被删除

#### 代码实例

    <script>
        // 设置web存储
        sessionStorage.setItem('test', 'hello sessionStorage');
        console.log(sessionStorage.getItem('week1')); // 输出 hello sessionStorage
        // 清除key为test的web存储
        sessionStorage.removeItem('test');
        // 清除所有web存储
        sessionStorage.clear();
        console.log(sessionStorage.getItem('week1')); // 输出 null
    </script>

### 综述

两者使用场景不同，在实际使用中可以结合不同的场景进行合理选择。常用方法、属性如下：

* `getItem(key)`方法 - 获取指定key存储的value值
* `key(index)`方法 - 返回列表中对应索引的key值
* `length()`属性 - 返回key/value对列表的长度
* `removeItem(key)`方法 - 从storage中删除一个对应的key值对
* `setItem(key,value)`方法 - 将value存储到key指定的字段
* `clear()`方法 - 移除storage中所有的key/value对

### 总结

两者常用来存储用`ajax`异步请求来的`json`数据，但两者本身都只支持存储字符串，因此在实际使用过程中，经常会与以下函数进行结合使用：

    var a={"name":"tom","sex":"男","age":"24"};
    var b='{"name":"Mike","sex":"女","age":"29"}';
    var aToStr=JSON.stringify(a);
    var bToObj=JSON.parse(b);
    alert(typeof(aToStr));  // string
    alert(typeof(bToObj));  // object

说明：把`ajax`异步请求来的`json`数据通过`JSON.stringify()`方法转换为字符串，然后通过web存储在本地，使用时再从本地获取到字符串，通过`JSON.parse()`方法转换来`json`对象进行操作。