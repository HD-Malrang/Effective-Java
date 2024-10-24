# equals는 일반 규약을 지켜 재정의 하라
### equals 재정의를 사용하지 않아도 되는 경우
1. 각 인스턴스가 본질적으로 고유한 경우
   - Thread 처럼 값을 표현하는 것이 아닌 동작하는 개체를 표현하는 클래스는 Object 클래스의 equals 메서드에서 기본적으로 제공한다.
2. 논리적 동치성(logical equality)을 검사할 일이 없는 경우
3. 상위 클래스에서 재정의한 equals가 하위 클래스에도 적합한 경우
   - 예를 들어 Set 인터페이스는 AbstractSet 이 구현한 equals를 사용하는 것을 의미한다.
4. 클래스가 private하거나 default인 경우
### equals 재정의를 사용하는 경우
- 값을 비교하기 위해서 주솟값 비교인 객체 식별 확인이 아닌, 논리적 동치성을 확인할 때

### 재정의를 할때 지켜야 하는 규약
- 반사성(reflexivity) : null이 아닌 모든 참조 값 x에 대해, x.equals(x)는 true이다.
- 대칭성(symmetry) : null이 아닌 모든 참조 값 x, y에 대해 x.equals(y)가 true면 y.equals(x)도 true이다.
- 추이성(transitivity) : null이 아닌 모든 참조 값 x, y, z에 대해 x.equals(y)가 true이고 y.equals(z)도 true 이면 x.equals(z)도 true이다.
- 일관성(consistency) : null이 아닌 모든 참조 값 x,y에 대해 x.equals(y)를 반복해서 호출하면 항상 같은 값을 반환한다.
- null-아님 : null이 아닌 모든 참조 값 x에 대해 x.equals(null)은 항상 false다.
### 올바른 equals 구현 방법
```java
public final class PhoneNumber {
  private final short areaCode, prefix, lineNum;

  public PhoneNumber(...){...}

  @Override
  public boolean equals(Object o) {
    if (o == this)
      return true;
    if (!(o instanceof PhoneNumber))
      return false;
    PhoneNumber pn = (PhoneNumber) o;
      return pn.lineNum == lineNum && pn.prefix == prefix && pn.areaCode == areaCode;
    }
}
```
- == 연산자를 사용하여 입력이 자기 자신의 참조인지 확인한다.
- instanceof 연산자로 입력이 올바른 타입인지 확인한다.
- 입력을 올바른 타입으로 형변환 한다.
- 입력 객체와 자기 자신의 대응되는 핵심 필드들이 모두 일치하는지 검사한다.

#### !! 꼭 필요한 경우가 아니라면 equals를 재정의하지 말자. <br> !! equals를 재정의할 땐 hashCode도 반드시 재정의하자.