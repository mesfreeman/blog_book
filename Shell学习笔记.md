### 简介

Shell 是一个用C语言编写的程序，它是用户使用Linux的桥梁。Shell既是一种命令语言，又是一种程序设计语言。

### 第一个shell脚本

小编发现无论学习哪门语言，第一程序永远都是`Hello World`，它也不例外

#### 建脚本文件

    vim test.sh

#### 写入内容

    #! /bin/bash

    echo "Hello World !"

#### 设置执行权限

    chmod +x test.sh

#### 执行

    ./test.sh

说明：

* `#!` -  是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行，即使用哪一种Shell
* `echo` - 向窗口输出文本

### shell变量

#### 定义变量

    your_name="double.he"

说明：

* 不要美元符号
* 等号前后不能有空格
* 首个字符必须是字母
* 变量名不能有空格，可以是下划线`_`
* 不能使用标点符号
* 不能使用`bash`里的关键字

#### 使用变量

只要在变量名前面加美元符即可，如：

    echo $your_name
    echo "${your_name} very good !"

#### 重定义变量

    your_name="double.he"
    echo $your_name
    your_name="double.li"
    echo $your_name

#### 只读变量

    your_name="double.he"
    echo $your_name
    readonly your_name
    your_name="double.li"
    echo $your_name

运行后会报错：

    ./test.sh: line 8: myName: readonly variable

#### 删除变量

    unset your_name

### shell字符串

#### 单双引号

与`PHP`类似，单引号原样输出，双引号支持变量和转义字符，如：

    echo 'this is a single string' # 输出 this is a single string
    your_name="double.he"
    echo "Hello, I know your are \"$your_name\"! \n" # 输出 Hello, I know your are double.he!

#### 拼接字符串

    your_name="double.he"
    say="hello, "$your_name" !"

#### 获取字符串长度

    your_name="double.he"
    echo ${#your_name} # 输出 9

### shell数组

`bash`支持一维数组（不支持多维数组），并且没有限定数组大小

#### 定义数组

用括号表示数组，数组元素用`空格`符号分割开，如：

    数组名=(值1 值2 …… 值n)

    array_name=(value0 value1)

    # 或者
    array_name=(
        value0
        value1
    )

    # 或者
    array_name[0]=value0
    array_name[1]=value1

#### 读取数组

    ${数组名[下标]}

    echo ${array_name[0]} # 输出 value0

    echo ${array_name[@]} # 输出所有

    # 或者
    array_name[0]=value0
    array_name[1]=value1

#### 读取数组

    ${数组名[下标]}

    echo ${array_name[0]} # 输出 value0

    echo ${array_name[@]} # 输出所有

### shell传递参数

我们可以在执行 Shell 脚本时，向脚本传递参数，脚本内获取参数的格式为：$n。n 代表一个数字，1 为执行脚本的第一个参数，2 为执行脚本的第二个参数，以此类推……

    echo "shell传值实例"
    echo "执行文件名：$0"
    echo "第一个参数：$1"
    echo "第二个参数：$2"

执行：

    ./test.sh 1 2

    shell传值实例
    执行的文件名：./test.sh
    第一个参数为：1
    第二个参数为：2

说明：

* `$0` - 执行的文件名
* `$#` - 传递到脚本的参数个数
* `$*` - 以一个单字符串显示所有向脚本传递的参数。如"$*"用「"」括起来的情况、以"$1 $2 … $n"的形式输出所有参数。
* `$$` - 脚本运行的当前进程ID号
* `$!` - 后台运行的最后一个进程的ID号
* `$@` - 与$*相同，但是使用时加引号，并在引号中返回每个参数。如"$@"用「"」括起来的情况、以"$1" "$2" … "$n" 的形式输出所有参数
* `$-` - 显示Shell使用的当前选项，与`set`命令功能相同。
* `$?` - 显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误

实例：

    echo "\$# test $#"
    echo "\$* test $*"
    echo "\$\$ test $$"
    echo "\$! test $!"
    echo "\$@ test $@"
    echo "\$- test $-"
    echo "\$? test $?"

运行结果：

    ./test.sh 1 2 3

    $# test 3
    $* test 1 2 3
    $$ test 10472
    $! test
    $@ test 1 2 3
    $- test hB
    $? test 0

### shell基本运算符

初探：

    var=`expr 2 + 2`
    echo "2 + 2 = "$var

运行结果：

    2 + 2 = 4

注意：表达式与运算符之间要有空格，表达式要被反引号包含

#### 算术运算符

下表列出了常用的算术运算符，假定变量 a 为 10，变量 b 为 20：

![](http://oo5edb6t9.bkt.clouddn.com/14918083996091.jpg)

注意：条件表达式要放在方括号之间，并且要有空格，例如: [$a==$b] 是错误的，必须写成 [ $a == $b ]

#### 关系运算符

下表列出了常用的关系运算符，假定变量 a 为 10，变量 b 为 20：

![](http://oo5edb6t9.bkt.clouddn.com/14918084562619.jpg)

#### 布尔运算符

下表列出了常用的布尔运算符，假定变量 a 为 10，变量 b 为 20：

![](http://oo5edb6t9.bkt.clouddn.com/14918084872137.jpg)

#### 逻辑运算符

以下介绍 Shell 的逻辑运算符，假定变量 a 为 10，变量 b 为 20:

![](http://oo5edb6t9.bkt.clouddn.com/14918085082043.jpg)

#### 字符串运算符

下表列出了常用的字符串运算符，假定变量 a 为 "abc"，变量 b 为 "efg"：

![](http://oo5edb6t9.bkt.clouddn.com/14918085270372.jpg)

#### 文件测试运算符

文件测试运算符用于检测 Unix 文件的各种属性：

![](http://oo5edb6t9.bkt.clouddn.com/14918085483449.jpg)

### test命令

Shell中的 test 命令用于检查某个条件是否成立，它可以进行数值、字符和文件三个方面的测试。如：

    num1=100
    num2=100
    if test $num1 -eq $num2
    then
        echo "两个数相等"
    else
        echo "两个数不相等"
    fi

### shell流程控制

#### if……else

比较两个数的大小，通过传值方式

实例：

    num1=$1
    num2=$2
    if [ $num1 == $num2 ]
    then
        echo "两个数相等"
    elif [ $num1 -lt $num2 ]
    then
        echo "数1小于数2"
    elif [ $num1 -gt $num2 ]
    then
        echo "数1大于数2"
    else
        echo "没有符合条件"
    fi

#### for循环

格式：

    for var in item1 item2 ... itemN
    do
        command1
        command2
        ...
        commandN
    done

实例：

    for loop in 1 2 3
    do
        echo "The value is: $loop"
    done

输出结果：

    The value is: 1
    The value is: 2
    The value is: 3

#### while循环

格式：

    while condition
    do
        command
    done

实例：

    a=1
    while(($a<=2))
    do
        echo $a
        let "a++"
    done

输出结果：

    1
    2

### shell函数

#### 初识

    demoFun(){
        echo "我是一个函数"
    }
    # 调用，只需写函数名
    demoFun

#### 带返回值的函数

    returnFun(){
        return 1
    }
    returnFun
    echo "返回值为：$? !"

注意：不支持返回字符串

### 输入/输出重定向

简单来说：

* `<` - 输入重定向
* `>` - 输出重定向
* `>>` - 输出以追加的方向重定向

如果希望执行某个命令，但又不希望在屏幕上显示输出结果，那么可以将输出重定向到`/dev/null`，如：

    $ command > /dev/null

如果希望屏蔽 stdout 和 stderr，可以这样写：

    $ command > /dev/null 2>&1

说明：0 是标准输入（STDIN），1 是标准输出（STDOUT），2 是标准错误输出（STDERR）

### 文件包含

实例：

shell1.sh

    url="blog.dandy.fun"

shell2.sh

    # 包含脚本1,注意有空格
    . ./shell1.sh

    echo $url

## 结语

不积跬步，无以至千里，学无止境，初探shell就这样子结束了，以后有用到其它方面的，继续积累。积累。

