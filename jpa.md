---
description: 야매로 배우는 JPA
---

# JPA

## **관계형 데이터베이스와 자바**

Persistence \(영속성\)이란 어떤 정보를 저장하고, 앱을 다시 켜도 그 데이터는 유지가 됨 

### 기존 JDBC를 이용할때 문제점?

* SQL을 실행하는 비용이 비싸다.
* SQL이 DB마다 다르다.
* 스키마를 바꿨는데 코드가 바뀐다.
* 반복적인 코드가 많고, 당장 쓸모없는 데이터도 많이 받아오는 경우가 많

###  JDBC 코드 샘플

```java
public class Application {

    public static void main(String[] args) throws SQLException {
        String url = "jdbc:postgresql://localhost:5432/springdata";
        String username = "root";
        String password = "0000";

        try(Connection connection = DriverManager.getConnection(url, username, password)){
            System.out.println("Connection created: "+ connection);
            String sql = "CREATE TABLE ACCOUNT (id int, username varchar(255), password varchar(255));";
            sql = "INSERT INTO ACCOUNT VALUES(1, 'root', '0000');";
            try(PreparedStatement statement = connection.prepareStatement(sql)){
                statement.execute();
            }
        }
    }
}

```

## ORM이란

애플리케이션의 클래스와 SQL 데이터베이스 테이블 사이의 매핑 정보를 기술한 메타데이터를 사용하여, 자바 객체를 SQL데이터베이스의 테이블에 자동으로 영속화 해주는 기술 =&gt; 도메인 모델을 사용하는 방식 \(행위와 데이터를 둘다 아우르는 도메인의 개념 모델\)

## 도메인 모델을 사용하려는 이유?

* 객체 지향 프로그래밍의 장점을 활용하기 좋다. 
* 각종 디자인 패턴
* 코드 재사용
* 비즈니스 로직 구현 및 테스트 편리성 

## 비침투성 논란의 여지

* trasparent: 자기 자신의 코드를 숨기려고함
* SpringFramework, Hibernate, ORM도 비 침투성적 철학을 가지고 있으나, EntityManager를 사용하기 때문에 아주 비침투적이지 않다.



