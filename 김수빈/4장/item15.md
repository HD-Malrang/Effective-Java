## 클래스와 멤버의 접근권한을 최소화해라

 

- 정보은닉 : 소프트웨어에서 사용하는 객체에 대한 구체적인 정보를 노출시키지 않도록 하는 기법

 

### 정보은닉의 장점

1. 시스템 개발 속도를 높인다. (병렬 개발 가능)

- 정보은닉을 위해 인터페이스를 설계하게 되면 , 인터페이스를 사용하는 쪽, 구현하는 쪽 동시에 개발 진행 가능.

  - 내부 로직이 없어도 메서드 자체를 호출 가능하기 때문에 개발 가능.

 

2. 시스템 관리 비용을 낮춘다.

- 인터페이스를 통해 각 컴포넌트를 더 빨리 파악할 수 있다. 다른 컴포넌트로 교체하는 부담도 적다.

 

3. 성능 최적화에 도움을 준다.

- 정보은닉 자체가 성능에 도움이 되는 것은 아니고, 다른 컴포넌트에 영향을 주지 않고 특정 컴포넌트만 최적화 가능하다.

 

4. 소프트웨어 재사용성을 높인다.

- 독자적으로 동작할 수 있는 컴포넌트라면 다른 프로젝트에서도 그대로 사용할 수 있다.

 

5. 시스템 개발 난이도를 낮춘다.

- 시스템 전체가 완성되지 않은 상태에서도 개별 컴포넌트의 동작을 검증할 수 있다.

 

### 클래스, 인터페이스, 멤버의 접근 제한자 제대로 활용하기

- 기본 원칙 : 소프트웨어가 올바로 동작하는 한 항상 가장 낮은 접근 수준을 부여해야한다.


 

1. 톱레벨 클래스와 인터페이스

- 부여할 수 있는 접근 수준 : package-private , public

  -> 패키지를 외부에서 쓸 이유가 없다면 default로 선언. 그러면 이들은 클라이언트에 피해 없이 수정 가능.

   ex. 구현체 같은 경우는 내부 클래스에서만 알면 되기 때문에 default가 적절하다.

```java
class DefaultMemberService implements MemberService {

}
```
 

- 이를 사용하는 클래스 안에 private static으로 중첩시켜 보기.

  -> 바깥 클래스 하나에서만 접근 가능. 바깥 클래스의 멤버들에 대한 접근이 불가능.

  만약 내부 클래스에서 외부 클래스의 필드를 참조하고 싶다면 private class로.
```java
class DefaultMemberService implements MemberService {

   private static class MemberRepository {

   }
}
```
 

2. 멤버 (필드,메서드,중첩 클래스, 중첩 인터페이스)

- 부여할 수 있는 접근 수준 : 4가지

 

- 클래스의 공개 API를 세심히 설계한 후, 그 외의 모든 멤버는 private으로 만들자.  그런 다음, 같은 패키지의 다른 클래스가 접근해야 하는 멤버에 한하여 제한자 제거.

 

** <b>상위 클래스의 메서드를 재정의할 때는 그 접근 수준을 상위 클래스에서보다 좁게 설정 할 수 없다.</b>

  -> 리스코프 치환 원칙. (상위 클래스의 인스턴스는 하위 클래스의 인스턴스로 대체해 사용 가능해야함)

 

- public 클래스의 인스턴스 필드는 되도록 public이 아니어야한다. 
  - 예외 : 상수용 public static final 필드

 

- 테스트만을 위해 멤버를 공개 API로 만들어서는 안 된다.

 

- public static final 배열 필드를 두거나 이 필드를 반환하는 접근자 메서드를 제공하면 안된다.

  -> 클라이언트에서 배열을 수정할 수 있게 된다.

```java
// 보안 허점이 숨어 있다.
public static final Thing[] VALUES = { ... };
```
해결책. 
```java
// 1. 
private static final Thing[] PRIVATE_VALUES = { ... };
public static final List<Thing> VALUES =
	Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));

// 2.
private static final Thing[] PRIVATE_VALUES = { ... };
public static final Thing[] values() {
    return PRIVATE_VALUES.clone();
}
```