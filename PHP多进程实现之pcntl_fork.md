### 背景

最近在做一个网上数据爬取的程序，涉及的数据量很大，大到几十万的级别。第一次遇到要处理这么大的数据量，心里还是有点懵圈的。毋庸置疑肯定是要用多进程了，一个程序跑到猴年马月也爬不完了。

### pcntl_fork

官方解释在当前位置当前进程产生子分支。fork是创建了一个子进程，父进程和子进程 都从fork的位置开始向下继续执行，不同的是父进程执行过程中，得到的fork返回值为子进程号，而子进程得到的是0。

### 实例

实例1

    $pid = pcntl_fork();
    //父进程和子进程都会执行下面代码
    if ($pid == -1) {
        //错误处理：创建子进程失败时返回-1.
         die('could not fork');
    } else if ($pid) {
         //父进程会得到子进程号，所以这里是父进程执行的逻辑
         pcntl_wait($status); //等待子进程中断，防止子进程成为僵尸进程。
    } else {
         //子进程得到的$pid为0, 所以这里是子进程执行的逻辑。
    }

从上面的代码可以看出，产生一个子进程只需要调用一下`pcntl_fork`方法就可以了，失败时返回`pid`为`-1`，父进程`pid`为`1`,子进程`pid`为`0`。

**那么**如果产生多个子进程呢？

产生一个进程是调用一下`pcntl_fork`，所以产生多个子进程就多次调用呗。

实例2

    for ($i = 0; $i < 5; $i ++) {
            $pid = pcntl_fork();
            if (! $pid) {
                sleep(1);
                print "In child $i\n";
                exit($i);
            }
        }

**`注意`**：千万千万不要忘记在子进程里写`exit`，否则会成指数式的产生子进程，服务器分分钟钟的搞死。

这样子就会产生`5`个进程了，看着是不是很简单啊，那么**坑爹**地方来了~~~

我们爬取网上数据干什么用的？当然是存到数据库里自己用喽。那你肯定会想到这样子做了：

    // 建立数据库连接
    $conn = mysql_connect('127.0.0.1', 'root', 'root');
    for ($i = 0; $i < 5; $i ++) {
            $pid = pcntl_fork();
            if (!$pid) {
                // 抓取到的数据
                $data = …………
                // 存入数据库
                $sql = "…………";
                mysql_query($sql, $conn);
                print "Ok! \n";
                exit($i);
            }
        }

**不好意思**，`MySQL server has gone away`，MySQL 不干了！！！

![异常](https://pic.dandy.fun/14918121450717.jpg)

**官方说法**是这样子的：当子进程任务完成并结束的时候，会自己关闭数据库连接，下一进程再进行数据库操作的时候发现失去了数据库连接，因为就出现了`MySQL server has gone away`的错误。详细说明 [MySQL server has gone away错误](http://php.net/manual/zh/function.pcntl-fork.php)

官方也给出了解决实例，**`But`**是错的，我试了一下还是会现在上面的错误。已经无数次的折腾后，终于找到了解决的办法。

解决方法

    for ($i = 0; $i < 5; $i ++) {
            $pid = pcntl_fork();
            // 建立数据库连接
            $conn = mysql_connect('127.0.0.1', 'root', 'root', true);
            if ($pid) {
            // 建立新的数据库连接
            $conn = mysql_connect('127.0.0.1', 'root', 'root', true);
            } else if (! $pid) {
                // 抓取到的数据
                $data = …………
                // 存入数据库
                $sql = "…………";
                mysql_query($sql, $conn);

                print "Ok! \n";
                exit($i);
            }
        }

**只需注意两点**：

* 建立数据库连接要在`fork`子进程之后
* `mysql_connect`连接时不要忘了在最后一参数上加`true`

> 以上就是快把我坑哭的经历，希望能帮助到一些人。因为在此过程中查阅了好多地方，竟然都是抄来官方例子说事，真心不知道他们有没有试过。

在此次大数据爬取过程中还遇到了好多好多的问题，烧死了无数的脑细胞，等有时间更新到下篇文章中，我先哭会~

