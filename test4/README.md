# 实验四

## 我的用户与角色

  用户：ydyh
  密码：123


## 为用户分配表空间
```sql
ALTER USER ydyh QUOTA UNLIMITED ON USERS;
ALTER USER ydyh QUOTA UNLIMITED ON USERS02;
ALTER USER ydyh QUOTA UNLIMITED ON USERS03;
ALTER USER ydyh ACCOUNT UNLOCK;
```
结果
![blockchain](https://github.com/15283565516/Oracle/blob/master/test4/4-1.PNG)



## 为用户分配权限
```sql
GRANT "CONNECT" TO ydyh WITH ADMIN OPTION;
GRANT "RESOURCE" TO ydyh WITH ADMIN OPTION;
ALTER USER ydyh DEFAULT ROLE "CONNECT","RESOURCE";
```
结果
![blockchain](https://github.com/15283565516/Oracle/blob/master/test4/4-2.PNG)


## 系统权限分配
```sql
GRANT CREATE VIEW TO ydyh WITH ADMIN OPTION;
```
结果
![blockchain](https://github.com/15283565516/Oracle/blob/master/test4/4-3.PNG)




## 添加实验所需表和相应触发器、序列、视图

### 创建3个触发器
```sql
CREATE OR REPLACE EDITIONABLE TRIGGER "ORDERS_TRIG_ROW_LEVEL"

BEFORE INSERT OR UPDATE OF DISCOUNT ON "ORDERS"

FOR EACH ROW --行级触发器

declare

m number(8,2);

BEGIN

if inserting then

:new.TRADE_RECEIVABLE := - :new.discount;

else

select sum(PRODUCT_NUM*PRODUCT_PRICE) into m from ORDER_DETAILS where ORDER_ID=:old.ORDER_ID;

if m is null then

m:=0;

end if;

:new.TRADE_RECEIVABLE := m - :new.discount;

end if;

END;

ALTER TRIGGER "ORDERS_TRIG_ROW_LEVEL" DISABLE;





-- 触发器 ORDER_DETAILS_ROW_TRIG



CREATE OR REPLACE EDITIONABLE TRIGGER "ORDER_DETAILS_ROW_TRIG"

AFTER DELETE OR INSERT OR UPDATE ON ORDER_DETAILS

FOR EACH ROW

BEGIN

--DBMS_OUTPUT.PUT_LINE(:NEW.ORDER_ID);

IF :NEW.ORDER_ID IS NOT NULL THEN

MERGE INTO ORDER_ID_TEMP A

USING (SELECT 1 FROM DUAL) B

ON (A.ORDER_ID=:NEW.ORDER_ID)

WHEN NOT MATCHED THEN

INSERT (ORDER_ID) VALUES(:NEW.ORDER_ID);

END IF;

IF :OLD.ORDER_ID IS NOT NULL THEN

MERGE INTO ORDER_ID_TEMP A

USING (SELECT 1 FROM DUAL) B

ON (A.ORDER_ID=:OLD.ORDER_ID)

WHEN NOT MATCHED THEN

INSERT (ORDER_ID) VALUES(:OLD.ORDER_ID);

END IF;

END;

ALTER TRIGGER "ORDER_DETAILS_ROW_TRIG" DISABLE;

```
结果

![blockchain](https://github.com/15283565516/Oracle/blob/master/test4/4-8.PNG)



### 创建两个序列

```sql
CREATE SEQUENCE "SEQ_ORDER_ID" MINVALUE 1 MAXVALUE 9999999999 INCREMENT BY 1 START WITH 1 CACHE 2000 ORDER NOCYCLE NOPARTITION ;
```

```sql
CREATE SEQUENCE "SEQ_ORDER_DETAILS_ID" MINVALUE 1 MAXVALUE 9999999999 INCREMENT BY 1 START WITH 1 CACHE 2000 ORDER NOCYCLE NOPARTITION ;
```
结果
![blockchain](https://github.com/15283565516/Oracle/blob/master/test4/4-4.PNG)


### 创建视图
``sql
CREATE OR REPLACE FORCE EDITIONABLE VIEW "VIEW_ORDER_DETAILS" ("ID", "ORDER_ID", "CUSTOMER_NAME", "CUSTOMER_TEL", "ORDER_DATE", "PRODUCT_TYPE", "PRODUCT_NAME", "PRODUCT_NUM", "PRODUCT_PRICE") AS

SELECT

d.ID,

o.ORDER_ID,

o.CUSTOMER_NAME,o.CUSTOMER_TEL,o.ORDER_DATE,

p.PRODUCT_TYPE,

d.PRODUCT_NAME,

d.PRODUCT_NUM,

d.PRODUCT_PRICE

FROM ORDERS o,ORDER_DETAILS d,PRODUCTS p where d.ORDER_ID=o.ORDER_ID and d.PRODUCT_NAME=p.PRODUCT_NAME;
```
结果
![blockchain](https://github.com/15283565516/Oracle/blob/master/test4/4-5.PNG)



### 插入初始化数据
```sql
INSERT INTO ydyh.DEPARTMENTS(DEPARTMENT_ID,DEPARTMENT_NAME) values (1,'总经办');
INSERT INTO ydyh.EMPLOYEES(EMPLOYEE_ID,NAME,EMAIL,PHONE_NUMBER,HIRE_DATE,SALARY,MANAGER_ID,DEPARTMENT_ID)
  VALUES (1,'李董事长',NULL,NULL,to_date('2010-1-1','yyyy-mm-dd'),50000,NULL,1);

INSERT INTO ydyh.DEPARTMENTS(DEPARTMENT_ID,DEPARTMENT_NAME) values (11,'销售部1');
INSERT INTO ydyh.EMPLOYEES(EMPLOYEE_ID,NAME,EMAIL,PHONE_NUMBER,HIRE_DATE,SALARY,MANAGER_ID,DEPARTMENT_ID)
  VALUES (11,'张总',NULL,NULL,to_date('2010-1-1','yyyy-mm-dd'),50000,1,1);
INSERT INTO ydyh.EMPLOYEES(EMPLOYEE_ID,NAME,EMAIL,PHONE_NUMBER,HIRE_DATE,SALARY,MANAGER_ID,DEPARTMENT_ID)
  VALUES (111,'吴经理',NULL,NULL,to_date('2010-1-1','yyyy-mm-dd'),50000,11,11);
INSERT INTO ydyh.EMPLOYEES(EMPLOYEE_ID,NAME,EMAIL,PHONE_NUMBER,HIRE_DATE,SALARY,MANAGER_ID,DEPARTMENT_ID)
  VALUES (112,'白经理',NULL,NULL,to_date('2010-1-1','yyyy-mm-dd'),50000,11,11);

INSERT INTO ydyh.DEPARTMENTS(DEPARTMENT_ID,DEPARTMENT_NAME) values (12,'销售部2');
INSERT INTO ydyh.EMPLOYEES(EMPLOYEE_ID,NAME,EMAIL,PHONE_NUMBER,HIRE_DATE,SALARY,MANAGER_ID,DEPARTMENT_ID)
  VALUES (12,'王总',NULL,NULL,to_date('2010-1-1','yyyy-mm-dd'),50000,1,1);
INSERT INTO ydyh.EMPLOYEES(EMPLOYEE_ID,NAME,EMAIL,PHONE_NUMBER,HIRE_DATE,SALARY,MANAGER_ID,DEPARTMENT_ID)
  VALUES (121,'赵经理',NULL,NULL,to_date('2010-1-1','yyyy-mm-dd'),50000,12,12);
INSERT INTO ydyh.EMPLOYEES(EMPLOYEE_ID,NAME,EMAIL,PHONE_NUMBER,HIRE_DATE,SALARY,MANAGER_ID,DEPARTMENT_ID)
  VALUES (122,'刘经理',NULL,NULL,to_date('2010-1-1','yyyy-mm-dd'),50000,12,12);


insert into ydyh.products (product_name,product_type) values ('computer1','电脑');
insert into ydyh.products (product_name,product_type) values ('computer2','电脑');
insert into ydyh.products (product_name,product_type) values ('computer3','电脑');

insert into ydyh.products (product_name,product_type) values ('phone1','手机');
insert into ydyh.products (product_name,product_type) values ('phone2','手机');
insert into ydyh.products (product_name,product_type) values ('phone3','手机');

insert into ydyh.products (product_name,product_type) values ('paper1','耗材');
insert into ydyh.products (product_name,product_type) values ('paper2','耗材');
insert into ydyh.products (product_name,product_type) values ('paper3','耗材');

```

结果
![blockchain](https://github.com/15283565516/Oracle/blob/master/test4/4-6.PNG)


## 插入订单数据 ,插入10000条数据
```sql
declare
  dt date;
  m number(8,2);
  V_EMPLOYEE_ID NUMBER(6);
  v_order_id number(10);
  v_name varchar2(100);
  v_tel varchar2(100);
  v number(10,2);

begin
  for i in 1..10000
  loop
    if i mod 2 =0 then
      dt:=to_date('2015-3-2','yyyy-mm-dd')+(i mod 60);
    else
      dt:=to_date('2016-3-2','yyyy-mm-dd')+(i mod 60);
    end if;
    V_EMPLOYEE_ID:=CASE I MOD 6 WHEN 0 THEN 11 WHEN 1 THEN 111 WHEN 2 THEN 112
                                WHEN 3 THEN 12 WHEN 4 THEN 121 ELSE 122 END;
    --插入订单
    v_order_id:=SEQ_ORDER_ID.nextval; --应该将SEQ_ORDER_ID.nextval保存到变量中。
    v_name := 'aa'|| 'aa';
    v_name := 'zhang' || i;
    v_tel := '139888883' || i;
    insert  into ydyh.ORDERS (ORDER_ID,CUSTOMER_NAME,CUSTOMER_TEL,ORDER_DATE,EMPLOYEE_ID,DISCOUNT)
      values (v_order_id,v_name,v_tel,dt,V_EMPLOYEE_ID,dbms_random.value(100,0));
    --插入订单y一个订单包括3个产品
    v:=dbms_random.value(10000,4000);
    v_name:='computer'|| (i mod 3 + 1);
    insert  into ydyh.ORDER_DETAILS(ID,ORDER_ID,PRODUCT_NAME,PRODUCT_NUM,PRODUCT_PRICE)
      values (SEQ_ORDER_DETAILS_ID.NEXTVAL,v_order_id,v_name,2,v);
    v:=dbms_random.value(1000,50);
    v_name:='paper'|| (i mod 3 + 1);
    insert  into ydyh.ORDER_DETAILS(ID,ORDER_ID,PRODUCT_NAME,PRODUCT_NUM,PRODUCT_PRICE)
      values (SEQ_ORDER_DETAILS_ID.NEXTVAL,v_order_id,v_name,3,v);
    v:=dbms_random.value(9000,2000);
    v_name:='phone'|| (i mod 3 + 1);
    insert  into ydyh.ORDER_DETAILS(ID,ORDER_ID,PRODUCT_NAME,PRODUCT_NUM,PRODUCT_PRICE)
      values (SEQ_ORDER_DETAILS_ID.NEXTVAL,v_order_id,v_name,1,v);
    --在触发器关闭的情况下，需要手工计算每个订单的应收金额：
    select sum(PRODUCT_NUM*PRODUCT_PRICE) into m from ydyh.ORDER_DETAILS where ORDER_ID=v_order_id;
    if m is null then
     m:=0;
    end if;
    UPDATE ORDERS SET TRADE_RECEIVABLE = m - discount WHERE ORDER_ID=v_order_id;
    IF I MOD 1000 =0 THEN
      commit; --每次提交会加快插入数据的速度
    END IF;
  end loop;
end;
```


### 查询数据

#### 查询单条数据
```sql
--查询数据 id值从10012到20021
select * from ydyh.ORDERS where  order_id=100;
select * from ydyh.ORDER_DETAILS where  order_id=100;
select * from ydyh.VIEW_ORDER_DETAILS where order_id=100;
```

#### 递归查询员工及其下级员工
```sql
WITH A (EMPLOYEE_ID,NAME,EMAIL,PHONE_NUMBER,HIRE_DATE,SALARY,MANAGER_ID,DEPARTMENT_ID) AS
  (SELECT EMPLOYEE_ID,NAME,EMAIL,PHONE_NUMBER,HIRE_DATE,SALARY,MANAGER_ID,DEPARTMENT_ID
    FROM ydyh.employees WHERE employee_ID = 11
    UNION ALL
  SELECT B.EMPLOYEE_ID,B.NAME,B.EMAIL,B.PHONE_NUMBER,B.HIRE_DATE,B.SALARY,B.MANAGER_ID,B.DEPARTMENT_ID
    FROM A, ydyh.employees B WHERE A.EMPLOYEE_ID = B.MANAGER_ID)
SELECT * FROM A;
--或
SELECT * FROM employees START WITH EMPLOYEE_ID = 11 CONNECT BY PRIOR EMPLOYEE_ID = MANAGER_ID;
```
结果
![blockchain](https://github.com/15283565516/Oracle/blob/master/test4/4-7.PNG)






