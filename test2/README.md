
第1步：以system登录到pdborcl，创建角色yd和用户ydyh，并授权和分配空间：

$ sqlplus system/123@pdborcl
SQL> CREATE ROLE yd;
Role created.
SQL> GRANT connect,resource,CREATE VIEW TO yd;
Grant succeeded.
SQL> CREATE USER ydyh IDENTIFIED BY 123 DEFAULT TABLESPACE users TEMPORARY TABLESPACE temp;
User created.
SQL> ALTER USER ydyh QUOTA 50M ON users;
User altered.
SQL> GRANT yd TO ydyh;
Grant succeeded.
SQL> exit




 第2步：新用户ydyh连接到pdborcl，创建表mytable和视图myview，插入数据，最后将myview的SELECT对象权限授予hr用户。

$ sqlplus ydyh/123@pdborcl
SQL> show user;
USER is "YDYH"
SQL> CREATE TABLE mytable (id number,name varchar(50));
Table created.
SQL> INSERT INTO mytable(id,name)VALUES(1,'wo');
1 row created.
SQL> INSERT INTO mytable(id,name)VALUES (2,'li');
1 row created.
SQL> CREATE VIEW myview AS SELECT name FROM mytable;
View created.
SQL> SELECT * FROM myview;
NAME
--------------------------------------------------
wo
li
SQL> GRANT SELECT ON myview TO hr;
Grant succeeded.
SQL>exit



 第3步：用户hr连接到pdborcl，查询yd授予它的视图myview

$ sqlplus hr/123@pdborcl
SQL> SELECT * FROM yd.myview;
NAME
--------------------------------------------------
wo
li
SQL> exit


以下样例查看表空间的数据库文件，以及每个文件的磁盘占用情况。

$ sqlplus system/123@pdborcl

SQL>SELECT tablespace_name,FILE_NAME,BYTES/1024/1024 MB,MAXBYTES/1024/1024 MAX_MB,autoextensible FROM dba_data_files  WHERE  tablespace_name='USERS';

SQL>SELECT a.tablespace_name "表空间名",Total/1024/1024 "大小MB",
 free/1024/1024 "剩余MB",( total - free )/1024/1024 "使用MB",
 Round(( total - free )/ total,4)* 100 "使用率%"
 from (SELECT tablespace_name,Sum(bytes)free
        FROM   dba_free_space group  BY tablespace_name)a,
       (SELECT tablespace_name,Sum(bytes)total FROM dba_data_files
        group  BY tablespace_name)b
 where  a.tablespace_name = b.tablespace_name;
