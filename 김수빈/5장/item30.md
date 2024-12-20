## 이왕이면 제네릭 메서드로 만들라

### 제네릭 메서드
클래스와 마찬가지로 메서드도 제네릭으로 만들 수 있다.

매개변수화 타입을 받는 정적 유틸리티 메서드는 보통 제네릭이다. 

 

 

<b>ex) 두 집합의 합집합을 반환하는 코드(문제 발생)</b>
```java
public static Set union(Set s1, Set s2) {
    Set result = new HashSet<>();
    result.addAll(s2);
    return result;
}
```
- 위의 코드는 컴파일은 되지만 경고가 2개 발생한다.
- 경고를 없애려면 이 메서드를 타입 안전하게 만들어야 한다.
- 메서드 선언에서의 세 집합(입력 2개, 반환 1걔)의 원소타입을 타입 매개변수로 명시하고, 메서드 안에서도 이 타입 매개변수만 사용하게 수정하면 된다.
- (타입 매개변수들을 선언하는) 타입 매개변수 목록은 메서드의 제한자와 반환 타입 사이에 온다.
 

 

<b>ex) 제네릭 메서드로 변경한 코드</b>
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
- 이 코드는 경고없이 컴파일 되며, 타입 안전하고 사용하기도 쉽다.
- union 메서드의 집합 3개(입력 2개, 반환 1개)의 타입이 모두 같아야 한다.
- 한정적 와일드카드 타입을 사용하면 더 유연하게 개선할 수 있다. (아이템 31)

### 정리
- 제네릭 타입과 마찬가지로, 클라이언트에서 입력 매개변수와 반환값을 명시적으로 형변환해야 하는 메서드보다 제네릭 메서드가 더 안전하고 사용하기 쉽다.
- 타입과 마찬가지로, 메서드도 형변환 없이 사용할 수 있는 편이 좋으며 많은 경우 그렇게 하려면 제네릭 메서드가 되어야 한다.
- 형변환을 해줘야 하는 기존 메서드는 제네릭하게 만들어 주자.