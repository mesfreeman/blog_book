mysql触发器顾名思义，当触发器所在的表上出现指定的事件时，将调用该对象触发执行。

## 创建触发器

在mysql中，触发器的语法如下：

```sql
CREATE TRIGGER trigger_name
trigger_time
trigger_event ON tb1_name
FOR EACH ROW
trigger_stmt
```

说明：

* trigger_name：标识触发器名称，用户自行指定
* trigger_time：标识触发时机，可以为 BEFORE / AFTER
* trigger_event：标识触发事件，可以为 INSERT / UPDATE / DELETE / LOAD DATA / REPLACE
* tb1_name：触发器所有的表名
* trigger_stmt：触发程序体，可以是一句SQL，或者是 BEGIN 和 END 包含的多条语句

## 示例

假设有两张表：

* 班级表 class(classId, studentCount)
* 学生表 student(studentId, classId)

创建触发器，学生表有学生添加时自动更新班级表里的studentCount，如下

```sql
DELIMITER \\
CREATE TRIGGER trigger_student_insert
AFTER
INSERT ON student FOR EACH ROW
BEGIN
DECLARE c int;
SET c = (SELECT studentCount FROM class WHERE classId = new.classId);
UPDATE class SET studentCount = c +1 WHERE classId = new.classId;
END;
\\
```

说明：

* `DELIMITER $$`：重新定义结束符，因为mysql默认的结束符是分号，可程序体里也要写分号，如果不重新定义，代码会在遇到分号时结束，出现错误
* `DECLARE c int`：定义一个局部变量，只能在 BEGIN ... END 复合语句中使用，并且要定义在复合语句的开头
* `NEW`：mysql中有OLD与NEW，old指触发器所在表的数据（只读），new指因触发器而响应的数据（可写）

## 查看触发器

查看所有触发器

```sql
SHOW TRIGGERS;
```

查看指定表触发器

```sql
SHOW TRIGGERS  FROM 库名 Like '表名';
```

查看指定触发器

```sql
SELECT * FROM TRIGGERS WHERE TRIGGER_NAME = '触发器名称';
```

## 删除触发器

```
DROP TRIGGER 触发器名称;
```
