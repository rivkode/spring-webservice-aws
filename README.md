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
