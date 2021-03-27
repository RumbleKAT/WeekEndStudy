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

## Node JS 

Nodejs 는 이벤트 기반의 플랫폼. 노드에서 일어나는 모든 일은 어떤 이벤트에 대한 반응. 모두 일련의 콜백이다. libuv라는 추상화된 라이브러리가 이벤트 루프 기능을 제공. 자바스크립트를 실행하는 스레드는 단 하나, 이 스레드가 바로 이벤트 루프가 실행되는 스레드. 

### 모든 CPU 활용 <a id="&#xBAA8;&#xB4E0;-CPU-&#xD65C;&#xC6A9;"></a>

Node.js 어플리케이션은 싱글 스레드로 작동합니다. 멀티코어 환경에서 1개의 Node.js 어플리케이션은 효율적으로 작동하지 않습니다. 낭비되는 CPU가 있기 때문입니다. [Cluster Module](https://nodejs.org/api/cluster.html)을 사용하면, CPU 마다 child 프로세스를 쉽게 만들 수 있습니다. 각각의 child 프로세스는 각자 자신만의 이벤트루프가 존재하고 master 프로세스는 모든 자식들에게 요청을 분산시켜 줍니다.

### 스레드 풀 조정 <a id="&#xC2A4;&#xB808;&#xB4DC;-&#xD480;-&#xC870;&#xC815;"></a>

앞서 언급했듯이, libuv는 스레드 4개로 스레드 풀을 생성합니다. 스레드 풀의 기본 크기는 `UV_THREADPOOL_SIZE` 환경변수를 설정해서 수정할 수 있습니다. 이 방법은 I/O 작업이 많은 어플리케이션에서 도움이 될 수 있겠지만, 큰 스레드 풀은 메모리나 CPU를 고갈시킬 수 있음을 기억해야 합니다.

### 작업을 다른서비스에 맡기기 <a id="&#xC791;&#xC5C5;&#xC744;-&#xB2E4;&#xB978;&#xC11C;&#xBE44;&#xC2A4;&#xC5D0;-&#xB9E1;&#xAE30;&#xAE30;"></a>

만약 Node.js가 CPU사용이 과도하게 필요한 작업에서 사용된다면, 이 특정 작업에 더 잘맞는 다른 언어를 선택해서 그 쪽으로 처리를 옮겨 작업량을 줄이는 것이 가능한 방법일 수 있습니다.

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
➜ $mysql npm install mysql2 --save
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

## Express

Express는 웹 및 모바일 애플리케이션을 위한 일련의 강력한 기능을 제공하는 간결하고 유연한 Node.js 웹 애플리케이션 프레임워크. 

1. require는 nodeJS에서 다른 패키지를 불러올 때 사용되는 키워드. NODE\_PATH 환경 변수에 설정한 위치에서 express라는 모듈을 찾는다.
2. app이라는 변수에 express 함수의 반환값을 저장 / REST End Point 생성
3. Process.env는 nodejs에서 환경 변수를 가져올때 사용됨, 현재 default는 3000
4. app.get / get 요청으로 정의 엔드포인트를 작성 
   * 엔드포인트 생성시 파라미터는 두가지를 받는데, 첫번째 파라미터는 URL 정의, 두번째 파라미터는 해당 url에서 수행할 작업 및 응답을 정의
   * req\(요청\)/ res\(응답\) 

```javascript
module.exports = {
    host : 'localhost',
    user : 'root',
    port : 3306,
    password : 'Init123$',
    database : 'my_db'
}
```

```javascript
const mysql = require("mysql2");
const express = require('express');
const dbconfig = require('./config/database');
const connection = mysql.createConnection(dbconfig);

const app = express();

const port = process.env.PORT || 3000;

app.get('/', (req, res) => {
    res.json({
        success: true,
    });
});

app.get('/users',(req,res)=>{
    connection.query('SELECT * from users',(error, rows)=>{
        if(error) throw error;
        res.json(rows);
    })
})


app.listen(port, () => {
    console.log(`server is listening at localhost:${process.env.PORT}`);
});
```

![](.gitbook/assets/2021-03-23-11.03.25.png)

##  POST Insert 구문 추가

```javascript
const bodyParser = require('body-parser')
app.use(bodyParser.json())

app.post('/users',(req,res)=>{
    const { id , pw } = req.body;
    const sql = 'INSERT INTO users (id,password) VALUES(?,?)';

    connection.query(sql,[id,pw],(err,rows,fields)=>{
        if(err) return console.log(err);
        return console.log(rows.insertId);
    });

    console.log(id, pw);
    res.json({success: "s"});
});
```

![](.gitbook/assets/2021-03-23-11.03.25%20%282%29.png)

```javascript
mysql> select * from users;
+---------+----------+
| id      | password |
+---------+----------+
| song    | 1234     |
| song11  | 1234     |
| song112 | 1234     |
+---------+----------+
3 rows in set (0.00 sec)
```

## Sequelize

ORM이란 객체와 관계형 데이터베이스의 데이터를 자동으로 매핑\(연결\)해주는 것을 말한다. 객체 지향 프로그래밍은 클래스를 사용하고, 관계형 데이터베이스는 테이블을 사용한다. 즉 여기서 Sequelize는 자바스크립트 객체와 데이터베이스의 릴레이션을 매핑해주는 유용한 도구라는 의미이다! 자바스크립트 구문을 알아서 SQL문으로 변경해준다.

```javascript
$ npm i sequelize mysql2
$ npm i -g sequelize-cli
$ sequelize init
```

```javascript
➜  mysql npm i sequelize --save 

added 17 packages, and audited 83 packages in 3s

➜  mysql npm i -g sequelize-cli

added 81 packages, and audited 82 packages in 5s

➜  mysql sequelize init

Sequelize CLI [Node: 12.18.3, CLI: 6.2.0, ORM: 6.6.2]

Created "config/config.json"
Successfully created models folder at "/Users/songmyeongjin/Desktop/mysql/models".
Successfully created migrations folder at "/Users/songmyeongjin/Desktop/mysql/migrations".
Successfully created seeders folder at "/Users/songmyeongjin/Desktop/mysql/seeders".
```

### config/config.json 

DB 연결 관련 config 정보는 모두 config.json에서 관리한다. 

```javascript
{
  "development": {
    "username": "root",
    "password": "Init123$",
    "database": "database_development",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "test": {
    "username": "root",
    "password": null,
    "database": "database_test",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "production": {
    "username": "root",
    "password": null,
    "database": "database_production",
    "host": "127.0.0.1",
    "dialect": "mysql"
  }
}

```

### Sequelize DB Create

Sequelize-cli를 이용해서 DB를 생성하고 조작할 수 있음

```javascript
➜  mysql sequelize db:create

Sequelize CLI [Node: 12.18.3, CLI: 6.2.0, ORM: 6.6.2]

Loaded configuration file "config/config.json".
Using environment "development".
Database database_development created.
```

```javascript
mysql> show databases;
+----------------------+
| Database             |
+----------------------+
| database_development | <-- 
| information_schema   |
| MY_DB                |
| mysql                |
| performance_schema   |
| sys                  |
+----------------------+
6 rows in set (0.01 sec)
```

### Table 생성

Sequelize를 이용하여 DDL을 아래와 같이 작성할수 있다. \(주의해야할 점은, --attribute뒤에 컬럼을 콤마로 구분한다.띄어쓰기없이\)

```javascript
$sequelize model:generate --name Point --attributes pid:integer,pntamt:integer,userId:string  
```

### model 파일과, migration 파일이 생성

생성한 Models은 `/models` 폴더 안에 저장되고, migrations는 `/migrations` 폴더 안에 저장된다. Migrations파일 앞에 붙은 숫자는 시간에 대한 정보로 sequelize는 이를 인식해 어떤 마이그레션이 먼저이고, 어떤 순서로 생성되어야 할 지에 대해 판단한다. \(지금은 별로 중요하지 않지만, 나주엥 columns과 tables를 추가할 때 중요\)

```javascript
Sequelize CLI [Node: 12.18.3, CLI: 6.2.0, ORM: 6.6.2]

New model was created at /Users/songmyeongjin/Desktop/mysql/models/point.js .
New migration was created at /Users/songmyeongjin/Desktop/mysql/migrations/20210326125441-create-point.js .
```

```javascript
mysql> desc points;
+-----------+--------------+------+-----+---------+----------------+
| Field     | Type         | Null | Key | Default | Extra          |
+-----------+--------------+------+-----+---------+----------------+
| id        | int          | NO   | PRI | NULL    | auto_increment |
| pid       | int          | YES  |     | NULL    |                |
| pntamt    | int          | YES  |     | NULL    |                |
| userId    | varchar(255) | YES  |     | NULL    |                |
| createdAt | datetime     | NO   |     | NULL    |                |
| updatedAt | datetime     | NO   |     | NULL    |                |
+-----------+--------------+------+-----+---------+----------------+
6 rows in set (0.00 sec)
```

