## Git https和ssh方式的区别

在管理Git项目上，很多时候都是直接使用`https url`克隆到本地，当然也有有些人使用`SSH url`克隆到本地。这两种方式的主要区别在于：使用`https url`克隆对初学者来说会比较方便，复制`https url`然后到`git Bash`里面直接用`clone`命令克隆到本地就好了，但是每次`fetch`和`push`代码都需要输入账号和密码，这也是`https`方式的麻烦之处。而使用`SSH url`克隆却需要在克隆之前先配置和添加好`SSH key`，因此，如果你想要使用`SSH url`克隆的话，你必须是这个项目的拥有者。否则你是无法添加`SSH key`的，另外`ssh`默认是每次`fetch`和`push`代码都不需要输入账号和密码。

## Git 配置和使用

### 设置Git的user name和email（第一次的时候需要）

    $ git config --global user.name "你的名字"
    $ git config --global user.email "你的邮箱"

### 生成密钥

    $ ssh-keygen -t rsa -C "你的邮箱"

连续3个回车。如果不需要密码的话。最后会得到两个文件：`id_rsa`和`id_rsa.pul`，即私钥和公钥。如下图：

![生成密钥](http://blog.dandy.fun/usr/uploads/2016/09/1940243662.png)

如果不是第一次生成密钥，则选择`overwrite`。如下图：

![第二次生成](http://blog.dandy.fun/usr/uploads/2016/09/2480617211.png)

### 添加密钥到ssh-agent

确保`ssh-agent`是可用的。`ssh-agent`是一种控制用来保存公钥身份验证所使用的私钥的程序，其实`ssh-agent`就是一个密钥管理器，运行`ssh-agent`以后，使用`ssh-add`将私钥交给`ssh-agent`保管，其他程序需要身份验证的时候可以将验证申请交给`ssh-agent`来完成整个认证过程。

    eval "$(ssh-agent -s)"
    Agent pid 59889

添加生成的SSH Key到`ssh-agent`

    $ ssh-add .ssh/id_rsa

![添加密钥](http://blog.dandy.fun/usr/uploads/2016/09/4037310316.png)

### 登录Github，添加ssh

把上面生成的`id_rsa.pub`文件里的内容复制到这里

![添加ssh](http://blog.dandy.fun/usr/uploads/2016/09/1576689016.png)

### 测试成功与否

    $ ssh -T git@github.com

如果有看到你的名字，就成功了~

    Hi 你的名字! You've successfully authenticated, but GitHub does not provide shell access.

## 拓展

### 更改连接方式

    // 从ssh改为https连接
    git remote set-url https://xxx.com

    // 从https改为ssh连接
    git remote set-url git@github.com

或者直接修改它的配置文件

    sudo vim .git/config