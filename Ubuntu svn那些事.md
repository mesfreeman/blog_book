> uname -a 查看当前操作系统内核信息
> cat /proc/version 查看当前操作系统版本信息

## 安装客户端

Ubuntu svn的安装特别简单，只需要一条命令即可，`sudo apt-get install subversion`。一路回车，完事。

## 部署SVN仓库

### 1. 创建SVN仓库目录

    mkdir -p /data/svn
    
### 2. 创建项目版本库
    
    svnadmin create /data/svn/project01
    
创建成功后，`project01`目录会生成以下文件：

    [double@ii project]# ll
    drwxrwxrwx 2 root root 4096 Dec 14 11:23 conf
    drwxrwxrwx 6 root root 4096 Dec 14 18:24 db
    -rwxrwxrwx 1 root root    2 Dec 8  23:10 format
    drwxrwxrwx 2 root root 4096 Dec 14 18:23 hooks
    drwxrwxrwx 2 root root 4096 Dec  8 23:10 locks
    -rwxrwxrwx 2 root root  229 Dec  8 23:10 README.txt
      
### 3. 配置SVN仓库

进入`conf`目录（该版本库的配置文件目录）:

* `authz`：权限控制文件
* `passwd`：账号密码文件
* `svnserve.conf`：svn服务配置文件

1）设置账号密码，`vi passwd`，添加如下代码

    [users]
    # harry = harryssecret
    
    double = double.he
    test = test.he
    
保存并退出，此时就配置好了两个账号并分别设置好了密码（在项目代码的检出时会用到）。

2）为账号设置权限，`vi authz`，添加如下代码

    [/]
    project01 = double,test
    double=rw
    test=r
    
保存并退出，为double账号分配了读写权限，为test账号分配了只读权限。

3）修改`svnserve.conf`文件，`vi svnserve.conf`，找到并修改如下代码

    anon-access = none          # 匿名用户不可读
    auth-access = write         # 授权用户可写
    password-db = passwd        # 使用哪个文件作为账号文件
    authz-db = authz            # 使用哪个文件作为权限文件
    realm = /data/svn/project01 # 版本库处在目录

4）启动SVN版本库

    svnserve -d -r /data/svn/project01
    
注：如果启动时出错，可以输入`killall svnserve`，终止SVN服务然后再输入上面命令进行开启。如果已经有SVN别的仓库运行着，则可以输入指定端口进行启动，`svnserve -d -r /data/svn/project01 --listen-port 3691`

> 至此，SVN仓库部署完毕～

## SVN常用操作

### 代码检出

svn co svn版本库地址 检出目录，如：

    svn co svn://192.168.0.1/project01 /data/service/project01
    
### 添加文件或目录

svn add 文件或目录，如

    svn add /www/project01/index.html
    svn commit -m '说明'
    
### 代码更新

    svn update
    
### 查看操作日志

    svn log -l 3 # 查看3条操作日志
    
## 小技巧

### 更换仓库地址

    svn switch --relocate svn://192.168.0.1/project01 svn://192.168.0.1/project02 # 前者为旧地址，后者新地址
    
### SVN仓库代码有更新时，自动更新代码到网站目录

在SVN版本库的`hooks`目录，新增文件`post-commit`并写入如下代码：

    #!/bin/sh

    REPOS="$1"
    REV="$2"
    
    mailer.py commit "$REPOS" "$REV" /path/to/mailer.conf
    
    WEB=/alidata/www     #要更新的目录
    export LANG=en_US.UTF-8
    svn update $WEB --username double --password double.he

更改`post-commit`文件权限为777

    chmod 777 post-commit
    
> 大功告成～

