## 用法

合并两个数组

    $a = array('a', 'b');
    $b = array('c', 'd');
    // + 号使用
    $c = $a + $b;
    // array_merge使用
    $c = array_merge($a, $b);
    
## 两者之间的区别

两者之间的区别主要分为两个方面：`key`值为数字和字符串

* 数字：`array_merge`当`key`相等时，不会覆盖前面的值，`key`值递增。`+`当`key`值相等，只保留最先出现的值而舍弃后出现的值
* 字符串：`array_merge`当`key`相等时，后面的值覆盖前面的值。`+`和`数字`时一致

### 示例

代码

    $a = array(0 => 'a', 1 => 'b');
    $b = array(0 => 'c', 1 => 'b');
    $c = $a + $b;
    var_dump($c);
    var_dump(array_merge($a, $b));
    
    echo '-------' . PHP_EOL;
    
    $a = array('abc' => 'a', 'cef' => 'b');
    $b = array('abc' => 'c', 'cef' => 'f');
    $c = $a + $b;
    var_dump($c);
    var_dump(array_merge($a, $b));
    
    echo '-------' . PHP_EOL;
    
    $a = array('0' => 'a', '1' => 'b');
    $b = array(0 => 'c', 1 => 'f');
    $c = $a + $b;
    var_dump($c);
    var_dump(array_merge($a, $b));
    
运行结果

    array (size=2)
    0 => string 'a' (length=1)
    1 => string 'b' (length=1)
    
    array (size=4)
    0 => string 'a' (length=1)
    1 => string 'b' (length=1)
    2 => string 'c' (length=1)
    3 => string 'd' (length=1)
    
    ------
    
    array (size=2)
    'abc' => string 'a' (length=1)
    'cef' => string 'b' (length=1)
    
    array (size=2)
    'abc' => string 'c' (length=1)
    'cef' => string 'f' (length=1)
    
    ------
    
    array (size=2)
    0 => string 'a' (length=1)
    1 => string 'b' (length=1)
    
    array (size=4)
    0 => string 'a' (length=1)
    1 => string 'b' (length=1)
    2 => string 'c' (length=1)
    3 => string 'd' (length=1)
