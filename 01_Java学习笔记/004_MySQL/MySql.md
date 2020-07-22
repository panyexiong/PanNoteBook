---
title: mysql 笔记
---



---



# MySQL基础知识

## 数据库的启动与暂停

### 启动：

```mysql
net start mysql
```

### 停止：

```mysql
net stop mysql
```

## 数据库的登陆与退出

方式1：通过mysql自带的客户端

只限于root用户

### 登陆：

mysql [-h主机名 -p端口号 ] -u用户名 -p密码

```mysql
eg : mysql -h localhost -p3306 -u root -p
```

### 退出：

```mysql
exit 或 ctrl+c
```

## MySQL常用命令

### 1.查看当前所有数据库

```mysql
show databases;
```

### 2.打开指定库

```mysql
use sys;
```

### 3.查看当前库的所有表

```mysql
show tables;
```

### 4.查看其他库的所有表

```mysql
show tables from 库名;
```

### 5.创建表

```mysql
creat table 表名（

​           列名 列类型

​           列名 列类型

​           ...

);
```

### 6.查看表结构

```mysql
desc 表名
```

### 7.查看服务器版本

方式一：

```mysql
select version();
```

方式二：

```mysql
mysql --version或mysql --V
```

## MySQL的语法规范

### 1.不区分大小写，但建议关键字大写，表名，列名小写

### 2.每条命令用分号结尾

### 3.每条命令根据需要可以缩进和换行

### 4.注释

​    单行注释：#注释文字

   单行注释：-- 注释文字 

   多行注释：/*注释文字*/



# DQL语言

## 进阶一：基础查询

/*
语法：
select 查询列表
from 表名;
特点：
1.查询列表可以是：表中的字段、常量值、表达式、函数
2.查询的结果式一个虚拟的表格
*/

```mysql
use mysql;
```

### 1.查询表中的单个字段

```mysql
select last_name from employees;
```

### 2.查询表中的多个字段

```mysql
select last_name,salary,email from employees;
```

### 3.查询表中的所有字段

```mysql
select `` from duils;
select * from duils
```

### 4.查询常量值

```mysql
select 100;
select `john`;
```

### 5.查询表达式

```mysql
select 100*98
```

### 6.查询函数

```mysql
select version();
```

### 7.起别名

1）.便于理解

2）.如果要查询的字段有重名的情况，使用别名可以区分开来

方式一：

```mysql
select 100*98 as 结果;
select last_name as 姓，first name as 名 from employees;
```

方式二 ：使用空格

```mysql
select last_name 姓，first_name 名 from employees;
```

案例:查询salary，显示结果为 out put 

```mysql
select salary as “out put” from employees
```

### 8.去重

案例：查询员工表中涉及到的所有的部门编号

```mysql
select distinct department_id from employees;
```

### 9.+号的作用

mysql中的+号的作用：

仅仅只有一个功能：运算符

select 100+90 ; 两个操作数都为数值型，则作加法运算

select ‘123’+90;只要其中一方为字符型，试图将字符型数值转化为数值型，

​                            如果转化成功，则继续做加法运算；

select ‘john‘+90 如果转化失败，则将字符型数值转化为0

select null +10;只要其中一方为null，则给出结果肯定为null

案例：查询员工名和姓连接成一个字段，并显示为 姓名

```mysql
select 
      CONCAT(last_name,first_name) as 姓名
from 
      employees;
```

### 10.显示出表employees的全部列，各个列之间用逗号连接，列头显示成out_put

```mysql
select
      ifnull(commission_pct,0) as 奖金率,commission_pct
from
      employees;
```



```mysql
SELECT
  CONCAT(`first_name`,',',`last_name`,',',`job_id`,',',IFNULL(commission_pct,0)) AS out_put
FROM
    employees;
```

## 进阶二：条件查询

语法：

```mysql
select 
      查询列表
from 
      表名
where 
      筛选条件
```

分类：

1. 按条件表达式筛选

   简单条件运算符：> < = !=   <>  >= <=

2. 按逻辑表达式筛选

   逻辑运算符：

   ​                    &&     ||   !

   ​                   and     or   not

3. 模糊查询

   like 

   between and 

   in

   is null

### 一、按条件表达式筛选

案例1：查询工资>12000的员工信息

```mysql
SELECT
	    *
FROM
      employees
WHERE
      salary>12000;
```

案例2：查询部门编号不等于90号的员工名和部门编号

```mysql
SELECT
			last_name,department_id
FROM
      employees
WHERE
		  department_id!=90;
		或
		  department_id<>90
```

### 二、按逻辑表达式筛选

案例1：查询工资在10000到20000之间的员工名、工资以及奖金

```mysql
SELECT
      last_name,salary,commission_pct
FROM
		  employees
WHERE
      salary>=10000 AND  salary <=20000;
```

案例2：查询部门编号不是在90到110之间，或者工资高于15000的员工信息

```mysql
SELECT
       *
FROM
       employees
WHERE
       department_id<90 OR department_id>110 OR salary>15000;
     或
       not(department_id>=90 and department_id<=110) or salary>15000;
```

### 三、模糊查询

1. #### like

   特点：

   一般与通配符搭配使用

   ​           通配符：

   ​            %  任意多个字符，包含0个字符

   ​            _    任意单个字符

   案例1：查询员工名中包含字符a的员工信息

   ```mysql
   SELECT
          *
   FROM
          employees
   WHERE
          department_id<90 OR department_id>110 OR salary>15000;
   ```

   案例2：查询员工名中第三个字符为n，第五个字符为l的员工名和工资

   ```mysql
   SELECT
         last_name,salary
   FROM
   		  employees
   WHERE
         last_name LIKE '__n_l%';
   ```

   案例3：查询员工名中第二个字符为_的员工名

   ```mysql
   SELECT
         last_name
   FROM
          employees
   WHERE
          last_name LIKE '_\_%';
         或
          last_name LIKE  '_$_%' ESCAPE '$';
   ```

   注：ESCAPE  定义转义字符

2. #### between and

   注意：

   1. 使用between and 可以提高语句的简洁度
   2. 包含临界值
   3. 两个临界值不要调换顺序

    案例1：查询员工编号在100到120之间的员工信息

   ```mysql
   SELECT 
         *
   FROM
         employees
   WHERE
         employee_id BETWEEN 100 AND  120
   ```

3. #### in

   特点：

   1. 使用in提高语句简洁度
   2. in列表的值类型必须一致或兼容

   案例：查询员工的工种编号是IT_PROG、AD_VP、AD_PRES中的一个员工名和工种编号

   ```mysql
   SELECT
   		 last_name,job_id
   FROM
        employees
   WHERE
   		 job_id IN('IT_PROG','AD_VP','AD_PRES');
   ```

4. #### is null

   特点：

   =或<>不能用于判断null值

   is null 或is not null 可以判断null值

   案例1：查询没有奖金的员工名和奖金率

   ```mysql
   SELECT
         last_name,commission_pct
   FROM
         employees
   WHERE
         commission_pct IS NULL;
   ```

   安全等于：<=>

   ```mysql
   SELECT
         last_name,commission_pct
   FROM
         employees
   WHERE
         commission_pct <=> NULL;
   ```

   ```mysql
   案例2：查询工资为20000的员工信息
   SELECT
         last_name,salary
   FROM
   		  employees
   WHERE
         salary <=>20000;
   ```

   is null  pk  <=>

   is null :仅仅可以判断null值，可读性较高，建议使用

   <=>     :既可以判断null值，又可以判断普通的数值，可读性较低

5. #### 经典面试题

   试问：

   ```mysql
   SELECT * FROM employees;
   ```

   和

   ```mysql
   SELECT 
          *
   FROM
          employees
   WHERE
          commission_pct LIKE '%%' AND last_name LIKE 'LIKE'
   ```

   的结果是否一致？请说明原因

   答：不一致，因为commission_pct中存在NULL值。当所选条件中不存在NULL时，两条语句结果一致。

## 进阶三：排序查询

语法：

​         select  查询列表

​          from   表

​          【where 筛选条件】

​           order by  排序列表 【asc  desc】

特点：

1. asc代表的是升序，desc代表的是降序，如果不写，默认是升序。
2. order by 子句中可以支持单个字段、多个字段、表达式、函数、别名
3. order by子句一般是放在查询语句的最后面，limit子句除外。

案例1：查询员工信息，要求工资从高到低排序

```
从高到低：SELECT * FROM employees ORDER BY salary DESC;
从低到高：SELECT * FROM employees ORDER BY salary ASC;
```

案例2：【添加筛选条件】查询部门编号>=90的员工信息，按入职时间的先后进行排序

```mysql
SELECT *
FROM  employees
WHERE department_id>=90
ORDER BY hiredate ASC;
```

案例3：【按表达式排序】按年新的高低显示员工的信息和年薪

```mysql
SELECT *,salary*12*(1+IFNULL(commission_pct,0)) AS 年薪
FROM employees
ORDER BY salary*12*(1+IFNULL(commission_pct,0)) DESC;
```

案例4：【按别名排序】按年新的高低显示员工的信息和年薪

```mysql
SELECT *,salary*12*(1+IFNULL(commission_pct,0)) AS 年薪
FROM employees
ORDER BY 年薪 DESC;
```

案例5：【按函数排序】按姓名的长度显示员工的姓名和工资

```mysql
SELECT LENGTH(last_name) 字节长度,last_name,salary
FROM employees
ORDER BY LENGTH(last_name) DESC;
```

案例6：【按多个字段排序】查询员工信息，要求先按工资升序，再按员工编号降序

```mysql
SELECT *
FROM employees
ORDER BY salary ASC,employee_id DESC;
```

## 进阶四：常见函数

概念：类似于java的方法，将一组逻辑语句封装在方法体中，对外暴露方法名

好处：1. 隐藏了实现细节     2. 提高代码的重用性

调用：select  函数名（实参列表）【from 表】；

特点：1.叫什么（函数名）

​            2.干什么（函数功能）

分类：

​            1.单行函数

​             如：concat、length、ifnull等

​            2.分组函数

​            功能：做统计使用，又称为统计函数、聚合函数、组函数。

### 一、字符函数

1. length 获取参数值的字节个数

   ```mysql
   SELECT LENGTH('john');
   SELECT LENGTH('张三丰hahaha');
   
   #查看
   SHOW VARIABLES LIKE '%char%'
   ```

2. concat 拼接字符串

   ```mysql
   SELECT CONCAT(last_name,'_',first_name) FROM employees
   ```

3. upper、lower

   ```mysql
   SELECT UPPER('john');
   SELECT LOWER('joHn');
   #示例：将姓变大写，名变小写，然后拼接
   SELECT CONCAT(UPPER(last_name),LOWER(first_name))  姓名 FROM employees
   ```

4. substr、substring

   注意：索引从1开始

   ```mysql
   #截取从指定索引处后面所有字符
   SELECT SUBSTR('李莫愁爱上了陆展元',7) out_put;
   
   #截取从指定索引处指定字符长度的字符
   SELECT SUBSTR('李莫愁爱上了陆展元',1,3)  out_put;
   ```

   案例：姓名中首字母大写，其他字符小写然后用_拼接，显示出来。

   ```mysql
   SELECT CONCAT(UPPER(SUBSTR(last_name,1,1)),'_',LOWER(SUBSTR(last_name,2))) out_put FROM employees
   ```

5. instr 返回子串第一次出现的索引，如果找不到返回0

   ```mysql
   SELECT INSTR('杨不悔爱上了殷六侠','殷六侠') AS out_put;
   ```

6. trim 去掉子串的前部和尾部中指定的字符

   ```mysql
   SELECT TRIM('a' FROM 'aaaaIaaaaamalienaaaaaa') AS out_put;
   #out_put:Iaaaaamalien
   ```

7. lpad 用指定的字符实现左填充指定长度

   ```mysql
   SELECT LPAD('殷素素',10,'*') AS out_put;
   ```

8. rpad 用指定的字符实现右填充指定长度

   ```mysql
   SELECT RPAD('殷素素',10,'*') AS out_put;
   ```

9. replace 替换

   ```mysql
   SELECT REPLACE('周芷若周芷若周芷若周芷若张无忌爱上了周芷若周芷若周芷若周芷若','周芷若','赵敏') AS out_put;
   ```

### 二、数学函数

1. round 四舍五入

   ```mysql
   SELECT ROUND(1.65);
   SELECT ROUND(1.567,2);
   ```

2. ceil 向上取整，返回>=该参数的最小整数

   ```mysql
   SELECT CEIL(-1.02);
   ```

3. floor 向下取整，返回<=该参数的最大整数

   ```mysql
   SELECT FLOOR(-9.99);
   ```

4. truncate 截断

   ```mysql
   SELECT TRUNCATE(1.6999,1);
   ```

5. mod 取余

   mod(a,b)   a-a/b*b     #如果除号两边均为整数，则商取整。

   eg: mod(-10，-3)   -10-(-10)/(-3)*(-3)=-1

   ```mysql
   SELECT MOD(10,-3);
   ```

### 三、日期函数

1. now 返回当前系统日期+时间

   ```mysql
   SELECT NOW();
   ```

2. curdate 返回当前系统日期，不包含时间

   ```mysql
   SELECT CURDATE();
   ```

3. curtime 返回当前时间，不包含日期

   ```mysql
   SELECT CURTIME();
   ```

4. 可以获取指定的部分，年、月、日、小时、分钟、秒

   ```mysql
   SELECT YEAR(NOW())  年;
   
   SELECT YEAR('1998-1-1') 年;
   
   SELECT YEAR(hiredate) 年 FROM employees;
   
   SELECT MONTH(NOW()) 月;
   SELECT MONTHNAME(NOW()) 月;
   ```

5. str_to_date 将字符通过指定的格式转换成日期

   ![1562074633971](C:\Users\ZYW\AppData\Roaming\Typora\typora-user-images\1562074633971.png)
   
   ```mysql
   SELECT STR_TO_DATE('1998-3-2','%Y-%c-%d') AS out_put;
   ```
   
   ```mysql
   SELECT MONTH(NOW()) 月;
   
   SELECT MONTHNAME(NOW()) 月;
   
   SELECT STR_TO_DATE('1998-3-2','%Y-%c-%d') AS out_put;
   ```
   
   查询入职日期为1992-4-3的员工信息
   
   ```mysql
   SELECT * FROM employees WHERE hiredate = '1992-4-3';
   
   #当hiredate保存的日期格式不是规范格式时，例如4-3 1992，利用str_to_date进行转化后进行寻找。
   SELECT * FROM employees WHERE hiredate = STR_TO_DATE('4-3 1992','%c-%d %Y');
   ```
   
6. date_format 将日期转换成字符

   ```mysql
   SELECT DATE_FORMAT(NOW(),'%y年%m月%d日') AS out_put;
   ```

   查询有奖金的员工名和入职日期(xx月/xx日 xx年)

   ```mysql
   SELECT last_name,DATE_FORMAT(hiredate,'%m月/%d日 %y年') 入职日期 FROM employees WHERE commission_pct IS
   NOT NULL;
   ```

### 四、其他函数

```mysql
#查看版本号
SELECT version();

#查看当前库
SELECT DATABASE();

#查看当前用户
SELECT USER();
```

### 五、流程控制函数

1. if函数 ： if  else 的效果

   ```mysql
   SELECT IF(10<5,'大','小');
   ```

   ```mysql
   SELECT last_name,commission_pct,IF(commission_pct IS NULL,'没奖金','有奖金') FROM employees;
   ```

2. case函数的使用一： switch case 的效果

   case 要判断的字段或表达式

   when 常量1  then 要显示的值1或语句1；

   when 常量2  then 要显示的值2或语句2；

   ...

   else 要显示的值n或语句n；

   end

   注意：case与select一起使用时，case当作表达式来用，then后面不能使用语句；当case独立使用时，then后面可以跟语句。

   案例： 查询员工的工资，要求

   ​             部门号=30，显示的工资为1.1倍

   ​             部门号=40，显示的工资为1.2倍

   ​			 部门号=50，显示的工资为1.3倍

   ​			 其他部门，显示的工资为原工资

   ```mysql
   SELECT salary 原工资,department_id,
   CASE department_id
   	WHEN 30 THEN
   		salary*1.1
   	WHEN 40 THEN
   	  salary*1.2
     WHEN 50 THEN
   	  salary*1.3
   	ELSE
   		salary
   END AS 新工资
   FROM employees;
   ```


习题：

1. 查询员工号、姓名、工资、以及工资提高百分之20后的结果(new salary)

   ```mysql
   #在重新命名别名时，别名中出现空格时，需用“”，将别名括起来。
   SELECT employee_id,last_name,salary,salary*1.2  "new salary" FROM employees;
   ```

2. 将员工的姓名按首字母排序，并写出姓名的长度（length）

   ```mysql
   SELECT LENGTH(last_name) 长度,SUBSTR(last_name,1,1) 首字母,last_name FROM employees
   ORDER BY 首字母;
   ```

3. 做一个查询，产生下面的结果

   <last_name> earns <salary> monthly but wants <salary*3>

   示例：K_ing earn 24000.00 monthly but wants 72000.00

   ```mysql
   SELECT CONCAT(last_name," earn ",salary," monthly but wants ",salary*3)
   FROM employees;
   ```

4. 使用case-when，按照下面的条件：

   job                          grade

   AD_PRES                   A

   ST_MAN                    B 

   IT_PROG                   C 

   ```mysql
   SELECT last_name,job_id AS job,
   CASE job_id
   	WHEN 'AD_PRES' THEN 'A'
   	WHEN 'ST_MAN' THEN 'B'
   	WHEN 'IT_PROG' THEN 'C'
   	WHEN 'SA_PRE' THEN 'D'
   	WHEN 'AD_PRES' THEN 'E'
   END AS Grade
   FROM employees;
   ```

### 六、分组函数

功能：用作统计使用，又称聚合函数或统计函数或组函数

分类：

sum  求和、avg 平均值、max  最大值、min  最小值、count  计算个数

特点：

1、sum、avg一般用于使用处理数值型

​      max、min、count可以处理任何类型

2、以上分组函数都忽略null值

3、可以和distinct搭配实现去重的运算

4、count函数的介绍

一般使用count(*)统计行数

5、和分组函数一同查询的字段要求是group by字段

1. 简单的使用

   ```mysql
   SELECT SUM(salary) FROM employees;
   SELECT AVG(salary) FROM employees;
   SELECT MIN(salary) FROM employees;
   SELECT MAX(salary) FROM employees;
   SELECT COUNT(salary) FROM employees;
   ```

   ```mysql
   SELECT SUM(salary) 和,ROUND(AVG(salary),2) 平均,MAX(salary) 最高,MIN(salary) 最低,COUNT(salary) 个数
   FROM employees;
   ```

2. 和distinct搭配

   ```mysql
   SELECT SUM(DISTINCT salary),SUM(salary) FROM employees;
   ```

3. count函数的详细介绍

   ```mysql
   #计算行数
   SELECT COUNT(*) FROM employees;
   #计算行数count()中的1可以被任意常量替代
   SELECT count(1) FROM employees;
   ```

   效率：

   MYISAM存储引擎下，count(*)的效率高一些

   INNODB存储引擎下，count(*)和count(1)的效率差不多，比count(字段)要高一些

   习题：

   查询员工表中的最大入职时间和最小入职时间的相差天数(DIFFRENCE)

   ```mysql
   select DATEDIFF(MAX(hiredate),MIN(hiredate)) diffrence FROM employees;
   ```

   查询部门编号为90的员工个数

   ```mysql
   SELECT COUNT(*)  个数 FROM employees WHERE department_id = 90;
   ```

## 进阶五：分组查询

语法：

​          select  分组函数，列（要求出现在group by后面）

​          from   表

 		 【where 筛选条件】

​			group by  分组的列表

​			【order by 子句】

注意：

​			查询列表必须特殊，要求是分组函数和group by后出现的字段

特点：

​            1、分组查询中的筛选条件分为两类

​                                                数据源                     位置                                           关键字

​             分组前筛选                原始表                     group by子句的前面                where

​             分组后筛选               分组后的结果集       group by子句的后面               having

​             （1）分组函数做条件肯定是放在having子句中

​             （2）能用分组前筛选的，就优先考虑分组前筛选

​            2、group by 子句支持单个字段分组，多个字段分组（多个字段之间用逗号隔开没有顺序要求），表达式或函数（用得较少）

​            3、也可以添加排序（排序放在整个分组查询的最后）

案例1：查询每个工种的最高工资

```mysql
SELECT MAX(salary),job_id
FROM employees
GROUP BY job_id;
```

案例2：查询每个位置上的部门个数

```mysql
SELECT COUNT(*),location_id 
FROM departments 
GROUP BY location_id;
```

### 添加筛选条件

案例3：查询邮箱中包含a字符的，每个部门的平均工资

```mysql
SELECT AVG(salary),department_id
FROM employees
WHERE email LIKE '%a%'
GROUP BY department_id;
```

案例2：有奖金的每个领导手下员工的最高工资

```mysql
SELECT MAX(salary),manager_id
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY manager_id;
```

案例1：查询哪个部门的员工个数>2

(1) 查询每个部门的员工个数

```mysql
SELECT COUNT(*),department_id
FROM employees
GROUP BY department_id;
```

(2) 根据(1)的结果进行筛选，查询哪个部门的员工个数>2

```mysql
SELECT COUNT(*),department_id
FROM employees
GROUP BY department_id
HAVING COUNT(*)>2;
```

案例2：查询每个工种有奖金的员工最高工资>12000的工种编号和最高工资

(1) 查询每个工种有奖金的员工的最高工资

```mysql
SELECT MAX(salary),job_id
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY job_id;
```

（2）根据（1）中的结果继续筛选，最高工资>12000

```mysql
SELECT MAX(salary),job_id
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY job_id
HAVING MAX(salary)>12000;
```

案例3：查询领导编号>102的每个领导手下的最低工资>5000的领导编号是哪个，以及其最低工资

(1) 查询每个领导手下的员工固定最低工资

```mysql
SELECT manager_id,MIN(salary)
FROM employees
GROUP BY manager_id;
```



(2) 添加筛选条件：编号>102

```mysql
SELECT manager_id,MIN(salary)
FROM employees
WHERE manager_id>102
GROUP BY manager_id;
```



(3) 添加筛选条件：最低工资>5000

```mysql
SELECT manager_id,MIN(salary)
FROM employees
WHERE manager_id>102
GROUP BY manager_id
HAVING  AND MIN(salary)>5000;
```

### 按表达式或函数分组

案例：按员工姓名的长度分组，查询每一组的员工个数，筛选员工个数>5的有哪些

（1）查询每个长度的员工个数

```mysql
SELECT COUNT(*),LENGTH(last_name) len_name
FROM employees
GROUP BY LENGTH(last_name);
```



（2）添加筛选条件

```mysql
SELECT COUNT(*),LENGTH(last_name) len_name
FROM employees
GROUP BY LENGTH(last_name)
HAVING COUNT(*)>5;
```

### 按多个字段分组

案例：查询每个部门每个员工的员工的平均工资

```mysql
SELECT job_id,department_id,AVG(salary)
FROM employees
GROUP BY job_id,department_id;
```

添加排序

案例：查询每个部门每个工种的员工的平均工资，并且按平均工资的高低显示

```mysql
SELECT job_id,department_id,AVG(salary) a
FROM employees
WHERE department_id IS NOT NULL
GROUP BY job_id,department_id
HAVING a>1000
ORDER BY a DESC;
```

习题

1. 查询各job_id的员工工资的最大值、最小值、平均值、总和，并按job_id升序

   ```mysql
   SELECT job_id,MAX(salary),MIN(salary),AVG(salary),SUM(salary)
   FROM employees
   GROUP BY job_id
   ORDER BY job_id;
   ```

2. 查询员工最高工资和最低工资的差距（DIFFRENCE）

   ```mysql
   SELECT MAX(salary)-MIN(salary) DIFFRENCE
   FROM employees;
   ```

3. 查询各个管理者手下员工的最低工资，其中最低工资不能低于6000，没有管理者的员工不计算在内

   ```mysql
   SELECT manager_id,MIN(salary)
   FROM employees
   WHERE manager_id IS not null
   GROUP BY manager_id
   HAVING MIN(salary)>=6000;
   ```

4. 查询所有部门的编号，员工数量和工资平均值，并按平均工资降序

   ```mysql
   SELECT department_id,COUNT(*),AVG(salary)
   FROM employees
   GROUP BY department_id
   ORDER BY AVG(salary) DESC;
   ```

5. 选择具有各个job_id的员工人数

   ```mysql
   SELECT job_id,COUNT(*) 人数
   FROM employees
   GROUP BY job_id;
   ```

## 进阶六：连接查询

含义：又称多表查询，当查询的字段来自于多个表时，就会用到连接查询



笛卡尔乘积现象：表1  有m行，表2有n行，结果=m*n行



发生原因：没有有效的连接条件

如何避免：添加有效的连接条件

分类：

​			按年代分类：

​                                  sq192标准：仅仅支持内连接

​                                  sq199标准【推荐】：支持内连接+外连接（左外+右外）+交叉连接

​            按功能分类：

​                                  内连接：

​                                               等值连接

​                                               非等值连接

​                                               自连接

​                                 外连接：

​                                                左外连接

​                                                右外连接

​                                                全外连接

​                                   交叉连接

### 一、sq192标准

#### 1、等值连接

好处：

​        （1）多表等值连接的结果为多表的交集部分

​		（2）n表连接，至少需要n-1个连接条件

​        （3）多表的顺序没有要求

​        （4）一般需要为表起别名

​        （5）可以搭配前面介绍的所有子句使用，比如排序、分组、筛选

案例1：查询女神名和对应的男神名

```mysql
SELECT name,boyName FROM boys,beauty
WHERE beauty.boyfriend_id = boys.id; 
```

案例2：查询员工名和对应的部门名

```mysql
SELECT last_name,department_name
FROM employees,departments
WHERE employees.`department_id`=departments.`department_id`;
```

2、为表起别名

好处：

（1）提高语句的简洁度

（2）区分多个重名的字段

注意：如果为表起了别名，则查询的字段就不能使用原来的表名去限制。

查询员工名、工种号、工种名

```mysql
SELECT e.last_name,e.job_id,j.job_title
FROM employees e,jobs j
WHERE e.`job_id`=j.`job_id`;
```

3、两个表的顺序可以调换

查询员工名、工种号、工种名

```mysql
SELECT e.last_name,e.job_id,j.job_title
FROM jobs j,employees e
WHERE e.`job_id`=j.`job_id`;
```

4、可以加筛选

案例1：查询有奖金的员工名、部门名

```mysql
SELECT last_name,department_name,commission_pct
FROM employees e,departments d
WHERE e.`department_id` = d.`department_id`
AND e.`commission_pct` IS NOT NULL;
```

案例2：查询城市名中第二个字符为o的部门名和城市名

```mysql
SELECT department_name,city
FROM departments d,locations l
WHERE d.`location_id` = l.`location_id`
AND city LIKE '_o%';
```

5、可以加分组

案例1：查询每个城市的部门个数

```mysql
SELECT COUNT(*) 个数,city
FROM departments d,locations l
WHERE d.`location_id` = l.`location_id`
GROUP BY city;
```

案例2：查询有奖金的每个部门的部门名和部门的领导编号和该部门的最低工资

```mysql
SELECT department_name,d.`manager_id`,MIN(salary)
FROM departments d,employees e
WHERE d.`department_id` = e.`department_id`
AND commission_pct IS NOT NULL
GROUP BY department_name,d.`manager_id`;
```

6、可以加排序

案例：查询每个工种的工种名和员工个数，并且按员工个数降序

```mysql
SELECT j.`job_title`,COUNT(*) 个数
FROM jobs j,employees e
WHERE e.`job_id`=j.`job_id`
GROUP BY j.`job_title`
ORDER BY 个数 DESC;
```

7、可以实现三表连接

案例：查询员工名、部门名和所在城市

```mysql
SELECT last_name,department_name,city
FROM employees e,departments d,locations l
WHERE e.department_id=d.department_id
AND d.location_id=l.location_id
```

```mysql
SELECT last_name,department_name,city
FROM employees e,departments d,locations l
WHERE e.department_id=d.department_id
AND d.location_id=l.location_id
AND city LIKE 's%'
ORDER BY department_name DESC;
```

#### 2、非等值连接

案例1：查询员工的工资和工资级别

```mysql
SELECT salary,grade_level
FROM employees e,job_grades g
WHERE salary BETWEEN g.lowest_sal AND g.highest_sal;
```

```mysql
SELECT salary,grade_level
FROM employees e,job_grades g
WHERE salary BETWEEN g.lowest_sal AND g.highest_sal
AND g.grade_level = 'A';
```

3、自连接

案例：查询员工名和上级的名称

```mysql
SELECT e.employee_id,e.last_name,m.employee_id,m.last_name
FROM employees e,employees m
WHERE e.manager_id=m.employee_id;
```

习题：

1、显示员工的最大工资、工资平均值

```mysql
SELECT MAX(salary),AVG(salary)
FROM employees;
```

2、查询员工表中的employee_id，job_id，last_name,按department_id降序，salary升序

```mysql
SELECT employee_id,job_id,last_name
FROM employees
ORDER BY department_id DESC,salary ASC;
```

3、查询员工表的job_id中包含a和e的，并且a在e的前面

```mysql
SELECT job_id
FROM employees
WHERE job_id LIKE '%ae%';
```

4、已知student，里面有id（学号），name（年级编号）

​     已知grade，里面有id（年纪编号），name（年级）

​     已知result，里面有id、score、sciroed

要求查询姓名、年级名和成绩

```mysql
SELECT s.name,g.name,r.score
FROM student s,grade g,result r
WHERE s.id = r.studentNo
and g.id = s.gradeid;
```

5、显示当前日期，以及去前后空格，截取子字符串的函数

```mysql
SELECT NOW()
SELECT TRIM(字符 from '');

SELECT SUBSTR(str,startIndex);
SELECT SUBSTR(str,startIndex,length);
```

习题：

1. 显示所有员工的姓名，部门号和部门名称

   ```mysql
   SELECT last_name,department_name,d.department_id
   FROM employees e,departments d
   WHERE e.department_id=d.department_id;
   ```

2. 查询90号部门员工的job_id和90号部门的location_id

   ```mysql
   SELECT job_id,location_id
   FROM employees e,departments d
   WHERE e.department_id=d.department_id
   AND e.department_id = '90';
   ```

3. 选择所有有奖金的员工的last_name,department_name,location_id,city

   ```mysql
   SELECT last_name,department_name,d.location_id,l.city
   FROM employees e,departments d,locations l
   WHERE e.commission_pct IS NOT NULL
   AND e.department_id=d.department_id
   AND d.location_id=l.location_id;
   ```

4. 选择city在Toronto工作的员工的last_name，job_id,department_id,department_name

   ```mysql
   SELECT last_name,job_id,e.department_id,department_name
   FROM employees e,locations l,departments d
   WHERE d.location_id=l.location_id
   AND e.department_id=d.department_id
   AND l.city='Toronto';
   ```

5. 查询每个工种、每个部门的部门名、工种名和最低工资

   ```mysql
   SELECT job_title,department_name,MIN(salary)
   FROM employees e,jobs j,departments d
   WHERE e.job_id=j.job_id
   AND e.department_id=d.department_id
   GROUP BY job_title,department_name;
   ```

6. 查询每个国家下的部门个数大于2的国家编号

   ```mysql
   SELECT country_id,COUNT(*) 部门个数
   FROM departments d,locations l
   WHERE d.`location_id`=l.`location_id`
   GROUP BY country_id
   HAVING 部门个数>2;
   ```

7. 选择指定员工的姓名，员工号，以及他的管理者的姓名和员工号，结果类似以下格式

   ![1562323539841](C:\Users\ZYW\AppData\Roaming\Typora\typora-user-images\1562323539841.png)
   
   ```mysql
   SELECT e.last_name employees,e.employee_id "Emp",m.last_name manger,m.employee_id "Mgr"
   FROM employees e,employees m
   WHERE e.manager_id=m.employee_id
   AND e.last_name='kochhar';
   ```
   

### 二、sq199语法

语法：

​          select  查询列表

​          from  表1 别名 【连接类型】

​		  join  表2  别名

​          on 连接条件

​          【where 筛选条件】

​          【group by 分组】

​          【having 筛选条件】

​           【oeder by 排序列表】

分类：

内连接（★）：inner

外连接

​               左外（★）：left 【outer】

​               右外（★）：right 【outer】

​               全外：full 【outer】

交叉连接：cross

#### 1、内连接

特点：

1. 可添加排序、分组、筛选
2. inner 可以省略
3. 筛选条件放在where后面，连接条件放在on后面，提高分离性，便于阅读
4. inner join 连接和sql192语法中的等值连接效果是一样的，都是查询多表的交集

语法：

​         select 查询列表

​         from 表1 别名

​         【inner】  join  表2 别名

​         on  连接条件

分类：

​			等值连接

​			非等值连接

1. 等值连接

   案例1.查询员工名、部门名

   ```mysql
   SELECT last_name,department_name
   FROM departments d
   INNER JOIN employees e
   ON e.`department_id`= d.`department_id`
   ```

   案例2.查询名字中包含e的员工名和工种名（添加筛选）

   ```mysql
   SELECT last_name,job_title
   FROM employees e
   INNER JOIN jobs j
   ON e.`job_id` = j.`job_id`
   WHERE e.`last_name` LIKE '%e%';
   ```

   案例3.查询部门个数>3的城市名和部门个数（添加分组+筛选）

​                 1.查询每个城市部门的部门个数

​                 2.在1的结果上筛选满足条件的

```mysql
    SELECT city,COUNT(*) 部门个数
	FROM departments d
	INNER JOIN locations l
	ON d.`location_id`= l.`location_id`
	GROUP BY city
	HAVING COUNT(*)>3;
```

​        案例4.查询部门的员工个数>3的部门名和员工个数，并按个数降序（添加排序）

```mysql
SELECT department_name,COUNT(*) 员工个数
FROM employees e
INNER JOIN departments d
ON e.`department_id`=d.`department_id`
GROUP BY department_name
HAVING COUNT(*)>3
ORDER BY COUNT(*) DESC;
```

​		案例5.查询员工名、部门名、工种名，并按部门名降序（添加三表连接）

```mysql
SELECT last_name,department_name,job_title
FROM employees e 
INNER JOIN departments d ON e.`department_id`= d.`department_id`
INNER JOIN jobs j ON e.`job_id` = j.`job_id`
ORDER BY department_name DESC;
```

1. 非等值连接

   查询员工的工资级别

   ```mysql
   SELECT salary,grade_level
   FROM employees e
   JOIN job_grades g
   ON e.`salary` BETWEEN g.`lowest_sal` AND g.`highest_sal`;
   ```

   查询工资级别的个数>20的个数，并且按工资级别降序

   ```mysql
   SELECT COUNT(*),grade_level
   FROM employees e
   JOIN job_grades g
   ON e.`salary` BETWEEN g.`lowest_sal` AND g.`highest_sal`
   GROUP BY grade_level
   HAVING COUNT(*)>20
   ORDER BY grade_level DESC;
   ```

1. 自连接

   查询姓名中包含字符K的员工名字、上级名字

   ```mysql
   SELECT e.last_name,m.last_name
   FROM employees e
   JOIN employees m
   ON e.`manager_id`=m.`employee_id`
   WHERE e.`last_name` LIKE '%k%';
   ```

#### 2、外连接

语法：

​         select 查询列表

​         from 表1 别名

​         left|right|full 【outer】  join  表2 别名

​         on  连接条件



应用场景：用于查询一个表中有，另一个表中没有的记录

特点：

1、外连接的查询结果为主表中的所有记录

​                  如果从表中有和他匹配的，则显示匹配的值

​                  如果从表中没有和他匹配的，则显示null 

​                  外连接查询结果=内连接结果+主表中有而从表中没有的记录

2、左外连接，left join左边的是主表

​      右外连接，right join右边的是主表

3、左外和右外交换两个表的顺序，可以实现同样的效果

4、全外连接=内连接的结果+表1中有但表2中没有的+表2中有但表1中没有的

引入：查询男朋友不在男神表的女神名

```mysql
#左外
SELECT b.name,bo.*
FROM beauty b
LEFT OUTER JOIN boys bo
ON b.`boyfriend_id` = bo.`id`
WHERE bo.`id` IS NULL;

#右外
SELECT b.name,bo.*
FROM boys bo
RIGHT OUTER JOIN beauty b
ON b.`boyfriend_id` = bo.`id`
WHERE bo.`id` IS NULL;
```

案例1.查询哪个部门没有员工

```mysql
#左外
SELECT d.*,e.employee_id
FROM departments d
LEFT OUTER JOIN employees e
ON d.`department_id` = e.`department_id`
WHERE e.`employee_id` IS NULL;

#右外
SELECT d.*,e.employee_id
FROM employees e
RIGHT OUTER JOIN departments d
ON d.`department_id` = e.`department_id`
WHERE e.`employee_id` IS NULL;
```

#### 3、交叉连接

语法：

select  查询列表

from 表1 别名

cross join 表2 别名；

特点：

 类似于笛卡尔乘积

```mysql
SELECT b.*,bo.*
FROM beauty b
CROSS JOIN boys bo;
```



习题

1、查询编号>3的女神的男朋友信息，如果有，则列出详细，如果没有，用null填充。

```mysql
SELECT b.id,b.name,bo.*
FROM beauty b
LEFT OUTER JOIN boys bo
ON b.`boyfriend_id` = bo.`id`
WHERE b.`id`>3;
```

2、查询哪个城市没有部门

```mysql
SELECT city
FROM locations l
LEFT OUTER JOIN departments d
ON d.`location_id` = l.`location_id`
WHERE d.`department_id` is NULL;
```

3、查询部门名为SAL或IT的员工信息

```mysql
SELECT e.*,d.department_name
FROM departments d
LEFT OUTER JOIN employees e
ON e.`department_id` = d.`department_id`
WHERE d.`department_name` IN('SAL','IT');
```

## 进阶七：子查询

含义：

出现在其他语句中的select语句，称为子查询或内查询

外部的查询语句，称为主查询或外查询

分类：

按子查询出现的位置：

​				select 后面：

​									仅仅支持量子查询

​				from后面：

​									支持表子查询

​				where 或having后面：★

​									标量子查询（单行）√

​									列子查询    （多行）√

​									行子查询

​		   	exists后面（相关子查询）

​                                      表子查询

按结果集的行列数不同：

​				标量子查询（结果集只有一行一列）

​				列子查询（结果集只有一列多行）

​				行子查询（结果集有一行多列）	

​				表子查询（结果集一般为多行多列）		

### 一、where或having后面

1、标量子查询（单行子查询）

2、列子查询（多行子查询）

3、行子查询（多列多行）

4、子查询的执行优先于主查询执行，主查询的条件用到了子查询的结果

特点：

1. 子查询放在小括号内

2. 子查询一般放在条件的右侧

3. 标量子查询，一般搭配着单行操作符使用

   > < > = < = < >

   列子查询，一般搭配着多行操作符使用：IN、ANY/SOME、ALL

非法使用标量子查询

案例1：谁的工资比Abel高？

1. 查询Abel的工资

   ```mysql
   SELECT salary
   FROM employees
   where last_name = 'Abel'
   ```

2. 查询员工的信息，满足salary>1结果

   ```mysql
   SELECT *
   FROM employees
   WHERE salary>(
   
   				SELECT salary
   				FROM employees
   				WHERE last_name = 'Abel'
   
   );
   ```

案例2：返回job_id与141号员工相同，salary比143号员工多的员工 姓名、job_id和工资

1. 查询141号员工的job_id

   ```mysql
   SELECT job_id
   FROM employees 
   WHERE employee_id = 141;
   ```

2. 查询143号员工的salary

   ```mysql
   SELECT salary
   FROM employees
   WHERE employee_id = 143
   ```

```mysql
SELECT last_name,job_id,salary
FROM employees 
WHERE job_id = (
			SELECT job_id
			FROM employees 
			WHERE employee_id = 141
) AND salary>(
			SELECT salary
			FROM employees
			WHERE employee_id = 143
);
```

案例3：返回公司工资最少的员工的last_name,job_id和salary

1. 查询公司的最低工资

   ```mysql
   SELECT MIN(salary)
   FROM employees;
   ```

```mysql
SELECT last_name,job_id,salary
FROM employees
WHERE salary=(
				SELECT MIN(salary)
				FROM employees
);
```

案例4：查询最低工资大于50号部门最低工资的部门id和其最低工资

1. 查询50号部门的最低工资

   ```mysql
   SELECT MIN(salary),department_id
   FROM employees 
   WHERE department_id = 50;
   ```

2. 查询每个部门的最低工资

   ```mysql
   SELECT MIN(salary),department_id
   FROM employees
   GROUP BY department_id;
   ```

```mysql
SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
HAVING MIN(salary)>(

			SELECT MIN(salary)
			FROM employees 
			WHERE department_id = '50'

);
```

多行子查询

案例1：返回location_id是1400或1700的部门中的所有员工姓名

1. 查询location_id是1400或1700的部门编号

   ```mysql
   SELECT department_id
   FROM departments 
   WHERE location_id IN(1400,1700)
   ```

2. 查询员工姓名，要求部门号是1列表中的某一个

   ```mysql
   SELECT last_name
   FROM employees
   WHERE department_id IN(
   				SELECT department_id
   				FROM departments 
   				WHERE location_id IN(1400,1700)
   
   );
   ```

   ```mysql
SELECT last_name
   FROM employees
   WHERE department_id = ANY(
	   			SELECT department_id
	   			FROM departments 
	   			WHERE location_id IN(1400,1700)
	
	);
	```
	



案例2：返回其他工种中比job_id为‘IT_PROG’工种任一工资低的员工的员工号、姓名、job_id以及salary

1. 查询job_id为‘IT_PROG’部门任一工资

   ```mysql
   SELECT salary
   FROM employees
   WHERE job_id = 'IT_PROG';
   ```

2. 查询员工号、姓名、job_id以及salary<1的任意一个

   ```mysql
   SELECT employee_id,last_name,job_id,salary
   FROM employees
   WHERE salary <ANY(
   				SELECT salary
   				FROM employees
   				WHERE job_id = 'IT_PROG'
   
   ) AND job_id <> 'IT_PROG';
   ```

案例3：返回其他部门中比job_id为‘IT_PROG’工种所有工资低的员工的员工号、姓名、job_id以及salary

```mysql
SELECT employee_id,last_name,job_id,salary
FROM employees
WHERE salary <ALL(
				SELECT salary
				FROM employees
				WHERE job_id = 'IT_PROG'

) AND job_id <> 'IT_PROG';
```

3、行子查询

案例：查询员工编号最小并且工资最高的员工信息

```mysql
SELECT *
FROM employees
WHERE (employee_id,salary) = (
				SELECT MIN(employee_id),MAX(salary)
				FROM employees
);
```

原始方法：

```mysql
SELECT *
FROM employees
WHERE employee_id =(
        SELECT MIN(employee_id)
				FROM employees
) AND salary = (
				 SELECT MAX(salary)
				 FROM employees
);
```

### 二、select后面

 注： 仅仅支持标量子查询

案例：查询每个部门的员工个数

```mysql
SELECT d.*,(
			SELECT COUNT(*)
			FROM employees e
			WHERE e.department_id = d.department_id

) 个数
FROM departments d;
```

案例2：查询员工号=102的部门名

```mysql
SELECT (
		SELECT department_name
		FROM departments d
		INNER JOIN employees e
		ON d.department_id = e.department_id
		WHERE e.employee_id = 102
) 部门名;
```

### 三、from后面

案例：查询每个部门的平均工资的工资等级

1. 查询每个部门的平均工资

   ```mysql
   SELECT AVG(salary),department_id
   FROM employees
   GROUP BY department_id;
   ```

2. 连接1的结果集和job_grades表，筛选条件平均工资between lowest_sal and highest_sal

   ```mysql
   SELECT ag_dep.*,g.`grade_level`
   FROM (
   			SELECT AVG(salary) ag,department_id
   			FROM employees
   			GROUP BY department_id
   ) ag_dep
   INNER JOIN job_grades g
   ON ag_dep.ag BETWEEN lowest_sal AND highest_sal;
   ```


### 四、exists后面（相关子查询）

语法：

exists（完整的查询语句）

结果：

1或0

```mysql
SELECT EXISTS (SELECT employee_id FROM employees WHERE salary = 30000);
```

案例1：查询员工名和部门名

```mysql
SELECT department_name
FROM departments d
WHERE EXISTS (
				SELECT *
				FROM employees e
				WHERE d.`department_id` = e.`department_id`
);
```

用 IN 写：

```mysql
SELECT department_name
FROM departments d
WHERE d.`department_id` IN(
				SELECT department_id
				FROM employees
);
```

案例2：查询没有女朋友的男神信息

用 IN 

``` mysql
select bo.*
from boys
where bo.id not in (
		select boyfriend_id
        from beauty
);
```

用exists 

```mysql
select bo.*
from boys bo
where not exists (
			select boyfriend_id
            from beauty b
            where bo.`id`=b.`boyfriend_id`
);
```

例题：

1. 查询和zlotkey相同部门的员工姓名和工资

   ```mysql
   SELECT last_name,salary
   FROM employees e
   WHERE e.department_id = (
   			SELECT department_id
   			FROM employees e
   			WHERE e.last_name = 'zlotkey'
   );
   ```

2. 查询工资比公司平均工资高的员工号，姓名和工资

   ```mysql
   SELECT employee_id,last_name,salary
   FROM employees e
   WHERE salary > (
   				SELECT AVG(salary)
   				FROM employees
   );
   ```

3. 查询各部门中工资比本部门平均工资高的员工的员工号，姓名和工资

   ```mysql
   SELECT employee_id,last_name,salary,e.department_id
   FROM employees e
   INNER JOIN (
   		SELECT AVG(salary) ag,department_id
   		FROM employees
   		GROUP BY department_id
   		
   ) ag_dep
   ON e.department_id = ag_dep.department_id
   WHERE salary > ag_dep.ag;
   ```

4. 查询和姓名中包含字母u的员工在相同部门的员工的员工号和姓名

   ```mysql
   SELECT last_name,employee_id
   FROM employees e
   WHERE department_id IN(
   		SELECT DISTINCT department_id
   		FROM employees
   		WHERE last_name LIKE '%u%'
   );
   ```

5. 查询在部门的location_id为1700的部门工作的员工的员工号

   ```mysql
   SELECT employee_id,last_name
   FROM employees e
   WHERE department_id = ANY(
   			SELECT DISTINCT department_id
   			FROM departments
   			where location_id = 1700
   );
   或
   SELECT employee_id,last_name
   FROM employees e
   INNER join(
   				SELECT DISTINCT department_id
   				FROM departments
   				WHERE location_id = 1700
   ) ag_dep
   ON e.`department_id` = ag_dep.`department_id`;
   
   ```

6. 查询管理者是K_ing的员工姓名和工资

   ```mysql
   SELECT last_name,salary
   FROM employees
   where manager_id = ANY(
   				SELECT employee_id
   				FROM employees
   				WHERE last_name = 'K_ing'
   );
   或
   SELECT last_name,salary
   FROM employees
   where manager_id = IN(
   				SELECT employee_id
   				FROM employees
   				WHERE last_name = 'K_ing'
   );
   ```

7. 查询工资最高的员工的姓名，要求first_name和last_name显示为一列，列名为姓.名

   ```mysql
   SELECT CONCAT(first_name,last_name) "姓.名"
   FROM employees
   WHERE salary = (
   				SELECT MAX(salary)
   				FROM employees
   );
   ```

## 进阶8：分页查询

应用场景：当要显示的数据，一页显示不全，需要分页提交sql请求

语法：

​				select  查询列表

​				 from 表

​				【join type join 表2

​					on 连接条件

​					where  筛选条件

​					group by 分组字段

​					having 分组后的筛选

​					order by 排序后的字段】

​					limit 【offset，】 size；

​			

​					offset要显示条目的起始索引（起始索引从0开始）

​					size 要显示的条目个数



特点：

1. limit语句放在查询语句的最后

2. 公式

   要显示的页数 page，每页的条目数size

   ```mysql
   select 查询列表
   from  表
   limit (page - 1)*size,size;
   
   ```

   

案例1：查询前五条员工信息

```mysql
SELECT * FROM employees LIMIT 0,5;
或
SELECT * FROM employees LIMIT 5;
```

案例2：查询第11条-第25条

```mysql
SELECT * FROM employees LIMIT 10,15;
```

案例3：有奖金的员工信息，并且工资较高的前10名显示出来

```mysql
SELECT *
FROM employees
WHERE commission_pct IS NOT NULL
ORDER BY salary DESC
LIMIT 10;
```



试说出查询语句中涉及的所有关键字，以及执行的先后顺序

select  查询列表                         ⑦

from   表                                     ①

连接类型 join 表2                       ②

on  连接条件                                3

where  筛选条件                         ④

group by 分组列表                      5

having 分组后的筛选                  ⑥

order by 排序列表                      ⑧

limit  偏移，条目数                     ⑨



习题:

1、查询工资最低的员工信息：last_name，salary

```mysql
SELECT last_name,salary
FROM employees
WHERE salary = (
				SELECT MIN(salary)
				FROM employees
);
```



2、查询平均工资最低的部门信息

1. ```mysql
   SELECT AVG(salary),department_id
   FROM employees
   GROUP BY department_id
   ```

2. ```mysql
   SELECT MIN(sal)
   FROM  (
   			SELECT AVG(salary) sal
   			FROM employees
   			GROUP BY department_id
   ) ag_dep
   ```

3. ```mysql
   SELECT AVG(salary),department_id
   FROM employees
   GROUP BY department_id
   HAVING AVG(salary) = (
   			SELECT MIN(sal)
   			FROM  (
   						SELECT AVG(salary) sal
   						FROM employees
   						GROUP BY department_id
   			) ag_dep
   )
   ```

4. ```mysql
   SELECT d.*
   FROM departments d
   WHERE d.`department_id` = (
   			SELECT department_id
   			FROM employees
   			GROUP BY department_id
   			HAVING AVG(salary) = (
   						SELECT MIN(sal)
   						FROM  (
   									SELECT AVG(salary) sal
   									FROM employees
   									GROUP BY department_id
   						) ag_dep
   			)
   
   );
   ```

   

3、查询平均工资最低的部门信息和该部门的平均工资

```mysql
SELECT d.*,ag
FROM departments d
JOIN (
			SELECT AVG(salary) ag,department_id
			FROM employees
			GROUP BY department_id
			ORDER BY AVG(salary)
			LIMIT 1

) ag_dep
ON d.department_id = ag_dep.department_id;
```



4、查询平均工资最高的job信息

```mysql
SELECT j.*
FROM jobs j
where job_id = (
			SELECT job_id
			FROM employees
			GROUP BY job_id
			ORDER BY AVG(salary) DESC
			LIMIT 1
);
```



5、查询平均工资高于公司平均工资的部门有哪些？

```mysql
SELECT department_id,ag
FROM  (
		SELECT AVG(salary) ag,department_id
		FROM employees
		GROUP BY department_id
) ag_dep
WHERE ag>(
		SELECT AVG(salary)
		FROM employees
);

```



6、查询出公司中所有manager的详细信息

```mysql
SELECT e.*
FROM employees e
WHERE employee_id =ANY(
		SELECT DISTINCT manager_id 
		FROM employees
);
```



7、各个部门中最高工资中最低的那个部门的最低工资是多少？

```mysql
SELECT salary,department_id
FROM employees
WHERE department_id =(
				SELECT department_id
				FROM employees
				GROUP BY department_id
				ORDER BY MAX(salary) 
				LIMIT 1
);
```



8、查询平均工资最高的部门的manger的详细信息：last_name，department_id,email，salary

```mysql
SELECT last_name,d.department_id,email,salary
FROM employees e
INNER JOIN departments d 
ON d.manager_id = e.employee_id
WHERE d.department_id = (
		SELECT department_id
		FROM employees
		GROUP BY department_id
		ORDER BY AVG(salary) DESC
		LIMIT 1
);
```

## 进阶9：联合查询

union 联合 合并：将多条查询语句的结果合并成一个结果

语法：

查询语句1

union

查询语句2

union

...



应用场景：

要查询的结果来自于多个表，且多个表没有直接的连接关系，但查询的信息一致时

特点：

1. 要求多条查询语句的查询列数是一致的
2. 要求多条查询语句的查询的每一列的类型和顺序最好一致
3. union关键字默认去重，如果使用union all可以包含重复项。

引入的案例：查询部门编号>90或邮箱包含a的员工信息

```mysql
SELECT * FROM employees WHERE email
 LIKE '%a%' OR department_id>90;
```

UNION

```mysql
SELECT * FROM employees WHERE email LIKE '%a%'
UNION
SELECT * FROM employees WHERE department_id>90;
```



案例：查询中国用户中男性的信息以及外国用户中男性的用户信息

```mysql
select id,cname,csex from t_ca where csex = '男'
UNION
select t_id,tName,tGender from t_ua where tGender = 'male';
```

# DML语言

数据操作语言：

插入：insert 

修改：update

删除：delete

## 一、插入语句

### 方式一：经典的插入

语法：

insert into 表名（列名，...） value (值1，...)；

1、插入的值的类型要与列的类型一致或兼容

```mysql
INSERT INTO beauty(id,name,sex,borndate,phone,photo,boyfriend_id)
VALUES(13,'唐艺昕','女','1990-4-23','18999999999',NULL,2);
```

2、不可以为null的列必须插入值，可以为null的列如何插入值？

方式一：

```mysql
INSERT INTO beauty(id,name,sex,borndate,phone,photo,boyfriend_id)
VALUES(13,'唐艺昕','女','1990-4-23','18999999999',NULL,2);
```

方式二：

```mysql
INSERT INTO beauty(id,name,sex,phone)
VALUES (14,'娜扎','女','138888888');
```

3、列的顺序是否可以调换（可以）

```mysql
INSERT INTO beauty(name,sex,id,phone)
VALUES ('蒋欣','女',15,'110');
```

4、列数和值的个数必须一致

```mysql
INSERT INTO beauty(name,sex,id,phone)
VALUES ('关晓彤','女',17,'110');
```

5、可以省略列名，默认所有列，而且列的顺序和表中的顺序一致

```mysql
INSERT INTO beauty
VALUES (18,'张飞','男',NULL,'119',NULL,NULL);
```

### 方式二：

语法：

insert into 表名

set 列名= 值，列名= 值...

```mysql
INSERT INTO beauty
SET id = 19,name = '刘涛',phone = '999';
```

两种方式PK

1、方式一支持插入多行，方式二不支持

```mysql
INSERT INTO beauty
VALUES(13,'唐艺昕','女','1990-4-23','18999999999',NULL,2)(23,'唐艺昕','女','1990-4-23','18999999999',NULL,2;
```

2、方式一支持子查询，方式二不支持

```mysql
INSERT INTO beauty(id,name,phone)
SELECT id,boyName,'1234567'
FROM boys WHERE id<3;
```

## 二、修改语句

1、修改单表的记录

语法：

update 表名

set 列=新值，列=新值，.....

where 筛选条件；

2、修改多表的记录【补充】

语法：

sql192语法：

update 表1 别名，表2，别名

set 列= 值，...

where 连接条件

and 筛选条件；



sql199语法：

update 表1 别名

inner|left|right| join 表2 别名

on 连接条件

set 列= 值

where 筛选条件；

修改单表的记录

案例1：修改beauty表中姓唐的女神的电话为1389988889

```mysql
UPDATE beauty SET phone = '13899888899'
WHERE name LIKE '唐%';
```

案例2：修改boys表中id号为2的名称为张飞，魅力值10

```mysql
UPDATE boys SET boyName='张飞',usercp=10
WHERE id = 2;
```

修改多表记录

案例1：修改张无忌的女朋友的手机号为114

```mysql
UPDATE boys bo
INNER JOIN beauty b ON bo.`id` = b.`boyfriend_id`
SET b.`phone`= '114'
WHERE bo.`boyName` = '张无忌';
```

案例2：修改没有男朋友的女神的男朋友编号都为2号

```mysql
UPDATE boys bo
RIGHT JOIN beauty b 
ON  bo.`id`=b.`boyfriend_id`
SET b.`boyfriend_id`=2
WHERE bo.`id` IS NULL;
```

## 三、删除语句

### 方式一：delete

语法：

1、单表的删除【★】

delet from 表名 where 筛选条件

2、多表的删除【补充】



方式二：truncate

语法：truncate table 表名；



方式一：delete 

1.单表的删除

案例：删除手机号以9结尾的女神信息

```mysql
DELETE FROM beauty WHERE phone LIKE '%9';
```



2.多表的删除

案例：删除张无忌的女朋友的信息

```mysql
DELETE b
FROM beauty b
INNER JOIN boys bo ON b.`boyfriend_id` = bo.`id`
WHERE bo.`boyName` = '张无忌';
```

案例：删除黄晓明的信息以及他的女朋友的信息

```mysql
DELETE b,bo
FROM beauty b
INNER JOIN boys bo
ON b.`boyfriend_id` = bo.`id`
WHERE bo.`boyName` = '黄晓明';
```

### 方式二：truncate语句

案例：将魅力值>100的男神信息删除

```mysql
TRUNCATE TABLE boys WHERE bo.usercp>100;
报错
```

delete pk truncate    【★】：

1. delete可以加where条件，truncate不能加
2. truncate删除，效率高一些
3. 假如要删除的表中有自增长列，如果用delete删除后，再插入数据，自增长列的值从断点开始，而truncate删除后，在插入数据，自增长列的值从1开始。
4. truncate删除没有返回值，delete删除后有返回值
5. truncate删除后不能回滚，delete删除后可以回滚。

## 习题：

1、运行以下脚本创建表myemploees

```mysql
USE myemployees;
CREATE TABLE myemployees(
		Id INT(10),
    First_name VARCHAR(10),
		Last_name VARCHAR(10),
		Userid VARCHAR(10),
		Salary DOUBLE (10,2)
);
CREATE TABLE users(
		id INT,
		userid VARCHAR(10),
		department_id INT
);

```

2、显示表myemployees的结构

```mysql
DESC my_employees;
```



3、向myemployees表中插入下列数据

![1563773606896](C:\Users\ZYW\AppData\Roaming\Typora\typora-user-images\1563773606896.png)

```mysql
INSERT INTO my_employees
SELECT 1,'Patel','Ralph','Rpatel',895 UNION
SELECT 2,'Dancs','Betty','Bdancs',860 UNION
SELECT 3,'Biri','Ben','Bbiri',1100 UNION
SELECT 4,'Newman','Chad','Cnewman',750 UNION
SELECT 5,'Ropeburn','Audrey','Aropebur',1550;
```

或

```mysql
INSERT INTO my_employees
VALUES (1,'patel','Ralph','Rpatel',895),
(2,'Dancs','Betty','Bdancs',860),
(3,'Biri','Ben','Bbiri',1100),
(4,'Newman','Chad','Chewman',750),
(5,'Ropebrn','Audrey','Aropebur',1550);
```

4、向users表中插入数据

![1563774629664](C:\Users\ZYW\AppData\Roaming\Typora\typora-user-images\1563774629664.png)

```mysql
INSERT INTO users
VALUES (1,'Rpatel',10),
(2,'Bdancs',10),
(3,'Bbiri',20);
```



5、将3号员工的last_name修改为“drelxer”

```mysql
UPDATE my_employees SET Last_name = 'drelxer' WHERE id = 3;
```



6、将所有工资少于900的员工的工资修改为1000

```mysql
UPDATE my_employees SET Salary=1000 WHERE Salary<900;
```



7、将userid 为Bbiri的USER表和my_employees表的记录全部删除

```mysql
DELETE u,e
FROM users u
JOIN my_employees e ON u.`userid` =e.`Userid`
WHERE u.`userid` = 'Bbiri';
```



8、删除所有数据

```mysql
DELETE FROM my_employees;
DELETE FROM users;
```



9、检查所作的修正

```mysql
SELECT * FROM my_employees;
SELECT * FROM users;
```



10、清空表my_employees

```mysql
TRUNCATE TABLE my_employees
```



# DDL语言

数据定义语言

库和表的管理



一、库的管理

创建、修改、删除

二、表的管理

创建、修改、删除



创建：create

修改：alter

删除：drop

## 一、库的管理

### 1、库的创建

语法：

creat  database  库名；

```mysql
creat database if not exists books;
```

### 2、库的修改

更改库的字符集

```mysql
ALTER DATABASE books CHARACTER SET gbk;
```

### 3、库的删除

```mysql
DROP DATABASE IF EXISTS books;
```

## 二、表的管理

### 1、表的创建 ★

creata table 表名（

​                        列名 列的类型【（长度）约束】，

​						列名 列的类型【（长度）约束】，

​						列名 列的类型【（长度）约束】，

​						...

​						列名 列的类型【（长度）约束】

）

案例：创建表book

```mysql
CREATE TABLE book(
					id INT,
					bname VARCHAR(20),
					price DOUBLE,
					authorid INT,
					publishdate datetime
					
);
```

### 2、表的修改

alter table 表名 add|drop|modify|change column 列名 【列类型 约束】；

1. 修改列名

   ```mysql
   ALTER TABLE book CHANGE COLUMN publishdate pubDate datetime;
   ```

   

2. 修改列的类型或约束

   ```mysql
   ALTER TABLE book MODIFY COLUMN pubdate TIMESTAMP;
   ```

   

3. 添加新列

   ```mysql
   ALTER TABLE author ADD COLUMN annual DOUBLE;
   ```

   

4. 删除列

   ```mysql
   ALTER TABLE author DROP COLUMN annual;
   ```

   

5. 修改表名

   ```mysql
   ALTER TABLE author RENAME TO book_author;
   ```

### 3、表的删除

```mysql

DROP TABLE IF EXISTS book_author;

show TABLES;
```

通用的写法：

```mysql
DROP DATABASE IF EXISTS 旧库名；
CREATE DATABASE 新库名；

DROP TABLE IF EXISTS 旧表名；
CREATE TABLE 表名();
```

### 4、复制

1. 仅仅复制表的结构

   ```mysql
   CREATE TABLE copy LIKE author;
   ```

   

2. 复制表的结构+数据

   ```mysql
   CREATE table copy2
   SELECT * FROM author;
   ```

   

3. 是要复制部分数据

   ```mysql
   CREATE TABLE copy3
   SELECT id,au_name
   FROM author
   where nation = '中国';
   ```

   

4. 仅仅复制某些字段

   ```mysql
   CREATE TABLE copy4
   SELECT id,au_name
   FROM author
   WHERE 0;
   ```



习题：

1、创建表dept1

​		name		null			type

​			id								int(7)

​		name							varchar(25)

```mysql
CREATE TABLE dept1(
		id int(7),
		NAME VARCHAR (25)
);
```



2、将表departments中的数据插入新表dept2中

```mysql
CREATE TABLE dept2
SELECT department_id,department_name
FROM myemployees.departments;
```



3、创建表emp5

​		name 		null			type

​		id									int（7）

​		first_name 					vachar(25)

​		last_name					  vachar(25)

​		dept_id							int(7)

```mysql
CREATE TABLE emp5(
			id INT(7),
			First_name VARCHAR(25),
			Last_name VARCHAR(25),
			Dept_id INT(7)
);
```



4、将列last_name的长度增加到50

```mysql
ALTER TABLE emp5 MODIFY Last_name VARCHAR(50);
```



5、根据表employees创建employees2

```mysql
CREATE TABLE employees2 LIKE myemployees.employees; 
```



6、删除表emp5

```mysql
DROP TABLE IF EXISTS emp5;
```



7、将表employees2重命名为emp5

```mysql
ALTER TABLE employees2 RENAME TO emp5;
```



8、在表dept和emp5中添加新列test_column，并检查所作的操作

```mysql
ALTER TABLE emp5 ADD COLUMN test_column INT;
DESC emp5;
```



9、直接删除表emp5中的列dept_id

```mysql
ALTER TABLE emp5 DROP COLUMN test_column;
```



### 常见的数据类型

​	数值型：

​				整型

​				小数：

​							定点数

​							浮点数

​	字符型：

​				较短的文本：char、varchar

​				较长的文本：text、blob（较长的二进制数据）

​	日期型：



### 一、整型

分类：

tinyint、smallint、mediumint、int/integer、bigint

1				2					3						4				8

特点：

1. 如果不设置无符号还是有符号，如果想设置无符号，需要添加unsigned关键字

2. 如果插入的数值超出了整型的范围，会报out of range异常，并且插入临界值

3. 如果不设置长度，会有默认的长度

   长度代表了显示的最大宽度，如果不够，会用0在左边填充，但必须搭配zerofill使用；

   

### 二、小数

1、浮点型

float（M,D）

double（M,D）

2、定点型

dec（M，D）

decimal（M，D）

特点：

1. M：整数部位+小数部位

   D：小数部位

   如果超过范围，则插入临界值

2. M和D都可以忽略

   如果是decimal，则M默认为10，D默认为0

   如果是float和double，则会根据插入的数值的精度来决定精度

3. 定点型的精确度较高，如果要求插入数值的精度较高如货币运算等，则考虑使用



原则：

​		所选择的类型越简单越好，能保存数值的类型越小越好。

### 三、字符型

较短的文本：

char

varchar

其他：

binary和varbinary用于保存较短的二进制

enum用于保存枚举

set用于保存集合



较长的文本：

text

blob（较大的二进制）



特点：



​				写法				   M的意思						特点					     空间的耗费			效率

char		char（M）     最大的字符数，            固定长度的字符         比较耗费                  高

​										可以省略，默认为1

varchar   varchar（M） 最大的字符数			可变长度的字符			比较节省                 低

​											不可以省略



### 四、日期型

分类：

date 只保存日期

time 只保存时间

year 吃保存年



datetime  保存日期+时间

timestamp 保存日期+时间



特点:

​							字节					范围					时区等影响

datetime     		8						1000-9999				不受

timestamp          4						1970-2038				受



### 常见的约束

含义：一种限制，用于限制表中的数据，为了保证表中的数据的准确和可靠性

分类：六大约束

​					NOT NULL :非空，用于保证该字段的值不能为空

​					比如姓名、学号等

​					DEFAULT:默认，用于保证该字段有默认值

​					比如性别

​					PRIMARY KEY：主键，用于保证该字段的值具有唯一性，并且非空

​					比如学号、员工编号等

​					UNIQUE：唯一，用于保证该字段的值具有唯一性，可以为空

​					比如座位号

​					CHECK：检查约束【mysql中不支持】

添加约束的时机：

​				1、创建表时

​				2、修改表时



约束的添加分类：

​				列级约束：

​								六大约束语法上都支持，但外键约束没有效果

​				标记约束：

​								除了非空、默认，其他都支持



主键和唯一的大对比：

​						保证唯一性 		是否允许为空		一个表中可以有多少个		是否允许组合

主键                   √							×										至多有1个					√，但不推荐

唯一					√							√										可以有多个				 √，但不推荐



外键：

​		1、要求在从表设置外键关系

​		2、从表的外键列的类型和主表的关联列的类型要求一致或兼容，名称无要求

​		3、主表的关联列必须是一个key（一般是主键或唯一）

​		4、插入数据时，先插入主表，在插入从表

​		  删除数据时，先删除从表，再删除主表。

```mysql
CREATE TABLE 表名(
					字段名 字段类型 列级约束，
					字段名 字段类型
					表级约束
);
```

一、创建表时添加约束

1、添加列级约束

语法：

直接在字段名和类型后面追加 约束类型即可

只支持：默认、非空、主键、唯一

2、添加表级约束

语法：在各个字段的最下面

【constraint 约束名 】| 约束类型 （字段名）



```mysql
CREATE TABLE  stuinfo(
				id INT,
				stuname VARCHAR(20),
				genger CHAR(1),
				seat INT,
				age INT,
				majorid INT,
				
				CONSTRAINT pk PRIMARY KEY(id),
				CONSTRAINT ug UNIQUE(seat),
				CONSTRAINT ck CHECK(gender = '男' or gender = '女')
				#CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES manjor(id)

);

SHOW INDEX FROM stuinfo;
```



通用的写法：

```mysql
CREATE TABLE  stuinfo(
				id INT PRIMARY KEY,
				stuname VARCHAR(20) NOT NULL,
				genger CHAR(1),
				seat INT UNIQUE,
				age INT DEFAULT 18,
				majorid INT,
				
				#CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES manjor(id)

);
```

二、修改表时添加约束

1、添加列级约束

alter table  表名 modify column  字段名  字段类型   新约束；

2、添加表级约束

alter table 表名 【constraint 约束名】 约束类型（字段名） 【外键的引用】；



1. 添加非空约束

   ```mysql
   ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20)  NOT NULL;
   ```

   

2. 添加默认约束

   ```mysql
   ALTER TABLE stuinfo MODIFY COLUMN age INT DEFAULT 18;
   ```

   

3. 添加主键

   1、列级约束

   ```mysql
   ALTER TABLE stuinfo MODIFY COLUMN id INT PRIMARY KEY;
   ```

   

   2、表级约束

   ```mysql
   ALTER TABLE stuinfo ADD PRIMARY KEY(id);
   ```

   

4. 添加唯一

   1、列级约束

   ```mysql
   ALTER TABLE stuinfo MODIFY COLUMN seat INT UNIQUE;
   ```

   

   2、表级约束

   ```mysql
   ALTER TABLE stuinfo ADD UNIQUE(seat);
   ```

5. 添加外键

   ```mysql
   ALTER TABLE stuinfo ADD CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id);
   ```

三、修改表时删除约束

1、删除非空约束

```mysql
ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20) NULL;
```



2、删除默认约束

```mysql
ALTER TABLE stuinfo MODIFY COLUMN age INT;
```



3、删除主键

```mysql
ALTER TABLE stuinfo DROP PRIMARY KEY;
```



4、删除唯一

```mysql
ALTER TABLE stuinfo DROP INDEX seat;
```



5、删除外键

```mysql
ALTER TABLE stuinfo DROP FOREIGN KEY majorid;
```



习题：

1、向表emp2的id列中添加PRIMARY KEY约束（my_emp_id_pk）

```mysql
ALTER TABLE emp2 MODIFY COLUMN id INT PRIMARY KEY;
ALTER TABLE emp2 ADD CONSTRAINT my_emp_id_pk PRIMARY KEY(id);
```



2、向表dept2的id列中添加PRIMARY KEY约束（my_dept_id_pk）

```mysql
ALTER TABLE dept2 MODIFY COLUMN id INT PRIMARY KEY;
ALTER TABLE dept2 ADD CONSTRAINT my_dept_id_pk PRIMARY KEY(id);
```



3、向表emp2中添加dept_id，并在其中定义FOREIGN KEY，与之相关联的列是dept2表中的id列

```mysql
ALTER TABLE emp2 ADD COLUMN dept_id INT;
ALTER TABLE emp2 ADD CONSTRAINT fk_emp2_dept2 FOREIGN KEY(dept_id) REFERENCES dept2(id);
```

