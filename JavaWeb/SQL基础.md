# SQL
- 数据操作语言 (DML)
- 数据定义语言 (DDL)

### DML
- SELECT - 从数据库表中获取数据
- UPDATE - 更新数据库表中的数据
- DELETE - 从数据库表中删除数据
- INSERT INTO - 向数据库表中插入数据

#### SQL SELECT 语句
- SELECT 列名称 FROM 表名称
- SELECT * FROM 表名称
- 例子：SELECT LastName,FirstName FROM Persons
- 星号（*）是选取所有列的快捷方式。

#### SQL SELECT DISTINCT 语句
- SELECT DISTINCT 列名称 FROM 表名称
- 关键词 DISTINCT 用于返回唯一不同的值。

#### WHERE 子句
- 如需有条件地从表中选取数据，可将 WHERE 子句添加到 SELECT 语句。
- SELECT 列名称 FROM 表名称 WHERE 列 运算符 值
- SQL 使用单引号来环绕文本值（大部分数据库系统也接受双引号）。如果是数值，请不要使用引号。

#### AND 和 OR 运算符用于基于一个以上的条件对记录进行过滤。
- 如果第一个条件和第二个条件都成立，则 AND 运算符显示一条记录。
  - SELECT * FROM Persons WHERE FirstName='Thomas' AND LastName='Carter'
- 如果第一个条件和第二个条件中只要有一个成立，则 OR 运算符显示一条记录。
  - SELECT * FROM Persons WHERE firstname='Thomas' OR lastname='Carter'
- SELECT * FROM Persons WHERE (FirstName='Thomas' OR FirstName='William')
AND LastName='Carter'

#### ORDER BY 语句
- ORDER BY 语句用于根据指定的列对结果集进行排序。
- ORDER BY 语句默认按照升序对记录进行排序。
- 如果您希望按照降序对记录进行排序，可以使用 DESC 关键字。

#### INSERT INTO 语句
- INSERT INTO 表名称 VALUES (值1, 值2,....)
- 插入新的行
  - INSERT INTO Persons VALUES ('Gates', 'Bill', 'Xuanwumen 10', 'Beijing')
- 在指定的列中插入数据
  - INSERT INTO Persons (LastName, Address) VALUES ('Wilson', 'Champs-Elysees')

#### Update 语句
- UPDATE 表名称 SET 列名称 = 新值 WHERE 列名称 = 某值

#### DELETE 语句
- DELETE 语句用于删除表中的行。
  - DELETE FROM 表名称 WHERE 列名称 = 值
- 删除所有行: DELETE FROM table_name

### DDL
- CREATE DATABASE - 创建新数据库
- ALTER DATABASE - 修改数据库
- CREATE TABLE - 创建新表
- ALTER TABLE - 变更（改变）数据库表
- DROP TABLE - 删除表
- CREATE INDEX - 创建索引（搜索键）
- DROP INDEX - 删除索引

---
- 邮箱 ：[elisabethzhen@163.com](elisabethzhen@163.com)
- Good Luck!