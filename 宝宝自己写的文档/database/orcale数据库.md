# orcale数据库

[TOC]

### 一、数据库

​	**基本概念**：是用户存放数据、访问数据、操作数据的存储仓库，用户的各种数据被有组织的存放在数据库中。 可以随时被有权限的用户增删查改。

​	是由普通的文件系统发展而来的。

​	数据库具有较高的数据独立性，即不依赖特定的数据库应用程序；

​	数据库系统的数据冗（rong）余小，可以节省数据的存储空间；

​	容易世界多个用户的数据共享。	

​	**数据库管理系统(DBMS)** ：英文全称：Database Managerment System

​	DBMS是所有数据的知识库，并对数据的存储、安全、一致性、并发操作、恢复和访问负责。

​	**常见的关系型数据库**：Oracle 、DB2（千万级）、Sybase、MS SQL Server、MySQL（开源，百万级）、PostgreSQL（开源）。 

### 二、SQL：结构化查询语言（Structured Query Language）

​	**sql语言分类**

1. **DDL**：Data Definition Language 数据定义语言，定义对数据库对象（库、表、列、索引）的操作。

   如：create 、drop、alter、rename、truncate等。

2. **DML**：Data Manipulation Language 数据操作语言，定义对数据库记录的操作。

   如：insert、delete、update。

3. **DQL**：Data Query Language 数据查询语言，定义对数据库记录的查询操作。

   如：select。

4. **DCL**：Data Control Language 数据控制语言，定义对数据库、表、字段、用户的访问权限控制。

   如：grant、revoke等。

5. **Transaction Control**：事物控制

   如：commit、rollback、savepoint等。

### 三、Oracle数据库的特点

1. 支持大数据库、多用的高性能的事物处理
2. Oracle遵守数据存取语言、操作系统、用户接口和网络通信协议的工业标准
3. 安全性高
4. 支持分布式管理
5. 兼容性高
6. 全球化、跨平台

### 四、Oracle的服务

1. **OracleServiceORCL**：数据库的主服务，必须启动才能使用Oracle。
2. **OracleOraDB10g_home1TNSListener**：数据库的监听器服务，如果有客户端需要连接Oracle，必须启动此服务。

### 五、Orcale用户

1. SYS是系统最高权限的管理员。

2. scott用户是Oracle数据库的一个测试账号。密码默认为taiger，第一次登陆此账号需要解锁，使用 `Alter user scott account unlock;`命令解锁，同理去掉`un` 即可上锁。

3. 更改与删除用户

   ```java
   alter user username identified by password;
   //用于修改密码
   drop user username cascade;
   //删除用户，使用cascade子句删除所有关联的对象。

   //用户的数据字典
   DBA_USERS
   USER_USERS
   ```

4. 更改权限

   ```java
   //授予系统权限
   grant connect to username
   //CONNECT 角色允许用户连接至数据库，并创建数据库对象
   GRANT RESOURCE TO username：
   //RESOURCE 角色允许用户使用数据库中的存储空间
   grant all privileges to username;
   //把所有权限授予给用户

   //系统权限的数据字典
   DBA_SYS_PRIVS
   USER_SYS_PRIVS
   SESSION_PRIVS

   revoke connect to username;
   //收回用户的connect权限。
   //只有以grant命令授予的权限可以被REVOKE。

   //对象的权限数据字典：
   DBA_TAB_PRIVS
   DBA_COL_PRIVS
   USER_TAB_PRIVS
   DBA_COL_PRIVS

   //授予对象权限
   //授权必须在授予者模式或者被给予有with grant option
   //使用grant命令
   GRANT SELECT ON scott.emp TO username WITH GRANT OPTION;
   //将emp表查询授权给用户
   REVOKE SELECT ON scott.emp FROM username;
   //将emp表查询权限收回
   ```


### 六、表空间与表管理

1. **表空间概念**：一个或多个数据文件的逻辑集合。

2. **表空间分类**：
   1. 永久表空间：存储数据库中需要永久花存储的对象。
   2. 临时表空间：存储数据库的中间执行过程。
   3. UNDO表空间：保存修改前的副本，为了对数据进行回滚、恢复、撤销的操作。

3. 表空间的sql

   ```java
   //创建表空间
   create tablespace space_name
   datafile 'C:\file.dbf'
   size 20m;
   //删除表空间
   drop tablespace space_name including contents and datafiles;
   //并且删除表空间创建时新建的dbf文件
   ```

   ​

4. 数据类型

   | 描述               | 数据类型                 |
   | ---------------- | -------------------- |
   | VARCHAR2(size)   | 可变长度字符数据 1..4000     |
   | CHAR(size)       | 固定长度字符数据 1..2000     |
   | NUMBER(p,s)      | 可变长度数字数据（几位数，小数点后几位） |
   | DATE             | 日期和时间数值              |
   | LONG             | 可变长度字符数据，最大到2GB      |
   | CLOB             | 字符数据，最大到4GB          |
   | RAW and LONG RAW | 原始二进制数               |
   | BLOB             | 二进制数，最大到4GB          |
   | BFILE            | 存储到外部文件中的二进制数；最大到4GB |
   | ROWID            | 表示行在表中的唯一地址          |

   **Char、varchar和varchar2的区别**

   char的长度是固定的，而varchar2的长度可以变化的，比如，存储字符串“abc”对于char（20）来说将占用20个字节，有17个字节是空的。而varchar2将占用3字节，20是最大值。

   char的效率比varchar2的效率要高。

   varchar与varchar2其实是一样的。只不过varchar可以存储空字符串，而varchar2改为存储null值。varchar2是orcale自己开发的。

5. 操纵表的sql

   ```java
   //创建表
   create table table_name
   (
   	column_name datatype(number(10,2) or varchar2(20))
   )
   tablespace space_name;//给表赋一个表空间
   //修改表
   //增加一列
   alter table table_name add (column_name varchar2(20));
   //修改一列
   alter table table_name modify (column_name varchar2(30));
   //修改列名
   alter table table_name rename column column_name to new_column_name;
   //删除列名
   alter table table_name drop column column_name;
   //修改表名
   rename table_name to new_table_name;
   //为表加注释
   comment on table table_name is "test";
   //为列加注释
   comment on column table_name.column_name is "test";
   //删除一张表
   drop table table_name;
   //恢复被删除的表
   flashback table table_name to before drop;
   ```

   ​

