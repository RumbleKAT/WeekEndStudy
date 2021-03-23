# Nodejs Mysql 과 Serialize

## 들어가면서

MySQL은 세계에서 가장 많이 쓰이는 오픈 소스의 관계형 데이터베이스 관리 시스템이다. MariaDB는 오픈 소스의 관계형 데이터베이스 관리 시스템이다. MySQL과 동일한 소스 코드를 기반으로 하며, GPL v2 라이선스를 따른다. 오라클 소유의 현재 불확실한 MySQL의 라이선스 상태에 반발하여 만들어졌으며, 배포자는 몬티 프로그램 AB와 저작권을 공유해야 한다. 마리아DB는 MySQL과 소스코드를 같이 하므로 사용방법과 구조가 MySQL과 동일하다. **근본적으론 오라클에서 자유롭다.**

## MySQL Setting

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

## Node JS 패키지 초기화

`package.json`은 프로젝트 정보와 의존성\(dependencies\)을 관리하는 문서  
이미 작성된 `package.json` 문서는 어느 곳에서도 동일한 개발 환경을 구축할 수 있게 해준다.  
JSON 포맷으로 작성해야 하며, 다음과 같은 옵션들이 추가될 수 있습니다.

```text
$ npm init
```

* package name
* version
* description
* entry point
* test command
* git repository
* keywords
* author
* license

```text
$ npm init -y
```

```text
➜  mysql npm init

Press ^C at any time to quit.
package name: (mysql) nodejs-mysql
version: (1.0.0) 
description: mysql-nodejs exercise
entry point: (index.js) 
test command: node index.js
git repository: 
keywords: 
author: rumblekat
license: (ISC) 
About to write to /Users/songmyeongjin/Desktop/mysql/package.json:

{
  "name": "nodejs-mysql",
  "version": "1.0.0",
  "description": "mysql-nodejs exercise",
  "main": "index.js",
  "scripts": {
    "test": "node index.js"
  },
  "author": "rumblekat",
  "license": "ISC"
}


Is this OK? (yes) 

```

![](.gitbook/assets/2021-03-23-10.19.13%20%281%29.png)









