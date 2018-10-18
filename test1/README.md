# Oracle 实验一：分析SQL执行计划，执行SQL语句的优化指导
## 教材中的查询语句
### 查询1：
下面是查询语句1的结果
![blockchain](https://github.com/15283565516/Oracle/blob/master/test1/t1j.jpg)


下面是查询语句1的解释计划
![blockchain](https://github.com/15283565516/Oracle/blob/master/test1/t1.png)

由图可知cost是10，花费时间是0.091秒。



##查询2：
下面是查询语句2的结果
![blockchain](https://github.com/15283565516/Oracle/blob/master/test1/t2j.jpg)


下面是查询语句2的解释计划
![blockchain](https://github.com/15283565516/Oracle/blob/master/test1/t2f.png)

由图可知cost是18，花费时间是0.035秒。


下面是查询语句2的优化指导
![blockchain](https://github.com/15283565516/Oracle/blob/master/test1/t2y.png)

由图可以看出，该语句没有优化指导。



下面是我自己写的查询语句:
(```)
SELECT d.department_name，count(e.job_id)as "部门总人数"，
avg(e.salary)as "平均工资"
FROM hr.departments d，hr.employees e
WHERE d.department_id = e.department_id
partition department_name
HAVING d.department_name in ('IT'，'Sales');
(```)

下面是我的查询结果：
![blockchain](https://github.com/15283565516/Oracle/blob/master/test1/t3j.jpg)

我采用的是partition 分区查询，查询时间为0.05秒，时间更短，但不知道这种用法是否可行。



