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

## 테스트 코드 작성 후 npm test 실행

```text
➜  mysql npm test

> nodejs-mysql@1.0.0 test
> node index.js

hello world

```

## NodeJS와 MySQL 연동

createConnection 메소드의 인자로 전달되는 객체에 자신의 데이터베이스 정보\(유저명과 패스워드 등\)을 입력해야한다. 

```text
> node index.js

internal/modules/cjs/loader.js:968
  throw err;
  ^

Error: Cannot find module 'mysql2' ---> 모듈을 찾을 수 없을 경우, npm install <Module> --save 로 해당 의존성을 불러온다.
Require stack:
- /Users/songmyeongjin/Desktop/mysql/index.js
    at Function.Module._resolveFilename (internal/modules/cjs/loader.js:965:15)
    at Function.Module._load (internal/modules/cjs/loader.js:841:27)
    at Module.require (internal/modules/cjs/loader.js:1025:19)
    at require (internal/modules/cjs/helpers.js:72:18)
    at Object.<anonymous> (/Users/songmyeongjin/Desktop/mysql/index.js:1:15)
    at Module._compile (internal/modules/cjs/loader.js:1137:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1157:10)
    at Module.load (internal/modules/cjs/loader.js:985:32)
    at Function.Module._load (internal/modules/cjs/loader.js:878:14)
    at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:71:12) {
  code: 'MODULE_NOT_FOUND',
  requireStack: [ '/Users/songmyeongjin/Desktop/mysql/index.js' ]
}

```

```text
➜  mysql npm install mysql2 --save
```

```javascript
const mysql = require("mysql2");
const connection = mysql.createConnection({
    host : 'localhost',
    user : 'root',
    port : 3306,
    password : 'Init123$',
    database : 'my_db'
});

connection.connect();

connection.query('select * from users',(err,rows,fields)=>{
    if(err) throw err;
    console.log('User info is ...');
    console.table(rows);
});

connection.end();
```

### Mysql 연동 확인

```javascript
➜  mysql npm test

> nodejs-mysql@1.0.0 test
> node index.js

User info is ...
┌─────────┬────────┬──────────┐
│ (index) │   id   │ password │
├─────────┼────────┼──────────┤
│    0    │ 'song' │  '1234'  │
└─────────┴────────┴──────────┘
```

## Nodejs 웹서버

### Express vs Koa

* Express 만든 팀이 2016년에 Koa를 만듬
* Express는 뼈대로 설치되는 모듈이 많고, 미들웨어를 붙일 때 꼭 Express 기반
* Koa는 뼈대로 설치되는 모듈이 적고, 커스터마이징이 자유롭다.
* Koa는 태생이 ES6, Async/Await을 지원
* Express는 community가 강 

#### Express

```javascript
const express = require('express'); 
const app = express(); 
const PORT = process.env.PORT || 3000; 
const server = app.listen(PORT, () => { 
	console.log(`Express is listening to <http://localhost>:${PORT}); 
});
```

#### Koa

```javascript
const koa = require('koa'); 
const app = express(); 
const PORT = process.env.PORT || 3000; 
const server = app.listen(PORT, () => { 
	console.log(`Koa is listening to <http://localhost>:${PORT}); 
});
```

### Middleware 

#### Express

```javascript
app.use((req, res, next) => { 
	console.log(`Time : ${Date.now()}`); 
    next(); 
});
```

#### Koa

```javascript
app.use(async (ctx, next) => { 
	console.log(`Time: ${Date.now()}`); 
    await next(); 
});
```



