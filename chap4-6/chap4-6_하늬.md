# 4. TDD, 기능 명세, 설계

### 기능 명세

- 기능 명세의 다양한 형태: ppt로 스토리보드, 이메일로 설명, 지라로 이슈 관리...
- 어떤 형태든 공통적으로 두 가지로 생각 : 입력과 결과
    1. 입력 : 파라미터
    2. 결과 : 리턴 값, 익셉션, 변경
- 설계는 기능 명세로부터 시작한다. (선 기능 명세 후 설계)
    - 다양한 요구사항 문서 → 기능 명세 구체화 → 입력, 결과 도출 → 코드에 반영(설계 이뤄짐)

### 설계 과정을 지원하는 TDD

- TDD는 테스트 코드부터 만든다 → 테스트 코드에 무엇이 필요한가?
    1. 테스트할 기능을 실행 (테스트 코드에 사용할 클래스, 메서드 필요)

        → 클래스 이름, 메서드 이름/파라미터

    2. 실행 결과를 검증

        → 메서드 리턴 타입

- TDD 자체가 설계는 아니지만, 위처럼 테스트 코드를 작성하는 과정에서 일부 설계를 하게 됨.
- 이름 설계에 걸리는 시간을 아까워하지 말자

### 필요한 만큼 설계하기

- TDD는 테스트를 통과할 만큼만 코드를 작성한다. 미리 예측해서 설계를 유연하게 만들지 않는다.
- 지금 시점에서 필요한 설계만 반영하고, 유연한 설계는 필요한 시점에 추가한다.
- 항상 최초 설계가 유지된단 보장은 없는데 이 때 TDD를 적용하면 적어도 불필요한 구성 요소를 덜 만들게 한다는 뜻.

### 기능 명세 구체화

- 개발자는 요구사항 문서가 나왔어도 별도로 기능의 입력과 결과를 도출해야한다.
- 다양한 테스트 케이스를 추가하면서 애매한 점을 발견하면 기획자와 커뮤니케이션하여 구체적으로 정리해야 한다.

    → 테스트 코드 = 예를 이용한 구체적인 명세

---

# 5. JUnit5 기초

> 우리는 JUnit의 assertThat 대신 AssertJ의 assertThat을 쓰기로 함

### JUnit5 모듈 구성

- JUnit 플랫폼 : 테스팅 프레임워크를 구동하기 위한 런처, 테스트 엔진을 위한 API 제공
- JUnit 주피터 : JUnit5를 위한 테스트 API, 실행 엔진 제공
- JUnit 빈티지 : JUnit3과 4로 작성된 테스트를 JUnit5 플랫폼에서 실행하기 위한 모듈 제공

### `@Test` 와 테스트 메서드

```java
//...
import static org.assertj.core.api.Assertions.*;

public class SumTest {
	@Test
	void sum() {                 
		int result = 2 + 3;
		assertThat(result).isEqualTo(5);
}
```

- 테스트 클래스 이름 : 접미사 `Test` 사용
- 테스트할 메서드 : private이면 안됨. `@Test` 를 붙인다.

### 주요 단언 메서드 (AssertJ)

AssertJ Core : JDK 타입에 단언을 제공해주는 라이브러리이다.

```xml
<dependency>
  <groupId>org.assertj</groupId>
  <artifactId>assertj-core</artifactId>
	<version>3.20.2</version>
  <scope>test</scope>
</dependency>
```

```json
testImplementation("org.assertj:assertj-core:3.20.2")
```

```java
@Test
public void withAssertions_examples() {

  // 값 일치 여부 검사 (실제값, 기대값 순)
  assertThat(frodo.age).isEqualTo(33);
  assertThat(frodo.getName()).isEqualTo("Frodo").isNotEqualTo("Frodon");

  // 값 포함 여부 검사
  assertThat(frodo).isIn(fellowshipOfTheRing);
  assertThat(frodo).isIn(sam, frodo, pippin);
  assertThat(sauron).isNotIn(fellowshipOfTheRing);

  // 값 조건 만족 검사 (파라미터에 Predicate형 사용)
  assertThat(frodo).matches(p -> p.age > 30 && p.getRace() == HOBBIT);
  assertThat(frodo.age).matches(p -> p > 30);
}
```

```java
// 테스트 실패
doSomething(optional.orElse(() -> fail("boom")));.
doSomething(optional.orElse(() -> fail("b%s", "oom")));
doSomething(optional.orElse(() -> fail("boom", cause)));
// 발생했어야하는 예외 체크 (아래 두개 교차 가능)
doSomething(optional.orElse(() -> failBecauseExceptionWasNotThrown(IOException.class)));
doSomething(optional.orElse(() -> shouldHaveThrown(IOException.class)));
```

### 테스트 라이프사이클

- 각 테스트마다 테스트 메서드를 포함한 객체 생성
- `@BeforeEach` , `@AfterEach` : 각 테스트 실행 전, 후에 실행 (임시 파일 생성, 삭제)
- `@BeforeAll` , `@AfterAll` : 클래스의 모든 테스트 실행하기 전, 후 한번만 실행
→ 정적 메서드에 적용한다
    - 이유?
    - non-static 메서드에 적용하면 JUnitException이 발생함. 정상 적용하려면 테스트 클래스에 `@TestInstance(Lifecycle.PER_CLASS)` 를 달아줘야  한다. (디폴트 : *PER_METHOD*)

### 테스트 메서드 간 실행 순서 의존과 필드 공유하지 않기

- 한 테스트의 결과에 따라 다른 테스트의 결과가 달라지면 안 된다.
- 테스트 간 필드 공유, 실행 순서를 만들지 말아야한다. (유지 보수)

### `@DisplayName`, `@Disabled`

```java
@DisplayName("테스트 클래스에 붙일 수 있다.")
public class DisplayTest {
	@DisplayName("테스트 메서드에 붙일 수 있다.")
	@Test
	void assertMethod(){ ... }

	@Test
	void 테스트_입니다() { ... }
}
```

### 모든 테스트 실행

- `mvn test (mvnw test) 또는  gradle test (gradlew test)`
- 각 라이프사이클에 따르면 `maven package, gradle build` 명령에도 test를 실행한다.

---

# 6. 테스트 코드의 구성

### 상황

- 주어진 상황에 따라 기능 실행 결과는 달라진다 → 테스트 코드 구조(given, when, then)
- 상황 설정
    - 각 테스트 메서드 마다 객체 생성 후 상황 설정
    - `@BeforeEach`를 적용한 메서드에서 상황 설정
- 실행 결과 : 리턴 값 , 익셉션 발생

### 외부 상황과 결과

- 상황에는 외부 요인도 있다. (파일, DBMS, 외부 서버...)
    - 상황 설정 시 우연을 고려해서 테스트 실행 결과를 보장하도록 작성해야한다.
    - 버전 관리 대상에도 상황 설정을 위해 생성한 외부 파일 등을 포함시켜야한다.
- 외부 환경을 테스트에 맞게 구성하는 것이 항상 가능한 것은 아니다 → 대역 사용
- 대역 : 테스트 대상이 의존하는 대상의 실제 구현을 대신하여 외부 상황이나 결과를 대체할 수 있다. → 7장...
- Mockito → Mock 객체