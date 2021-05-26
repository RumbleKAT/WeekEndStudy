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

## ORM 패러다임 불일치

* 밀도 문제 --&gt;  객체 : 다양한 크기 만들수 있고, 커스텀 만들수 있다. / 테이블 :  고정 크기 
* 서브 타입 문제 --&gt; 객체 : 상속구조 / 테이블 : 테이블 상속이 없음 
* 식별성의 문제 --&gt; 객체 : == equals / 테이블 : primary key
* 관계 문제 --&gt; 객체 : 근본적으로 방향이 존재 \(다대다\) / 테이블 : 외래키로 관계 표현 Join으로 아무거나 만들수 있음
* 데이터 네비게이션 문제 --&gt; 레퍼런스를 이용해서 다른 객체로 이동가능 / db 억세스 최소화를 위해 Join을 쓰지만, 너무 한번에 많이 가져오는 것도 성능 이슈, 그렇다고 lazy loading을 하기에도 문제 \(n+1 select\)

### JPA DDL 자동설정 옵션

* `create`: 스키마와 데이터가 매번 새롭게 생성됨 개발시 사용
* `update`: 스키마와 데이터를 유지하면서 스키마나 데이터에 대한 변경사항을 적용\(스키마 변경시 이전 스키마가 남아있음\)
* `create-drop`: 스키마와 데이터가 매번 새롭게 생성되고 종료시 제거 됨
* `validate`: 스키마를 검증만 해준다 운영시 사용

```java
spring.jpa.hibernate.ddl-auto=create
```

##  맵핑 어노테이션

* `@Id`: 데이터베이스 주키\(Primary Key\)에 맵핑
* `@GeneratedValue`: 자동 생성 설정
* `@Entity`: 클래스명에 해당되는 테이블에 맵핑

어플리케이션을 실행하면, @Entity 로 모두 테이블에 자동매핑 컬럼생성 -&gt; datasource 타입의 빈을 만들고, 빈들은 application.properties 정보를 참조하여 만들어진다.

```java
import lombok.*;
import javax.persistence.*;

@Entity
@Getter
@NoArgsConstructor
@AllArgsConstructor
@ToString
public class Team {
    @Id
    @Column(name = "TEAM_ID")
    private String id;

    private String name;
}
```

```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface TeamRepository extends JpaRepository<Team,Long> {
}
```

## EntityManager 

* `@PersistenceContext`를 통해서 **JPA**의 핵심인 **EntityManager**를 주입 받음
* 이 클래스를 통해서 **Entity**들을 영속화 할 수 있음\(데이터베이스에 저장\)
* **JPA**와 관련된 모든 **Operation**들은 한 **Transaction** 안에서 일어나야함
* **Spring**에서 제공하는 `@Transactional`을 사용 클래스,메서드에 적용할 수 있음

```java
import com.rumblekat.jpastudy.domain.tram.MemberRepository;
import com.rumblekat.jpastudy.domain.tram.Team;
import com.rumblekat.jpastudy.domain.tram.TeamRepository;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.transaction.annotation.Transactional;

import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;

import static org.assertj.core.api.Assertions.assertThat;

@RunWith(SpringRunner.class)
@SpringBootTest
public class MemberTeamTest {

    @Autowired
    MemberRepository memberRepository;

    @Autowired
    TeamRepository teamRepository;

    @PersistenceContext
    EntityManager entityManager;

    @Test
    @Transactional
    public void 엔티티매니저_테스트(){
        Team team1 = new Team("team1", "팀1");
        entityManager.persist(team1);

        Team team2 = new Team("team2", "팀2");
        teamRepository.save(team2);

        assertThat(teamRepository.findAll().size() == 2);
        assertThat(entityManager.createQuery("SELECT t FROM Team as t",Team.class).getResultList().size() == 2);

        teamRepository.findAll().forEach(System.out::println);
    }


}

```



