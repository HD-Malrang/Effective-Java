# equlas는 일반 규칙을 지켜 재정의하라

기본적으로 equla()는 Object 클래스에 정의되어 있어, 참조 비교(두 객체가 동일한 메모리 위치에 있는지 확인)만 수행한다.

이를 논리적으로 두 객체가 같은지를 비교하기 위해서는 equlas() 메서드를 재정의해야 한다.

equals를 재정의해야 할 때는 언제일까?

객체 식별성(object identity；두 객체가 물리적으로 같은가)이 아니라 논리적 동치성을 확인해야 할 때이다.

equlas 메서드는 동치 관계를 구현하기 위해 다음과 같은 성질을 만족해야 한다. (리스코프 치환 원칙과 관련)

- 반사성: 모든 참조 값 `x`에 대해, `x.equals(x)`는 항상 `true`
    - 어떤 객체도 자기 자신과는 동등하다는 것
- 대칭성: 모든 참조 값 `x`와 `y`에 대해, `x.equals(y)`가 `true`이면 `y.equals(x)`도 `true`이어야 한다.
    - 두 객체 간의 동치 관계는 서로에게 대칭적이어야 한다.
- 추이성: 모든 참조 값 `x`, `y`, `z`에 대해, `x.equals(y)`가 `true`이고 `y.equals(z)`가 `true`이면 `x.equals(z)`도 `true`이어야 한다.
    - 동치 관계가 전이적이어야 함을 의미한다.
- 일관성: null이 아닌 모든 참조 값 `x`, `y`에 대해, `x.equals(y)`를 반복해서 호출하면 항상 `true` 또는 항상 `false`를 반환해야 한다.
    - 같은 객체에 대해 여러 번 호출할 때 결과가 변하지 않아야 한다.
- Null-아님: null이 아닌 모든 참조 값 `x`에 대해, `x.equals(null)`은 항상 `false`여야 한다.
    - 어떤 객체도 `null`과는 동등하지 않다고 판단해야 한다.

---

### equlas 메서드 구현 방법

1. **자기 참조 검사**
    - 입력 객체가 자기 자신인지 확인한다.
    - 동일한 객체일 경우 `true`를 반환한다.

    ```java
    if (o == this) return true;
    ```

2. **타입 검사**
    - `instanceof` 연산자를 사용해 입력 객체가 올바른 타입인지 확인한다.
    - 잘못된 타입일 경우 `false`를 반환한다.
    - 주의: 인터페이스를 사용해야 할 경우도 있으니, 상황에 맞게 조정해야한다.

    ```java
    if (!(o instanceof YourClassName)) return false;
    ```

3. **형변환**
    - 올바른 타입으로 형변환한다.

    ```java
    YourClassName other = (YourClassName) o;
    ```

4. **핵심 필드 비교**
    - 객체의 핵심 필드가 모두 일치하는지 하나씩 검사한다. 반드시 해당 클래스의 모든 핵심 필드를 빠짐없이 비교해야 한다.
    - 모든 필드가 일치하면 `true`, 하나라도 다르면 `false`를 반환한다.

    ```java
    return this.field1.equals(other.field1) && this.field2.equals(other.field2);
    ```


### equlas 메서드 예시

```java
public final class PhoneNumber {
    private final short areaCode, prefix, lineNum;

    public PhoneNumber(int areaCode, int prefix, int lineNum) {
        this.areaCode = rangeCheck(areaCode, 999, "지역코드");
        this.prefix = rangeCheck(prefix, 999, "프리픽스");
        this.lineNum = rangeCheck(lineNum, 9999, "가입자 번호");
    }

    private static short rangeCheck(int val, int max, String arg) {
        if (val < 0 || val > max)
            throw new IllegalArgumentException(arg + ": " + val);
        return (short) val;
    }

    @Override
    public boolean equals(Object o) {
        if (o == this) return true; // 1. 자기 참조 확인
        if (!(o instanceof PhoneNumber)) return false; // 2. 타입 확인
        PhoneNumber pn = (PhoneNumber) o; // 3. 형변환
        return pn.lineNum == lineNum && pn.prefix == prefix && pn.areaCode == areaCode; // 4. 필드 비교
    }
    
    // 나머지 코드는 생략
}

```