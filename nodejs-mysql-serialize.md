# Nodejs Mysql 과 Serialize

## Mysql 설정 

```text
mysql> CREATE DATABASE  MY_DB;
Query OK, 1 row affected (0.01 sec)

mysql> use my_db;
Database changed

mysql> create table users(
    -> id varchar(45) not null,
    -> password varchar(45) not null,
    -> primary key (id));
Query OK, 0 rows affected (0.01 sec)

mysql> Insert into Users(id, password) values ('song','1234');
Query OK, 1 row affected (0.01 sec)

mysql> select * from users;
+------+----------+
| id   | password |
+------+----------+
| song | 1234     |
+------+----------+
1 row in set (0.00 sec)
```



