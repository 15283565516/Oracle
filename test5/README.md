# 实验五
### 用户：ydyh  密码：123
## 1、创建一个包(Package)，包名是MyPack。
```
    create or replace PACKAGE MyPack IS
      FUNCTION Get_SaleAmount(V_DEPARTMENT_ID NUMBER) RETURN NUMBER;
      PROCEDURE Get_Employees(V_EMPLOYEE_ID NUMBER);
    END MyPack;
    /
```
## 结果：
![blockchain](https://github.com/15283565516/Oracle/blob/master/test5/1.PNG)
## 2、在MyPack中创建一个函数SaleAmount ，查询部门表，统计每个部门的销售总金额，每个部门的销售额是由该部门的员工(ORDERS.EMPLOYEE_ID)完成的销售额之和。函数SaleAmount要求输入的参数是部门号，输出部门的销售金额。
```
    create or replace PACKAGE BODY MyPack IS
      FUNCTION Get_SaleAmount(V_DEPARTMENT_ID NUMBER) RETURN NUMBER
      AS
        N NUMBER(20,2);
        BEGIN
          SELECT SUM(O.TRADE_RECEIVABLE) into N  FROM ORDERS O,EMPLOYEES E
          WHERE O.EMPLOYEE_ID=E.EMPLOYEE_ID AND E.DEPARTMENT_ID =V_DEPARTMENT_ID;
          RETURN N;
    END;
```
## 3、在MyPack中创建一个过程，在过程中使用游标，递归查询某个员工及其所有下属，子下属员工。过程的输入参数是员工号，输出员工的ID,姓名，销售总金额。信息用dbms_output包中的put或者put_line函数。输出的员工信息用左添加空格的多少表示员工的层次（LEVEL）。
```
    PROCEDURE GET_EMPLOYEES(V_EMPLOYEE_ID NUMBER)
      AS
        LEFTSPACE VARCHAR(2000);
        begin
          --通过LEVEL判断递归的级别
          LEFTSPACE:=' ';
          --使用游标
          for v in
          (SELECT LEVEL,EMPLOYEE_ID,NAME,MANAGER_ID FROM employees
          START WITH EMPLOYEE_ID = V_EMPLOYEE_ID
          CONNECT BY PRIOR EMPLOYEE_ID = MANAGER_ID)
          LOOP
            DBMS_OUTPUT.PUT_LINE(LPAD(LEFTSPACE,(V.LEVEL-1)*4,' ')||
                                 V.EMPLOYEE_ID||' '||v.NAME);
          END LOOP;
        END;
    END MyPack;
    /
```
## 结果：
![blockchain](https://github.com/15283565516/Oracle/blob/master/test5/2.PNG)
## 4、函数Get_SaleAmount()测试：
```
   select count(*) from orders;
```
## 结果：
![blockchain](https://github.com/15283565516/Oracle/blob/master/test5/3.PNG)
```
  select MyPack.Get_SaleAmount(11) AS 部门11应收金额,MyPack.Get_SaleAmount(12) AS 部门12应收金额 from dual;
```
## 结果：
![blockchain](https://github.com/15283565516/Oracle/blob/master/test5/4.PNG)
## 5、过程Get_Employees()测试：
```
set serveroutput on
DECLARE
  V_EMPLOYEE_ID NUMBER;    
BEGIN
  V_EMPLOYEE_ID := 1;
  MYPACK.Get_Employees (  V_EMPLOYEE_ID => V_EMPLOYEE_ID) ;  
  V_EMPLOYEE_ID := 11;
  MYPACK.Get_Employees (  V_EMPLOYEE_ID => V_EMPLOYEE_ID) ;    
END;
/
```
## 结果：
![blockchain](https://github.com/15283565516/Oracle/blob/master/test5/5.PNG)

