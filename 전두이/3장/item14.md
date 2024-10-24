# Comparable을 구현할지 고려하라

### Comparable 인터페이스

- Object의 `equals`와 비슷하지만 단순 동치성 비교에 더해 순서까지 비교할 수 있으며 제네릭하다.
- 또한,  `equals`와 달리, 타입이 다른 객체를 신경 쓰지 않아도 된다. 타입이 다른 객체가 주어지면 간단히 `ClassCastException`을 던지면 된다.
- `Comparable` 인터페이스를 구현하면 **제네릭 알고리즘과 컬렉션**의 힘을 활용할 수 있으며, **효율적인 정렬**과 비교가 가능해진다. (자연적인 순서가 있음을 뜻한다)
- 자바 플랫폼 라이브러리의 대부분의 값 클래스와 열거형은 `Comparable`을 구현하고 있다.

### compareTo 메서드

- `compareTo` 메서드는 두 객체의 순서를 비교하여 다음과 같은 값을 반환
    - **음의 정수**: 현재 객체가 주어진 객체보다 작음
    - **0**: 두 객체가 같음
    - **양의 정수**: 현재 객체가 주어진 객체보다 큼
- 비교할 수 없는 타입의 객체가 주어지면 `ClassCastException`이 발생한다.

### 규약

1. **대칭성**: `sgn(x.compareTo(y))`는 `sgn(y.compareTo(x))`와 같아야 한다.
    - 두 객체의 참조의 순서를 바꿔도 동일한 결과가 나와야 한다.
2. **추이성**: `(x.compareTo(y) > 0 && y.compareTo(z) > 0)`이면 `x.compareTo(z) > 0`이어야 한다.
    - 첫 번째가 두 번째보다 크고 두 번째가 세 번째보다 크면, 첫 번째는 세 번째보다 커야 한다.
3. **일관성**: `x.compareTo(y) == 0`이면 `sgn(x.compareTo(z))`와 `sgn(y.compareTo(z))`는 같아야 한다.
    - `sgn()`: 부호 함수
4. **equals와의 일관성**: `(x.compareTo(y) == 0)`이면 `(x.equals(y))`여야 한다. 이를 지키지 않으면 해당 사실을 명시해야 한다.

### compareTo 메서드 구현

- `compareTo` 메서드는 각 필드의 **순서**를 비교해야 하며, 객체의 **참조 필드**를 비교할 때는 재귀적으로 호출할 수 있다.
- `Comparable`을 구현하지 않은 필드나 비표준 순서로 비교해야 하는 경우, `Comparator`를 사용한다.
- 핵심 필드가 여러 개인 경우, **가장 중요한 필드부터 차례로 비교**해야 하며, 순서가 결정되면 그 결과를 바로 반환한다.
- `<`와 `>`와 같은 연산자를 사용하지 말자. 이는 객체가 null일 경우 `NullPointerException`가 발생할 수 있다. 반면, `Integer.compare()`와 같은 메서드는 `null` 값을 안전하게 처리할 수 있다.

코드 예시

```java
private static final Comparator<PhoneNumber> COMPARATOR =
    comparingInt((PhoneNumber pn) -> pn.areaCode)
    .thenComparingInt(pn -> pn.prefix)
    .thenComparingInt(pn -> pn.lineNum);

public int compareTo(PhoneNumber pn) {
    return COMPARATOR.compare(this, pn);
}

```

- 위의 예시는 `PhoneNumber` 클래스의 `compareTo` 메서드 구현을 보여주며, `Comparator`를 사용하여 각 필드를 비교하는 방식을 나타낸다.

---

### 결론

- `Comparable` 인터페이스를 구현하여 객체의 순서를 정할 수 있으며, 이를 통해 정렬 및 비교 연산에서 일관성을 유지하는 것이 중요하다.
- `compareTo` 메서드의 구현은 효율적이고 일관된 객체 비교를 가능하게 하여, 데이터 구조와 알고리즘에서 큰 장점을 제공한다.