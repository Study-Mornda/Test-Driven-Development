# 11. 마치며

- 질문 chap10 : **BizCLock클래스(**231pg)

    *부모 클래스의 static 메서드가 상속받은 하위클래스에 특별한 영향을 주나?
    - 왜 static으로 자기자신을 인스턴스 생성해서 필드로 설정했을까? → 하위클래스의 필드들을 사용하기 위해?  ⇒ 싱글톤 (effective java chap3)*

    ```java
    public class BizCLock {
    	private static BizClock DEFAULT = new BizClock();
    	private static BizCLock instance = DEFAULT;

    	public static void reset() {
    	  instatnce = DEFAULT;
      }
    	public static LocalDateTime now() {
    		return instance.timeNow();
    	}
    	protected void setInstance(BizClock bizClock) {
    		BizCLock.instance = bizClock;
    	}
    	public LocalDateTime timeNow(){
    		return LocalDateTime.now();
    	}
    }
    ```

    ```java
    class TestBizClock extends BizClock {
    	private LocalDateTIme now;
    	
    		public TestBizCLock() { //인스턴스 설정
    		setInstance(this);
    	}
    	public void setNow(LocalDateTime now){
    		this.now = now;
    	}
    	@Override
    	public LocalDateTime timeNow() {
    		return now != null ? now : super.now();
    	}
    }
    ```

## 테스트 우선하기

TDD를 적용하기 이전 : 압박 → {스트레스 증가 → 테스트 안함}

TDD 적용하면 : 압박 → 스트레스 증가 → { 테스트 작성→ 구현, 테스트 통과 → **버그 감소 신뢰 증가 → 스트레스 감소**}

- 회귀 테스트 : 코드를 수정해도 기존 코드가 올바르게 동작하는지 확인하기 위한 테스트

    → 테스트를 먼저 수행하고 코드를 구현하므로 테스트코드가 회귀 테스트로 사용될 수 있다. (안전 장치 역할)

## TDD 전파하기

경험할 수 있는 TDD의 효과: 결합 감수, 스트레스 감소, 빠른 피드백

- 그러나 **본인이 먼저 TDD에 익숙해져야** 전파시 동료들의 TDD에 대한 거부감을 덜어낼 수 있다.

    → 개인 프로젝트를 통해 TDD 연습

    → 업무에 적용해보기

- 짝 코딩을 통해 전파하기
    1. 동료가 눈으로 TDD를 경험하게 만든다 (대역 생성 방법, 업무 코드, 한번에 많이x)
    2. 테스트 코드만 내가 만들고 동료가 테스트를 통과시키게 한다.
    3. 테스트 코드를 동료가 직접 만들게 한다.
- 레거시 코드에 적용해보기

    > 도서 추천: Working Effectively with Legacy Code (레거시 코드 활용 전략)

    → 일부 코드 리팩토링해서 테스트 코드를 만들 수 있는 구조로 변경한다. (약간의 위험 감수할 만 함)

    → 테스트 코드를 작성할 별도의 클래스를 만들어 기존 동작에 대한 영향을 줄이면 된다.

## TDD와 개발 시간

보통 TDD 적용 반대 이유 : "*테스트 코드까지 작성하기엔 시간이 많이 걸린다!"*

but, 개발 시간을 분석해보면...

- 처음 코딩 시간
- 테스트 시간
- 디버깅 시간 → 개발 완료할 때까지 반복된다!

즉 개발 시간을 줄이려면

- 테스트 시간 → TDD는 테스트 자동화할 테스트 코드를 만든다.
- 디버깅 시간 → TDD는 기능을 구현하자마자 테스트를 진행한다.

`+ TDD는 리팩토링을 포함한다(코드 구조, 가독성 개선)`

테스트와 개발 시간 개선 예시에서 말하고자 하는 것...

→ 톰캣 실행, 엑셀 다운로드 등 코드 작성 이외의 작업 없이 ****기능별로 진행 가능

→ Task 순서도 정할 수 있다.

## 맺음말...

처음엔 쉬운 것을 TDD로 시도해서 익숙해진 후 점진적으로 TDD 적용 범위를 늘리자..

TDD가 주는 효과는 대단하며 TDD가 습관이 될 때까지 연습하자!