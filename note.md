1.创建数据库并插入数据
```
sudo service mysql start
mysql -u 用户密码
```
sql大小写不敏感
```
CREATE DATABASE mysql_siyan;
show datacases;
use mysql_shiyan;
show tables;
```
新建表格式：
```
CREATE TABLE 表的名字
(
列名a 数据类型(数据长度),
列名b 数据类型(数据长度)，
列名c 数据类型(数据长度)
);
```
```
CREATE TABLE employee (id int(10),name char(20),phone int(12));
```
查看表中内容
```
SELECT * FROM employee;
```
插入数据
```
INSERT INTO 表的名字(列名a,列名b,列名c) VALUES(值1,值2,值3);
```
```
INSERT INTO employee(id,name,phone) VALUES(01,'Tom',110110110);

INSERT INTO employee VALUES(02,'Jack',119119119);

INSERT INTO employee(id,name) VALUES(03,'Rose');
```
删除数据库
```
DROP DATABASE mysql_shiyan;
```

2. 约束（5种）
```
CREATE DATABASE mysql_shiyan;

use mysql_shiyan;

CREATE TABLE department
(
  dpt_name   CHAR(20) NOT NULL,
  people_num INT(10) DEFAULT '10',
  CONSTRAINT dpt_pk PRIMARY KEY (dpt_name)
 );

CREATE TABLE employee
(
  id      INT(10) PRIMARY KEY,
  name    CHAR(20),
  age     INT(10),
  salary  INT(10) NOT NULL,
  phone   INT(12) NOT NULL,
  in_dpt  CHAR(20) NOT NULL,
  UNIQUE  (phone),
  CONSTRAINT emp_fk FOREIGN KEY (in_dpt) REFERENCES department(dpt_name)
 );
 
CREATE TABLE project
(
  proj_num   INT(10) NOT NULL,
  proj_name  CHAR(20) NOT NULL,
  start_date DATE NOT NULL,
  end_date   DATE DEFAULT '2015-04-01',
  of_dpt     CHAR(20) REFERENCES department(dpt_name),
  CONSTRAINT proj_pk PRIMARY KEY (proj_num,proj_name)
 );
```
主键约束
```
CONSTRAINT dpt_pk PRIMARY KEY (dpt_name),
```
默认值约束
```
people_num INT(10) DEFAULT '10',
```
唯一值约束
```
phone   INT(12) NOT NULL,
in_dpt  CHAR(20) NOT NULL,
UNIQUE  (phone),
```
外键约束
```
CONSTRAINT emp_fk FOREIGN KEY (in_dpt) REFERENCES department(dpt_name)
```
非空约束
```
salary  INT(10) NOT NULL,
phone   INT(12) NOT NULL,
in_dpt  CHAR(20) NOT NULL,
```
3. select语句
```
SELECT * FROM table_name; 这条语句用于查看一张表中的所有内容。
SELECT 要查询的列名 FROM 表名字 WHERE 限制条件;
SELECT name,age,phone FROM employee WHERE name='Mary';
SELECT name,age FROM employee WHERE age<25 OR age>30; # 筛选出 age 大于 25，且 age 小于 30
SELECT name,age FROM employee WHERE age>25 AND age<30;
SELECT name,age,phone,in_dpt FROM employee WHERE in_dpt IN ('dpt3','dpt4');
SELECT name,age,phone,in_dpt FROM employee WHERE in_dpt NOT IN ('dpt1','dpt3');
SELECT name,age,phone FROM employee WHERE phone LIKE '1101__'; #其中 _ 代表一个未指定字符，% 代表不定个未指定字符
SELECT name,age,phone FROM employee WHERE name LIKE 'J%';
SELECT name,age,salary,phone FROM employee ORDER BY salary DESC; # 关键词 ASC 和 DESC 可指定升序或降序排序
```
 4.内置函数和计算（5个）
```
 函数名：	 COUNT	SUM 	AVG	    MAX	  MIN
   作用：	计数	 求和	 求平均值	最大值	最小值
```
```
SELECT MAX(salary) AS max_salary,MIN(salary) FROM employee;  # 使用 AS 关键词可以给值重命名
```
多个表查询
```
SELECT of_dpt,COUNT(proj_name) AS count_project FROM project GROUP BY of_dpt
HAVING of_dpt IN       # having和where的作用一样
(SELECT in_dpt FROM employee WHERE name='Tom');
```
连接查询
在处理多个表时，子查询只有在结果来自一个表时才有用。但如果需要显示两个表或多个表中的数据，这时就必须使用连接 (join) 操作。 连接的基本思想是把两个或多个表当作一个新的表来操作，如下：
```
SELECT id,name,people_num
FROM employee,department
WHERE employee.in_dpt = department.dpt_name
ORDER BY id;
```
等同于
```
SELECT id,name,people_num
FROM employee JOIN department
ON employee.in_dpt = department.dpt_name
ORDER BY id;
```
5.数据库及表的修改
```
RENAME TABLE 原名 TO 新名字;

ALTER TABLE 原名 RENAME 新名;

ALTER TABLE 原名 RENAME TO 新名;
```
```
RENAME TABLE table_1 TO table_2;
```
```
DROP TABLE 表名字; #删除一张表
```
```
DROP TABLE table_2;
```
```
ALTER TABLE 表名字 ADD COLUMN 列名字 数据类型 约束;
或：
ALTER TABLE 表名字 ADD 列名字 数据类型 约束;
ALTER TABLE employee ADD height INT(4) DEFAULT 170;
ALTER TABLE employee ADD weight INT(4) DEFAULT 120 AFTER age; # 放在age后面
ALTER TABLE employee ADD test INT(10) DEFAULT 11 FIRST; # 放在第一列
```
```
ALTER TABLE 表名字 DROP COLUMN 列名字;

或： ALTER TABLE 表名字 DROP 列名字;
ALTER TABLE employee DROP test;
```
```
ALTER TABLE 表名字 CHANGE 原列名 新列名 数据类型 约束;
ALTER TABLE employee CHANGE height shengao INT(4) DEFAULT 170;
```
```
ALTER TABLE 表名字 MODIFY 列名字 新数据类型;
```
```
UPDATE 表名字 SET 列1=值1,列2=值2 WHERE 条件;
UPDATE employee SET age=21,salary=3000 WHERE name='Tom';
```
```
DELETE FROM 表名字 WHERE 条件;
DELETE FROM employee WHERE name='Tom';
```


6.其他操作
```
ALTER TABLE 表名字 ADD INDEX 索引名 (列名);

CREATE INDEX 索引名 ON 表名字 (列名);
ALTER TABLE employee ADD INDEX idx_id (id);  #在employee表的id列上建立名为idx_id的索引

CREATE INDEX idx_name ON employee (name);   #在employee表的name列上建立名为idx_name的索引
如果你要查找某一用户，使用语句 select * from user where username=? 和 select * from user where note=? 
性能是有很大差距的，对建立了索引的用户名进行条件查询会比没有索引的个性签名条件查询快几倍。
```
视图
```
CREATE VIEW 视图名(列a,列b,列c) AS SELECT 列1,列2,列3 FROM 表名字;
CREATE VIEW v_emp (v_name,v_age,v_phone) AS SELECT name,age,phone FROM employee;
```
导入
```
source *.sql 
```
```
数据文件导入，可以把一个文件里的数据保存进一张表。导入语句格式为：
LOAD DATA INFILE '文件路径和文件名' INTO TABLE 表名字;
```
```
导入导出的文件都必须在指定的路径下进行，在 mysql 终端中查看路径变量：
```
```
mysql> show variables like '%secure%';
+--------------------------+-----------------------+
| Variable_name            | Value                 |
+--------------------------+-----------------------+
| require_secure_transport | OFF                   |
| secure_auth              | ON                    |
| secure_file_priv         | /var/lib/mysql-files/ |
+--------------------------+-----------------------+
3 rows in set (0.00 sec)
```
```
secure_file_priv 变量指定安全路径为 /var/lib/mysql-files/ ，要导入数据文件，需要将该文件移动到安全路径下。
sudo cp -a /home/shiyanlou/Desktop/SQL6 /var/lib/mysql-files/
```
导出
```
SELECT 列1，列2 INTO OUTFILE '文件路径和文件名' FROM 表名字;
SELECT * INTO OUTFILE '/var/lib/mysql-files/out.txt' FROM employee;
```
备份
```
mysqldump -u root 数据库名>备份文件名;   #备份整个数据库

mysqldump -u root 数据库名 表名字>备份文件名;  #备份整个表
```
mysqldump 是一个备份工具，因此该命令是在终端中执行的，而不是在 mysql 交互环境下
```
cd /home/shiyanlou/
mysqldump -u root mysql_shiyan > bak.sql;
```
恢复
```
CREATE DATABASE test;  #新建一个名为test的数据库
mysql -u root test < bak.sql
```
```
