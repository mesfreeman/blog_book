MySQL数据库是做为PHP程序猿最为常用的数据库，是一个基础。但随着工具和框架的强大，在实际开发中，对MySQL的基本命令用到的越来越少，所以好多人对MySQL的命令行操作几乎已经忘的差不多了。但有时候你会猛然发现工具固然强大，但当你需求复杂的SQL操作时，它并不能满足你的需求，再加入页面的来回跳转，SQL命令的不能复用，你的内心其实是崩溃的。这时候你想使用命令自己来的时候，你才发现原来这TMD基础的查询语句都忘了怎么拼的啦，`这就尴尬了～` 所以做为程序猿并不建议去使用类似于`phpMyAdmin`的工具。话不多说，总结如下：

> Note：大写字母为`MySQL`关键字，小写字母为参数，参数含义见参数说明。

## 用户操作

### 创建用户

    CREATE USER 'username'@'host' IDENTIFIED BY 'password';
    
说明：

* `username` - 你将创建的用户名
* `host` - 允许登录的主机，只允许本地使用`localhost`，允许某个ip使用`ip地址`，允许所有使用`%`通配符
* `password` - 该用户的登录密码，可以为空

### 删除用户

    DROP USER 'username'@'host';

### 用户授权

    GRANT privileges ON databaseName.tableName TO 'username'@'host';
    
说明：

* `privileges` - 用户的操作权限，如`SELECT`、`INSERT`、`UPDATE`等，如果授予所有权限则使用`ALL`
* `databaseName` - 数据库名
* `tableName` - 表名，如果要用户对所有数据库或数据表的相应操作权限则可以用`*`星号表示

注意：

上面命令授权的用户不给其它用户授权，如果想让该用户可以授权，可以用下面的命令：

    GRANT privileges ON databaseName.tableName TO 'username'@'host' WITH GRANT OPTION;
    
### 查看用户授权

    SHOW GRANTS FOR 'username'@'host';

### 撤消用户授权

    REVOKE privileges ON databaseName.tableName FROM 'username'@'localhost';
    
### 设置与更改用户密码

    SET PASSWORD FOR 'username'@'host' = PASSWORD('newPassword');
    SET PASSWORD = PASSWORD('newPassword'); # 如何是更改当前登录用户，可以更简单一些
    
说明：

* `newPassword` - 新密码

## 库操作

### 查看数据库

    SHOW DATABASES;
    
### 选择数据库

    USE databaseName;

### 创建数据库

    CREATE DATABASE databaseName;
    CREATE DATABASE databaseName CHARACTER SET UTF8; # 创建数据库并设置字符集
    
### 删除数据库

    DROP DATABASE IF EXISTS databaseName;
    
说明：

* `IF EXISTS` - 如果存在，则做什么操作，同理还有一个`IF NOT EXISTS`，通常用来做删除或创建表与数据库前的判断

### 修改数据库字符集

    ALTER DATABASE databaseName CHARACTER SET UTF8;
    
### 导出数据库

    1. 进入MySQL的bin目录
    2. 执行命令：mysqldump -uxxx -pxxx > /data/xxx_bak.sql
    
说明：

* `xxx` - 用户名、密码
* `/data/xxx_bak.sql` - 导出的数据库文件存放位置
    
### 导入数据库

    1. mysql -uxxx -pxxx # 登录到数据库
    2. USE databaseName  # 选择数据库
    3. SOURCE file.sql   # 导入数据库
    
说明：

* `file.sql` - 要导入的数据库文件

## 表操作

### 查看表

    SHOW TABLES;

### 修改表名

    ALTER TABLE oldTableName RENAME TO newTableName;
    
说明：

* `oldTableName` - 旧表名
* `newTableName` - 新表名

### 修改表的字符集

    ALTER TABLE tableName CONVERT TO CHARACTER SET characterName;
    
* `characterName` - 字符集名，如：UTF8 、 GBK等

### 查看表的创建SQL

    SHOW CREATE TABLE tableName;
    
说明：

* `tableName` - 表名

### 查看表结构

    DESC tableName;
    
### 只Copy源表结构并生成新表

    CREATE TABLE newTableName like oldTableName;

### Copy源表结构及数据并生成新表（注意这种方式会丢失索引）

    CREATE TABLE newTableName SELECT * FROM oldTableName;
    
### 查看表索引

    SHOW INDEX FROM tableName;
    
### 添加表索引

    ALTER TABLE tableName ADD INDEX (columnList);       # 添加普通索引
    ALTER TABLE tableName ADD UNIQUE (columnList);      # 添加唯一索引
    ALTER TABLE tableName ADD PRIMARY KEY (columnList); # 添加主键索引
    
说明：

* `columnList` - 列名，可以为多个，以逗号分隔

### 删除表索引

    ALTER TABLE tableName DROP INDEX indexName;
    ALTER TABLE tableName DROP PRIMARY KEY;      # 该方法只用于删除主键索引，为什么不用索引名，过来小编偷偷告诉你～
    
说明：

* `indexName` - 索引名，可通过上面的查看表索引来知晓

### 数据表的增、删、改、查

    # 增加
    INSERT INTO tableName (col1, col2, ……) values (val1, val2, ……)[, (val1, val2, ……)];
    # 删除
    DELETE FROM tableName where ……
    # 更改
    UPDATE tableName SET columnName = 'xx' WHERE ……
    # 查看
    SELECT * FROM tableName;
    
### 导出数据表查询结果到文件

    mysql -uxxx -pxxx databaseName -e "SELECT * FROM tableName WHERE Id > 20;" > "a.txt"

## 表字段操作

### 修改字段属性

    ALTER TABLE tableName CHANGE oldColumnName newColumnName ……              # 修改字段名
    ALTER TABLE tableName CHANGE columnName columnName VARCHAR(10) NOT NULL; # 修改字段类型
    
说明：

* `oldColumnName` - 旧字段名
* `newColumnName` - 新字段名

### 增加字段

    ALTER TABLE tableName ADD COLUMN columnName …… AFTER columnName;
    
### 删除字段

    ALTER TABLE tableName DROP columnName;

## 内置函数

MySQL里内置了很多函数，非常实用，熟练使用这些函数，能让你事半功倍～

`CONCAT()`函数，用于联合字段与字符，如：
    
    CONCAT('第', id, '行')
    
`SUBSTRING()`函数，用于截取字段（注意，MySQL是从第1位开始算的）,如截取名字的前两位：

    SUBSTRING(name, 1, 2) # 从第一位开始，取两位
    
`FROM_UNIXTIME()`函数，用于时间戳转换为日期显示，如

    FROM_UNIXTIME('1156219870')
    
`UNIX_TIMESTAMP()`函数，用于日期转换为时间戳显示，如

    UNIX_TIMESTAMP('2017-01-11')

