### 简介

软链接其实就是`windows`下的快捷方式。

### 实例

**建立软链接**

    ln -s /home/double double  # 回车
    double -> /home/double     # cd double时相当于进入了/home/double目录

说明：

* `/home/double`：源文件
* `double`：链接文件名

**删除软链接**

    rm -i double


