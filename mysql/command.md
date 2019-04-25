### 创建数据库
```
CREATE DATABASE db_name DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
```

### 增删改查
```
增加语句
mysql> insert into table(id,name) values(3,'cc'),(4,'ff');

删除语句
mysql> delete from table where id =3;

修改语句
mysql> update table set name = 'ccc' where id = 3;

查询语句
mysql> select * from table where id > 23 order by date desc limit 20,10\G
```

### 删除表
```
drop table 表名 ==== 删除表

truncate table 表名 ==== 清空表数据 保留表结构

delete from 表名称 where 列名称 = 值 ==== 删除一行
```

### 查询表结构
```
show create table 表名\G
```

### 修改表的字段类型
```
mysql> alter table 表名 modify column 字段名 类型;
```

### 授予用户访问权限
```
授予用户通过外网IP对于该数据库的全部权限
mysql> grant all privileges on `xiaoqicheng`.* to 'lnmp'@'%' identified by 'lnmp';

授予用户在本地服务器对该数据库的全部权限
mysql> grant all privileges on `xiaoqicheng`.* to 'lnmp'@'localhost' identified by 'lnmp';

刷新权限
mysql> flush privileges;
```