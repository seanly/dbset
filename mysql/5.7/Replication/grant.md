# MySQL创建用户与授权

## 创建用户

命令:

```sql
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
```

说明：

- username：你将创建的用户名
- host：指定该用户在哪个主机上可以登陆，如果是本地用户可用localhost，如果想让该用户可以从任意远程主机登陆，可以使用通配符%
- password：该用户的登陆密码，密码可以为空，如果为空则该用户可以不需要密码登陆服务器
例子：

```sql
CREATE USER 'dog'@'localhost' IDENTIFIED BY '123456';
CREATE USER 'pig'@'192.168.1.101_' IDENDIFIED BY '123456';
CREATE USER 'pig'@'%' IDENTIFIED BY '123456';
CREATE USER 'pig'@'%' IDENTIFIED BY '';
CREATE USER 'pig'@'%';

```
## 授权:

命令:

```sql
GRANT privileges ON databasename.tablename TO 'username'@'host'
SHOW GRANTS FOR <user@ip>;
```

说明:

- privileges：用户的操作权限，如SELECT，INSERT，UPDATE等，如果要授予所的权限则使用ALL
- databasename：数据库名
- tablename：表名，如果要授予该用户对所有数据库和表的相应操作权限则可用*表示，如*.*

例子:

```sql 
GRANT SELECT, INSERT ON test.user TO 'pig'@'%';
GRANT ALL ON *.* TO 'pig'@'%';
GRANT ALL ON maindataplus.* TO 'pig'@'%';
```

注意:
用以上命令授权的用户不能给其它用户授权，如果想让该用户可以授权，用以下命令:

```sql
GRANT privileges ON databasename.tablename TO 'username'@'host' WITH GRANT OPTION;
```

## 设置与更改用户密码

命令:
```sql
SET PASSWORD FOR 'username'@'host' = PASSWORD('newpassword');
```
如果是当前登陆用户用:

```sql
SET PASSWORD = PASSWORD("newpassword");
```
例子:

```sql
SET PASSWORD FOR 'pig'@'%' = PASSWORD("123456");
```

## 撤销用户权限
命令:

```sql
REVOKE privilege ON databasename.tablename FROM 'username'@'host';
```

说明:
privilege, databasename, tablename：同授权部分

例子:

```sql
REVOKE SELECT ON *.* FROM 'pig'@'%';
```

注意:

假如你在给用户'pig'@'%'授权的时候是这样的（或类似的）：GRANT SELECT ON test.user TO 'pig'@'%'，则在使用REVOKE SELECT ON *.* FROM 'pig'@'%';命令并不能撤销该用户对test数据库中user表的SELECT 操作。相反，如果授权使用的是GRANT SELECT ON *.* TO 'pig'@'%';则REVOKE SELECT ON test.user FROM 'pig'@'%';命令也不能撤销该用户对test数据库中user表的Select权限。

具体信息可以用命令SHOW GRANTS FOR 'pig'@'%'; 查看。

## 删除用户

命令:
```sql
DROP USER 'username'@'host';
```


## 案例

SQL程序语言有四种类型，对数据库的基本操作都属于这四类，它们分别为；数据定义语言(DDL)、数据查询语言（DQL）、数据操纵语言（DML）、数据控制语言（DCL）

权限分类参考：
1. https://help.aliyun.com/zh/rds/apsaradb-rds-for-mysql/account-permissions
2. http://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html


all privileges

```sql
select,
insert,
update,
delete,
create,
drop,
references,
index,
alter,
create temporary tables,
lock tables,
execute,
create view,
show view,
create routine,
alter routine,
event,
trigger
```

```sql

# 读写账号
GRANT ALL PRIVILEGES ON <database>.* TO <user_rw>@'%';
GRANT PROCESS,REPLICATION CLIENT,REPLICATION SLAVE ON *.* TO <user_rw>@'%';
REVOKE DROP ON <database>.* FROM <user_rw>@'%';

# 只读账号
GRANT LOCK TABLES,SELECT,SHOW VIEW ON <database>.* TO <user_ro>@'%';
GRANT PROCESS,REPLICATION CLIENT,REPLICATION SLAVE ON *.* TO <user_ro>@'%';
```