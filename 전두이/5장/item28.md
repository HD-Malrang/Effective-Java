# 배열보다는 리스트를 사용하라

배열과 제네릭 타입의 차이점 2가지

## 1. 공변(Covariant) / 불공변(Invariant)

공변성(Covariance)이란

**타입의 상속 관계에서 상위 타입 변수에 하위 타입 객체를 할당할 수 있는 특성을 뜻한다.**

→ 즉, 자식 클래스의 객체는 부모 클래스의 타입 변수에 할당될 수 있을 때, 공변성을 가진다고 말한다.

### 배열은 공변적이다

```java
Object[] objectArray = new Long[1];  // 정상
objectArray[0] = "String";           // 런타임 오류 (ArrayStoreException)
```

상위 타입 Object[] 배열에 하위 클래스인 Long[] 배열을 할당할 수 있다.

하지만, objectArray에 문자열 데이터를 넣으려고 하면  `ArrayStoreException`이 **런타임 시점에 발생**한다.

### 제네릭은 불공변이다 (Invariant)

제네릭 타입은 부모 타입과 자식 타입을 바꿀 수 없다는 의미이다.

```java
List<Object> ol = new ArrayList<Long>（）; // 호환되지 않는 타입이다.
ol.add（"String"）;                        // 컴파일 오류! 
```

List<Object>와 List<Long>은 호환되지 않기 때문에, **컴파일 시점에 타입 오류가 발생**한다.

### 런타임 에러와 컴파일 에러

- `런타임 에러`: 프로그램 실행 시점에 발생 (코드의 논리적 오류)

  예시: null 참조, 배열의 인덱스를 잘못 참고, 잘못된 타입의 객체를 처리하려고 할 때

- `컴파일 에러`: 소스 코드가 컴파일될 때 발생

  예시: 문법 오류, 변수나 메서드의 호출의 오류, 컴파일러가 코드를 분석하면서 발견할 수 있는 오류들


컴파일 오류는 개발 시점에 미리 발견할 수 있어서 빠르게 수정할 수 있지만, 런타임 오류는 실행 중에 발생하므로 더 큰 문제를 일으킬 수 있다.

컴파일 시 오류를 줄이는 것이 더 안전하고 효율적인 개발을 가능하게 한다.

---

## 2. 실체화(reify) / 소거(erasure)

### 실체화(Reification)

- 타입 정보가 런타임까지 유지되는 것을 의미한다.

배열은 실체화된 타입이다. 즉, 배열은 런타임에서 타입 정보를 유지한다. 예를 들어

```java
String[] strings = new String[10];
Object[] objects = strings;
objects[0] = 1;  // 런타임 오류 발생
```

위 코드에서 `objects`는 `Object[]`로 생성되었지만, 실제 배열 타입은 `String[]` 이다. 이로 인해 런타임 검사에서 정수 타입 1을 저장하려고 할 때 `ArrayStoreException`이 발생한다.

이처럼 배열은 타입이 일치하지 않을 경우 런타임 에러를 발생시킨다.

### 타입 소거(Type erasure)

- 제네릭 타입의 정보가 컴파일 시점에서 제거되어 런타임 시점에서는 `원시 타입(raw type)`으로 처리되는 Java의 제네릭 처리 방식이다.
- 제네릭을 도입하기 전에, Java는 원시 타입만을 사용했으므로, 제네릭을 사용하더라도 이전 코드와의 호환성을 유지하기 위해 필요하다.

제네릭은 컴파일 시점에만 타입 검사를 수행하고, 런타임에는 타입 정보가 존재하지 않는다.

```java
// 컴파일 시에는 타입이 다르지만, 런타임에서는 둘 다 List로 취급된다.
List<String> list1 = new ArrayList<>();
List<Integer> list2 = new ArrayList<>();
```

위에서 `List<String>`은 컴파일 시점에는 `List<String>`으로 다루어지지만, 런타임에서는 그냥 `List`로 취급된다.

그래서 `List<String>` 와 `List<Integer>`는 컴파일 시점에는 서로 다른 타입이지만, 런타임에서는 모두 `List`로 처리된다.

### 제네릭 배열을 만들 수 없는 이유

이와 같이 제네릭은 타입 소거가 적용되기 때문에 런타임 시점에는 타입을 구별할 수 없다.

반면, 배열은 런타입 시점에 타입 정보를 검사하므로 만약 제네릭 배열을 허용하게 되면 배열의 런타임 타입 검사와 제네릭의 타입 소거가 충돌하여 타입 안전성이 깨질 수 있기 때문에, 컴파일 시점에 에러가 발생한다.

원본 코드(제네릭 타입 배열 생성 문제) → 컴파일 에러

```java
public class Chooser<T> {
    private final T[] choiceArray;

    public Chooser(Collection<T> choices) {
        choiceArray = choices.toArray(); // 컴파일 에러
    }
}
```

- `choices.toArray()` 의 반환 타입은 Object[] 이다. 따라서 T[]에 직접 할당하려고 할 때 타입 불일치 오류가 발생한다.

수정된 코드(배열을 List<T>로 변경) → 타입 안전성 확보

```java
public class Chooser<T> {
    private final List<T> choiceList;

    public Chooser(Collection<T> choices) {
        choiceList = new ArrayList<>(choices);
    }

    public T choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceList.get(rnd.nextInt(choiceList.size()));
    }
}
```

- 배열 대신 List<T>를 사용하면 제네릭 타입의 요소들을 유연하게 저장할 수 있게 된다.
- `new ArrayList<>(choices)`를 사용하면 `Collection`의 `choices`를 타입 안전하게 리스트로 복사할 수 있다.

---

## 결론

- 배열은 공변이고 실체화 되는 반면, 제네릭은 불공변이고 타입 정보가 소거된다.
- 이로 인해 배열은 타입 안전성을 보장해줄 수 없어 제네릭 배열을 생성할 수 없다.
- 만약 이 둘을 섞어 쓰다가 오류를 만나면, 가장 먼저 배열을 리스트로 대체하는 방법을 적용해보자.