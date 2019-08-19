# 다양한 연관관계 매핑

### 목차

* 연관관계 매핑시 고려사항 3가지
* 다대일 [N:1]
* 일대다 [1:N]
* 일대일 [1:1]
* 다대다 [N:M]

## 연관관계 매핑시 고려사항 3가지

- 다중성
  - 다대일, 일대다, 일대일, 다대다
- 단방향, 양방향
- 연관관계의 주인

### 다중성

* JPA 에서는 다중성을 위한 어노테이션을 제공한다.
* 이 JPA 어노테이션들은 DB와 매핑하기 위해 존재 한다.
* 그래서 데이터베이스 관점에서의 다중성을 기준으로 고민 하면 된다.
* 다대일 - @ManyToOne
* 일대다 - @OneToMany
* 일대일 - @OneToOne
* 다대다 - @ManyToMany
  * 실무에서 쓰면 안된다.
* 다중성을 고민하다가 풀리지 않으면 대칭성을 고려하자. 다중성의 관계들은 대칭성을 다 가지고 있다. 예를 들면, 회원과 팀 - 팀과 회원 둘다를 고려하면 쉬워 진다.

### 단방향, 양방향

* 이전 학습 내용 정리
* 테이블은
  * 외래 키 하나로 양쪽을 조인할 수 있다.
  * 사실 방향이라는 개념이 없다.
* 객체는
  * 참조용 필드가 있는 쪽으로만 참조할 수 있다.
  * 한쪽만 참조하면 **단방향**이고
  * 양쪽이 서로 참조하면 **양방향**이다.
    * 앞서 말햇듯이 사실은 단방향이 두개가 마주보고 있는 것이다 

### 연관관계의 주인

* 위의 단방향, 양방향의 개념을 알아야. 연관관계의 주인을 이해할 수 있다.
* 테이블은 외래 키 하나로 두 테이블이 연관관계를 맺는데,
* 객체의 양방향 관계는 A->B, B->A 처럼 참조가 2군데에서 일어난다.
* 그렇기 때문에 객체에서 둘중 테이블의 외래키를 관리할 곳을 정해야 한다.
* **연관관계의 주인은 외래 키를 관리하는 참조**이다.
* 주인의 반대편에서는 외래키에 영향을 주지 않고, 단순 읽기만 할 수 있다.

## 다대일 [N:1]

* JPA에서 가장 많이 사용하고, 꼭 알야야 되는 다중성이다.
* 아래 테이블에서 보면, **DB 설계상 일대다에서 다 쪽에 외래키가 있어야 한다**. 그렇지 않으면 잘못된 설계다.
* 객체로 와서, 다대일 관계에서 다인 멤버에서 단순하게 팀으로 단방향 참조를 하고 싶다고 할 때, 테이블에 외래키 있는 쪽인 멤버 객체에서 팀객체로 연관관계 매핑 하면 된다.
* 테이블에서 FK가 팀을 찾기 위해 존재하고, 객체에서 Team 필드도 Team을 참조하기 위해 존재한다.
* 다대일의 반대는 일대다

![](https://github.com/namjunemy/TIL/blob/master/Jpa/inflearn/img/09_modeling.png?raw=true)

* JPA **@ManyToOne으로 다대일 관계 단방향 매핑**

  ```java
  public class Member {
    ...
    
      @ManyToOne
      @JoinColumn(name = "TEAM_ID")
      private Team team;
    
    ...
  }
  ```

### 다대일 양방향 매핑

* 다대일 관계에서 단방향 매핑을 진행하고, 양방향 매핑을 진행할 때
* 반대쪽에서 일대다 단방향 매핑을 해주면 된다.(객체에서 컬렉션 추가해주면 된다.)
* 여기서 중요한건, 반대에서 단방향 매핑을 한다고 해서 DB 테이블에 영향을 전혀 주지 않는다.
* 다대일관계의 **다 쪽에서 이미 연관관계의 주인이 되어서 외래키를 관리**하고 있다.

![](https://github.com/namjunemy/TIL/blob/master/Jpa/inflearn/img/11_many_to_one.PNG?raw=true)

* 반대쪽에서 일대다 단방향 매핑 JPA **@OneToMany** 사용

  * 연관관계의 주인이 아니고, 어디에 매핑 됐는지에 관한 정보 **(mappedBy = "team")**을 꼭 넣어줘야 한다.

  * 그러면 members 필드는 team에 의해 매핑 되어진 것이라는 의미 전달을 확실하게 할 수 있다.

    ```java
    public class Team {
        ...
    
        @OneToMany(mappedBy = "team")
        private List<Member> members = new ArrayList<>();
        
        ...
    }
    ```

* 정리

  * 외래키가 있는 쪽이 연관관계의 주인이고,
  * 양쪽을 서로 참조하도록 개발하면 된다.