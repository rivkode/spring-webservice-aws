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

![](https://user-images.githubusercontent.com/109144975/213993173-03843040-5f73-4bce-b784-a947de1e43f4.png)

위 5가지 레이어에서 비즈니스 처리를 담당해야 할 곳은 Domain 입니다.

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

