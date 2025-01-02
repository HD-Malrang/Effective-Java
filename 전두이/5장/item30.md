# 이왕이면 제네릭 메서드로 만들라

### **왜 제네릭 메서드를 사용하는 것이 좋은가?**

- **타입 안정성 보장:** 입력과 출력 타입을 컴파일 시점에 명확히 지정하므로 **런타임 오류**를 방지할 수 있다.
- **형변환 필요 없음:** 제네릭 메서드는 **자동으로 타입이 지정되므로** 클라이언트가 `형변환(casting)`을 하지 않아도 된다.
- **가독성과 유지보수성 향상:** 코드에서 타입이 명확히 드러나므로 **가독성이 좋고**, 잘못된 타입 사용을 컴파일 시점에 잡아준다.

### 제네릭 활용 예시

**두 집합의 합집합을 반환하는 메서드**

```java
public static Set union(Set s1, Set s2) {
    Set result = new HashSet<>();
    result.addAll(s2);
    return result;
}
```

**제네릭 메서드로 변경한 코드**

```java
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}

public static void main(String[] args) {
    Set<String> guys = Set.of("톰", "딕", "해리");
    Set<String> stooges = Set.of("래리", "모에", "컬리");
    Set<String> aflCio = union(guys, stooges);
    System.out.println(aflCio);
}
```

- 기존 union 메서드에서 제네릭으로 변경함으로써 이는 타입 안전하고 직접 형변환 하지 않아도 경고 없이 컴파일 된다.

---

## 결론

- 제네릭 타입처럼, 제네릭 메서드도 형변환 없이 사용할 수 있어 더 안전하고 편리하다.
- 새로운 메서드를 설계할 때는 형변환 없이 사용할 수 있도록 제네릭 메서드로 구현하라.
- 기존 메서드도 제네릭하게 개선하면, 기존 코드는 유지하면서 새로운 사용자를 더 편리하게 지원 가능하다.