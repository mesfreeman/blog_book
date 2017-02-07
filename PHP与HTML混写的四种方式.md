### 应用场景

PHP作为一款后端语言，大部分要处理的任务就是把内容输出到浏览器上显示出来，无可避免的要输入HTML代码，所以就会出现PHP与HTML代码混写的情况。

### 四种方式

#### 1）单、双引号包围法

这是最简单方法，如下：

    echo '<span>So Easy!</span>';

单引号直接包围HTML代码块，但HTML代码块里涉及到变量时，就是使用双引号来解决了，如下：

    $name = 'Double';
    echo "<span>$name</span>";
    echo '<br/>';
    echo '<span>$name</span>';

输出

    Double
    $name

区别已经很明显了，双引号的变量会自动解析，而单引号的不会。

缺点分析：

1. 当涉及的HTML代码块里出现大量的单双引号时，易出现冲突
2. 单双引号里的HTML书写时没有代码提示，也没有高亮显示，易出现代码编写错误

#### 2）使用HEREDOC/NOWDOC

`HEREDOC`和`NOWDOC`是PHP5.3开始支持的一种新特性，它允许在程序中使用一种自定义的标志符来包围文本，而`HEREDOC`和`NOWDOC`的关系就类似于双引号包围和单引号包围一样，前者解析区块内的变量，而后者不解析区块内的变量。

用法如下：

    $Content='Hello!';
    
    // 下面写出了一个HEREDOC，其中标识LABEL可以自定义为任何字符串，但要保证开头的标识和结尾的标识一样
    echo <<<LABEL
    $Content
    LABEL;
    // 结尾的方法：另起一行，打上LABEL。注意结尾的标识前面和后面不要插入任何字符，空格也不行
    
    echo '<br>';// 换行
    
    // NOWDOC和HEREDOC的书写方式差别在于NOWDOC的标识符需要用单引号包围
    echo <<<'LABEL'
    $Content
    LABEL;
    // 其他无异

缺点分析：

1. 很好的解决了单双引号冲突的问题，但仍不能解决代码提示和高亮的问题。

### 3）HTML中嵌入PHP代码块

这种方式使用比较多，利于PHP的开始与结束符来独立一部分空间出来，如下：

    <?php
    // 首先在这里写好相关的调用代码
    function OutputTitle(){
        echo 'TestPage';
    }
    function OutputContent(){
        echo 'Hello!';
    }
    
    // 然后再下面调用相关函数就可以了
    ?>
    
    <!DOCTYPE html>
    <html>
        <head>
            <title><?php OutputTitle(); ?></title>
        </head>
        <body>
            <span><?php OutputContent(); ?></span>
        </body>
    </html>

缺点分析：

1. 能很好的解决上述的两个问题，但会影响代码的可读性

#### 4）使用前端模版引擎

随着Web开发的蒸蒸日上，前后端开发相互分离也越来越成为了主流。前端模版引擎的诞生使得前后端的对接更加完美、更加高效，同时代码的可读性也更高。比如现在很流行的Smarty引擎。

> 结语：四种方式各有优劣，使用的场景有所不同，可以根据实际的开发需求进行选择