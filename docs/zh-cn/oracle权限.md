# Oracle查看权限

之前之一对oracle内部的角色和权限管理很乱，空余之时总结一下：

oracle的权限分为两种：

    系统权限：对oracle的操作权限，例如连接、授权等
    对象权限：对oracle的dba_object的权限，例如对表、触发器、存储过程等的CRUD的权限

1.查看所有用户涉及到的表：

```sql
  select * from dba_users;
  select * from all_users;
  select * from user_users;
```

2.查看用户或角色系统权限(直接赋值给用户或角色的系统权限)：

```sql
 select * from dba_sys_privs;
  select * from user_sys_privs;
```

3.查看角色(只能查看登陆用户拥有的角色)所包含的权限

```sql
sql>select * from role_sys_privs;
```

例如查询DBA、CONNECT、RESOURCE角色对应的权限信息

```sql
select * from role_sys_privs where role IN('DBA','CONNECT','RESOURCE');
```

4.查看用户对象权限：

```sql
  select * from dba_tab_privs;
  select * from all_tab_privs;
  select * from user_tab_privs;
```


5.查看所有角色：

```sql
select * from dba_roles;
```

6.查看用户或角色所拥有的角色：

```sql
select * from dba_role_privs;--查看整个oracle系统的权限角色列表
select * from user_role_privs;--查看当前登录用户自己的权限、角色列表
```

假如当前使用scott用户登录，并且scott有DBA权限，如下两条sql查询的结果一致

```sql
select *  from user_role_privs;
select *  from dba_role_privs where grantee='SCOTT';
```

7.查看哪些用户有sysdba或sysoper系统权限(查询时需要相应权限)

```sql
select * from V$PWFILE_USERS
```


8.例子：比如我要查看用户 SCOTT的拥有的权限：

```sql
SQL> select * from dba_sys_privs where grantee='SCOTT';

GRANTEE                        PRIVILEGE                                ADMIN_OPTION

------------------------------ ---------------------------------------- ------------

SCOTT                           CREATE TRIGGER                           NO
SCOTT                           UNLIMITED TABLESPACE                     NO
```

比如我要查看用户 SCOTT的拥有的角色：

```sql
SQL> select * from dba_role_privs where grantee='SCOTT';

GRANTEE   GRANTED_ROLE   ADMIN_OPTION  DEFAULT_ROLE

------------------------------ ------------------------------ ------------ ------------
SCOTT                           DBA                         NO              YES
查看一个用户所有的权限及角色
select privilege from dba_sys_privs where grantee='SCOTT'
union
select privilege from dba_sys_privs where grantee in 
(select granted_role from dba_role_privs where grantee='SCOTT' );
```

