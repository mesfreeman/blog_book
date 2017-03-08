> SSH免密码登录省去了每次登录服务器都需要输入密码的问题，同时也使得服务器更加安全，避免服务器账号密码被泄露的风险

## 在本地主机生成密钥

> 如果本地主机已经生成过密钥，可以忽略该过程

可以使用某些工具生成，也可以使用命令行生成，如下：

    ssh-keygen -t rsa # 连续三次回车，不需要设置密码，即可在本地生成公钥（id_rsa.pub）和私钥（id_rsa）
    

## 在服务器上配置授权公钥

登录服务器，进入要授权的用户目录，如`vi /home/double/.ssh`，如果还没`.ssh`文件夹就建一个（一般都有）,然后`vi authorized_keys`，打开授权文件，将上面本地生成的公钥字符串Copy到该文件里（注：千万别Copy少字符了），如：

    double@iZ28klb08jmZ:~/.ssh$ vi authorized_keys
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDoGs2T7vo//R8O6jDK84dfPwCLRo4XJrbDE4S9cOV3iRB0uLGVp+UFNiXZe38r4Os4/ax5QHCxC1zMC1WbRMl/us/I+dvqQV5GXtaEugJZEbxt6nPWFEtDpDqqTFDnrZ11S4PS44Fdiy/Zm5xF+dpk9hv4CSiMv9JYawhAdsJB1hRzxYWldhxjA22Kb/6LnOZSNmW5e4Av5j9TEuUOoPfa9WCONRexF3+dH1UkLfqbd6z1kpo1UXEHQJAgbvcLyGs+Aop1nmj34gt1yZLQMAD2nI0/pgMAiSiWtTA2A9vvf2+OsNqMv7dkbl6XKg75VJRu7yRIdHc3JIIwLVNonK9 double@mac

说明：`ssh-rsa`是加密方式，`double@mac`是用户标识可以改动。保存并退出，做到这个一步你已经成功一半了 ～～

### 注意

检查权限，`.ssh`目录及`authorized_key`文件的权限只能对该用户有写的权限 ！！！

检查权限，`.ssh`目录及`authorized_key`文件的权限只能对该用户有写的权限 ！！！

检查权限，`.ssh`目录及`authorized_key`文件的权限只能对该用户有写的权限 ！！！

> 重要的事情说三遍，因为如果其它用户也有写的权限时，会出现安全性问题，因此如果权限设置不对，会导致免密码登录失败 ～

可以把`.ssh`目录权限设置为700，把`authorized_key`文件权限设置为600，注意看一下该文件的权限用户及用户组是不是该用户

> 以上如果配置没问题的话，在命令行输入 ssh + 服务器ip就可以不需要输入密码就进去了 ～

## 拓展阅读

### 介绍

`id_rsa` - 生成的私钥文件
`id_rsa.pub` - 生成的公钥文件
`know_hosts` - 已知的主机公钥清单，ssh会把访问过计算机的公钥都记录在该文件里
`authorized_keys` - 存放远程免密码登录的公钥，可以是多个，每行一个公钥

### 原理图

![SSH免密码登录原理图](http://blog.hequanxi.com/usr/uploads/2017/03/4239003457.png)

