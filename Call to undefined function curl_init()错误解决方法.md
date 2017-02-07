### 原因

产生该错误的原因是因为php中没有开启curl模块，所以无法调用该方法。可以使用`php -m`查看已开启了哪些模块，如果找不到`curl`，说明正是这个原因，否则请略过这篇文章。

### 解决办法

1. 更新仓库：`sudo apt-get update`
2. 命令安装curl模块：`sudo apt-get install php5-curl`
3. 重启apache：`sudo service apache2 restart`

### 检查开启是否成功

命令行`php -m`，如果在下面的列表里能看到`curl`，则表示开启成功！