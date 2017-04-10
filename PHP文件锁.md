### 说明

当涉及多个进程或者线程对同一份文件进行操作时，这时候为了保证数据的完整性，需要对文件进程加锁处理。

### 涉及函数

PHP文件锁的使用涉及以下几个函数：

* [fopen][1]：打开文件或者Url，返回一个文件或Url资源
* [flock][2]：以咨询的方式锁定文件

### 实例

实例1

文件不加锁处理时，两个进程对它同时执行写的操作，会出现以下情况：

    A进程写入
    A进程写入
    B进程写入
    A进程写入
    B进程写入
    ....

从以上可以看出，A/B进程可以同时对文件进程操作，可能会出现A进程写入的内容，覆盖B进程写入内容的情况。

实例2

文件加锁处理时，两个进程对它同时执行写的操作，会出现以下情况：

    A进程写入
    A进程写入
    ....
    B进程写入
    B进程写入
    B进程写入
    ....

从以上可以看出，当A进程对文件做写入操作时，B进程处于等待状态，只有A进程结束时B进程才能对文件写入。

代码实例

下面通过一下简单的Demo演示一下：

    $processName = "test_process_1";
    $pidFile = "/tmp/{$processName}.pid";
    $dir = dirname($pidFile);
    if (!is_dir($dir)) {
        mkdir($dir, 0777, true);
    }
    $fp = fopen($pidFile, 'w+');
    if (!$fp || !flock($fp, LOCK_EX|LOCK_NB)) {
        echo "Process {$processName} still running\n";
        exit(3);
    }
    
    echo "Running process '$processName' \n";
    for ($i = 0; $i < 100; $i++) {
        echo "count $i \n";
        sleep(1);
    }
    echo "Done and exit \n";

运行效果如下：

A进程运行

![](http://oo5edb6t9.bkt.clouddn.com/14918120069916.jpg)

B进程等待

![](http://oo5edb6t9.bkt.clouddn.com/14918120263720.jpg)

[1]:http://php.net/manual/zh/function.fopen.php
[2]:http://php.net/manual/zh/function.flock.php

