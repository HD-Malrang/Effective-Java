## 제네릭과 가변인수를 함께 쓸 때는 신중하라


### 가변인수 메서드

하나의 메서드가 가변적인 수의 매개변수를 받을 수 있도록 지원



가변인수(Varargs)를 사용하는 메서드를 호출하면 Java 컴파일러가 자동으로 배열을 생성

```java
public static void varargsExample(String... args) {
    // args는 배열로 처리됨
    System.out.println("Length: " + args.length);
}
```
호출시 아래처럼 컴파일

```java
varargsExample(new String[]{"A", "B", "C"});
```

### 이 배열은 내부로 감춰져야 하는데, 클라이언트에 공개되면서 문제가 발생할 수 있다.



가변인수 매개변수에 제네릭이나 매개변수화 타입이 포함되면 컴파일 경고 발생

warning: [unchecked] Possible heap pollution from

parameterized vararg type List<String>

<b>매개변수화 타입의 변수가 타입이 다른 객체를 참조하면 힙 오염이 발생한다.</b>



ex)
```java
static void dangerous(List<String>... stringLists) {
    List<Integer> intList = List.of(42);
    Object[] objects = stringLists;
    objects[0] = intList; // 힙 오염 발생
    String s = stringLists[0].get(0); // ClassCastException
}
```

마지막 줄에 컴파일로가 생성한 형변환이 숨어 있기 때문에 ClassCastException 발생.
```java
String s = (String)stringLists[0].get(0); // ClassCastException
```





-> 이처럼 타입 안전성이 깨지니 제네릭 가변인수 배열 매개변수에 값을 저장하는 것은 안전하지 않다.



### 제네릭 가변인수 메서드를 선언할 수 있게 한 이유?

실무에서 유용!

자바 라이브러리에서도 이런 메서드를 여럿 제공 (타입 안전)

ex. Arrays.asList(T... a), Collections.addAll(Collection<? super T> c, T... elements)



타입 안전한 메서드의 조건

1. 메서드가 제네릭 배열에 아무것도 저장하지 않는다.

2. 그 배열의 참조가 밖으로 노출되지 않는다.

-> 가변인수(Varargs)를 사용하는 메서드가 전달된 데이터를 단순히 읽고 사용만 할 경우에는 문제가 발생하지 않는다

(가변인수의 목적 : 메서드 호출 시 임의의 개수의 인수를 간편하게 전달)



### @SafeVarargs

- 메서드 작성자가 그 메서드가 타입 안전함을 보장하는 장치

컴파일러는 이 약속을 믿고 그 메서드가 안전하지 않을 수 있다는 경고를 더 이상 하지 않는다.

메서드가 안전한 게 확실하지 않다면 절대 @SafeVarargs 애너테이션을 달아서는 안 된다.



### 제네릭 가변인수 매개변수를 안전하게 사용하는 메서드
```java
@SafeVarargs
static <T> List<T> flatten(List<? extends T>... lists) {
    List<T> result = new ArrayList<>();
    for (List<? extends T> list : lists)
    result.addAll(list);
    return result;
}
```

flatten 메서드는 임의 개수의 리스트를 인수로 받아, 받은 순서대로 그 안의 모든 원소를 하나의 리스트로 옮겨 담아 반환한다.
이 메서드에는 @SafeVarargs 어노테이션이 달려있으니 선언하는 쪽과 사용하는 쪽 모두에서 경고를 발생시키지 않는다.
제네릭이나 매개변수화 타입의 varargs 매개변수를 받는 모든 메서드에 @SafeVarargs 어노테이션을 달자


### 제네릭 varargs 매개변수를 List로 대체한 예 - 타입 안전
```java
static <T> List<T> flatten(List<List<? extends T>> lists) {
    List<T> result = new ArrayList<>();
    for (List<? extends T> list : lists)
    result.addAll(list);
    return result;
}
```



### 정리

메서드에 제네릭 （혹은 매개변수화된） varargs 매개변수를 사용하고자 한다면, 먼저 그 메서드가 타입 안전한지 확인한 다음

@SafeVarargs 애너테이션을 달아 사용하는 테 불편함이 없게끔 하자.