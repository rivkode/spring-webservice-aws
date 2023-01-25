# SpringWebService - AWS

## 2장

스프링 부트에서 테스트 코드를 작성하자

@SpringBootApplication 으로 인해 스프링 부트의 자동 설정, 스프링 Bean 읽기와 생성을 모두 자동으로 설정함

SpringApplication.run으로 인해 내장 WAS 를 실행
    내장 WAS를 실행하는 이유는 '언제 어디서나 같은 환경에서 스프링 부트를 배포하기 위함'

```java
@Test
    public void 롬복_기능_테스트() {
        //given
        String name = "test";
        int amount = 1000;

        //when
        HelloResponseDto dto = new HelloResponseDto(name, amount);

        //then
        assertThat(dto.getName()).isEqualTo(name);
        assertThat(dto.getAmount()).isEqualTo(amount);
    }
```

given, when, then 을 활용하여 테스트코드를 작성하였습니다.

HelloResponseDto 클래스 객체를 통해 "test", 1000 값들로 초기화 한 뒤 해당 값이 일치하는지 assertj의 assertThat 메소드를 사용하여
확인하였습니다. isEqualTo는 양쪽을 비교하는 메소드이며 서로 일치함을 의미합니다.

아래와 같이 테스트에 성공하였습니다.

>BUILD SUCCESSFUL in 1s
4 actionable tasks: 1 executed, 3 up-to-date
오후 3:44:43: Execution finished ':test --tests "com.rivkode.springwebservice.dto.HelloResponseDtoTest.롬복_기능_테스트"'.

**테스트시 builder를 유용하게 사용하자**

```java
    @Builder
    public Posts(Long id, String title, String content, String author) {
        this.id = id;
        this.title = title;
        this.content = content;
        this.author = author;
    }
```

위와 같이 Posts 엔티티 클래스에 Builder 애노테이션이 있을 경우 파라미터를 헷갈리지 않게 아래와 같이 넣을 수 있다

```java
postsRepository.save(Posts.builder()
        .title(title)
        .content(content)
        .author("rivs@kakao.com")
        .build());
```

## 3장

스프링 부트에서 JPA로 데이터베이스를 다뤄보자

**JPA**

객체를 관계형 데이터베이스에서 관리하는 것이 중요함

>패러다임 불일치란?
> 
> 관계형 데이터베이스(어떻게 데이터를 저장할지)와 객체지향 프로그래밍 언어(기능과 속성을 한 곳에서 관리)의 패러다임이 서로 다른데, 객체를
> 데이터베이스에 저장하려고 하니 여러 문제가 발생

서로 지향하는 바가 다른 2개 영역을 중간에서 패러다임 일치를 시켜주기 위한 기술

개발자는 객체지향적으로 프로그래밍을 하고 JPA가 이를 관계형 데이터베이스에 맞게 SQL을 대신 생성해서 실행함, 더는 SQL에 종속적인 개발을 JPA
덕분에 하지 않아도 됨

JPA는 인터페이스로 자바 표준 명세서임

이를 사용하려면 Hibernate, Eclipse Link 같은 구현체가 필요

관계도

JPA <- Hibernate <- Spring Data JPA

### 등록 수정 조회 API 만들기

API를 만들기 위해서는 3개의 클래스 필요

- Request 데이터를 받을 Dto
- API요청을 받을 Controller
- 트랜잭션, 도메인 기능 간의 순서를 보장하는 Service

**Service에서 비즈니스 로직을 처리하지 않음**

Service는 트랜잭션, 도메인 간 순서 보장의 역할만 함

순서를 어떻게 보장할까 ?

의존성을 활용함

service는 repository를 의존하며
controller는 service를 의존한다

즉 controller -> service -> repository 순으로 의존하게 되며 이 순서를 보장한다.


![](https://user-images.githubusercontent.com/109144975/213993173-03843040-5f73-4bce-b784-a947de1e43f4.png)

Web Layer

- 외부 요청과 응답에 대한 전반적인 영역

Service Layer

- controller 와 Dao 의 중간 영역에서 사용됨
- @Transactional이 사용되어야 하는 영역

Repository Layer

- DataBase와 같이 데이터 저장소에 접근하는 영역

Dtos

- 계층 간에 데이터 교환을 위한 객체를 이야기 하며 Dtos는 이들의 영역을 얘기함

Domain Model

- 개발 대상을 모든 사람이 동일한 관점에서 이해할 수 있고 공유할 수 있도록 단순화 시킨 것
- 택시 앱일 경우 배차, 탑승, 요금 등 모두 도메인
- @Entity가 사용되는 영역이 도메인 모델

위 5가지 레이어에서 비즈니스 처리를 담당해야 할 곳은 Domain 입니다.

Entity 클래스와 유사한 형태의 Dto 클래스를 나누는 이유는 뭘까요 ?

ENtity 클래스는 데이터베이스와 맞닿은 핵심 클래스, Entity 클래스 기준으로 테이블이 생성되고, 스키마가 변경됩니다.

화면 변경은 아주 사소한 기능 변경인데, 이를 위해 테이블과 연결된 Entity 클래스를 변경 하는 것은 너무 큰 변경입니다.


### 도메인 모델 패턴

각각의 객체가 책임을 가지고 요청을 처리한다는 것

트랜잭션 스크립트는 도메인 레이어에서 정보를 불러와서 레이어 내부에서 처리한다.

반면 도메인 모델은 데이터 별로 객체가 존재하고 해당 객체가 내부에서 데이터를 처리한다.

그렇기 때문에 도메인 레이어 하나만으로는 작동할 수 없다.

데이터를 객체에 전달해주고 전달 받아서 반환하는 등의 후처리가 필요하다.

그때 필요한 것이 바로 서비스 레이어이다.





PostsApiControllerTest

위 테스트코드 다시 작성하기

등록 API

수정 API

조회 API


update에 쿼리문을 날리지 않음

영속성 컨텍스트

영속성 컨텍스트란?
엔티티를 영구 저장하는 환경임
JPA 핵심 내용은 엔티티가 영속성 컨텍스트에 포함 되어 있냐 아니냐로 나뉨

JPA의 엔티티 매니저가 활성화된 상태로 트랜잭션 안에서 데이터베이스에서 데이터를 가져오면 이는 영속성 컨텍스트가 유지된 상태임

이 상태에서 데이터 값을 변경하면 트랜잭션이 끝나는 시점에 테이블에 변경분을 반영함

이것이 **더티체킹** 임

## 4장

머스테치로 화면 구성하기

