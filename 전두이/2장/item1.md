# 생성자 대신 정적 팩터리 메서드를 고려하라

Java에서 클래스의 인스턴스를 생성하는 방법은 크게 2가지가 있다.

(1) public 생성자를 사용하는 방법과

(2) 정적 팩터리 메서드를 사용하는 방법

## public 생성자를 사용한 인스턴스 생성

### 특징

- 직접적인 객체 생성: ‘new’ 연산자를 사용해서 클래스의 인스턴스를 직접 생성
- 생성자를 호출할 때마다 새로운 객체가 생성된다.
- 동일한 매개변수로 생성자를 호출하더라도 항상 새로운 인스턴스가 반환된다.

### 장점

- 장점은 일단 직관적이고 간단합니다. 가장 일반적이고 이해하기 쉬움

### 단점

- **객체 재사용 불가**: 생성자를 호출할 때마다 새로운 객체를 생성하므로, 동일한 객체가 반복적으로 생성될 수 있습니다. 이로 인해 메모리와 성능의 안 좋을 수 있다.
- **이름을 가질 수 없음**: 기본 public 생성자는 항상 클래스와 동일한 이름을 가져야 하는 규칙이 있습니다. 이는 생성 과정, 즉 생성자의 목적을 구체적으로 설명할 수 없게 된다.
- **복잡한 객체 생성**: 생성자만을 사용해서 객체를 생성하면, 매개변수 수가 많아질수록 이해하기 어려운 코드가 될 가능성이 있음

---

## 정적 팩터리 메서드를 사용한 인스턴스 생성

### 정적 팩터리 메서드란?

클래스의 ‘static’ 메서드로 정의되며, 객체를 생성하고 반환하는 역할을 합니다. ‘new’ 키워드를 직접적으로 사용하지 않고, 메서드를 호출하여 객체를 생성한다.

아래 코드는 책에서 소개한 정적 팩터리 메서드의 예시

```java
public static Boolean valueOf(boolean b) { 
    return b ? Boolean.TRUE : Boolean.FALSE;
}
```

- **boolean** 기본 타입을 객체로 감싸기 위해 사용되는 래퍼 클래스 **Boolean**로 변환하는 `valueOf` 메서드이다.  boolean 기본 타입의 값을 받아, 이에 대응되는 Boolean 객체를 반환한다.
- 예를 들어, `boolean` 값을 메서드에 전달하면, 해당 값에 맞는 `Boolean` 객체(`Boolean.TRUE` 또는 `Boolean.FALSE`)를 반환한다.
- `valueOf` 메서드는 `new Boolean(b)`와 같은 방식으로 매번 새로운 객체를 생성하지 않고, 이미 존재하는 `Boolean.TRUE` 또는 `Boolean.FALSE` 객체를 반환합니다. 이렇게 하면 메모리 사용을 절약할 수 있고, 성능도 향상된다.
- `Boolean.TRUE`와 `Boolean.FALSE`는 `Boolean` 클래스에서 미리 정의된 상수이므로, 필요할 때마다 새로운 객체를 생성하지 않고, 기존 객체를 재사용할 수 있다.

### 특징

- 객체 재사용 가능: 정적 팩터리 메서드는 기존에 생성된 객체를 재사용할 수 있다. 이를 통해 메모리 사용을 최적화할 수 있음
- 반환 타입의 유연함: 정적 팩터리 메서드는 반환 타입을 유연하게 지정할 수 있다.
- 인터페이스 타입을 반환하면서 실제로는 구현체의 다양한 클래스 중 하나를 반환할 수 있다.

### 장점

> 1. 이름을 가질 수 있다.
>

정적 팩터리 메서드는 의미 있는 이름을 가질 수 있다. 이는 메서드의 기능이나 반환하는 객체의 특성을 명확히 설명할 수 있다.

```java
LocalDate today = LocalDate.now();
System.out.println(today);  // 출력 예: 2024-09-01 (오늘 날짜)

LocalDate today = new LocalDate(2024, 9, 1)
System.out.println(today);  // 출력 예: 2024-09-01
```

- 메서드 이름 now는 메서드가 반환하는 객체가 “현재 날짜”임을 명확하게 전달한다. 이 이름을 통해 현재 날자를 얻는 용도로 사용된다는 것을 쉽게 이해할 수 있게 됨
- 기본 new 생정자를 이용한 방법은 직접 날짜 값을 지정하여 인스턴스를 생성하기 때문에, 생성된 날자가 현재인지 과거인지 알 수 없다.
- 즉, 기본 new 생성자 이름 만으로는 생성된 날짜의 의미를 정확히 알 수 없음

> 2. 호출될 때마다 인스턴스를 새로 생성하지는 않아도 된다.
>

```java
List<String> emptyList1 = Collections.emptyList();
List<String> emptyList2 = Collections.emptyList();
```

- `emptyList()` 메서드는 비어 있는 리스트를 반환하는데, 이 메서드는 항상 동일한 빈 리스트 객체를 반환한다. 즉, `emptyList1`과 `emptyList2`는 같은 객체를 참조하게 된다. 이를 통해 불필요한 객체 생성을 방지할 수 있음
- **불변 클래스(immutable class)**는 인스턴스를 캐싱하여 재활용하는 식으로 불필요한 객체 생성을 피할 수 있다.

> 3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
>

정적 팩터리 메서드는 반환 타입의 하위 타입 객체를 반환할 수 있는 유연성을 제공한다.

이는 메서드의 구현 세부 사항을 숨기고, 클라이언트 코드가 의존해야 하는 인터페이스나 추상 클래스의 타입을 유지하면서도 다양한 하위 타입을 반환할 수 있게 해줌

> 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
>

정적 팩터리 메서드는 입력 매개변수에 따라 다양한 클래스의 객체를 반환할 수 있다. 이는 생성 로직을 다양화하고, 클라이언트 코드가 객체의 구체적인 클래스에 의존하지 않게 해줌

```java
// 패턴에 따라 다양한 포맷터를 생성
DateTimeFormatter f1 = DateTimeFormatter.ofPattern("yyyy-MM-dd");
DateTimeFormatter f2 = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm:ss");
 DateTimeFormatter f3 = DateTimeFormatter.ofPattern("E, MMM d yyyy");

// 날짜와 시간 포맷 적용
LocalDateTime now = LocalDateTime.now();

System.out.println("Formatter 1: " + now.format(f1));  // 출력 예: 2024-09-01
System.out.println("Formatter 2: " + now.format(f2));  // 출력 예: 01/09/2024 15:30:00
System.out.println("Formatter 3: " + now.format(f3));  // 출력 예: Sun, Sep 1 2024
```

- `DateTimeFormatter.ofPattern(String pattern)` 메서드는 제공된 문자열 패턴에 따라 `DateTimeFormatter` 객체를 생성한다.
- 각 패턴은 날짜와 시간을 포맷하는 방식이 다르기 때문에, 입력된 패턴에 따라 서로 다른 `DateTimeFormatter` 객체가 반환된다.
- **다양한 포맷 제공**: 사용자가 제공하는 패턴 문자열에 따라 다양한 형식의 `DateTimeFormatter` 객체를 생성할 수 있다. 예를 들어, `"yyyy-MM-dd"`는 날짜를 `년-월-일` 형식으로 포맷하고, `"dd/MM/yyyy HH:mm:ss"`는 날짜와 시간을 `일/월/년 시:분:초` 형식으로 포맷한다.
- **클라이언트 코드의 유연성**: 클라이언트 코드는 `ofPattern` 메서드를 호출하여 원하는 포맷의 `DateTimeFormatter`를 생성할 수 있으며, 다양한 날짜와 시간 형식을 처리할 수 있다. 메서드는 입력 패턴에 따라 적절한 포맷터 객체를 반환하므로, 날짜와 시간의 형식을 유연하게 조정할 수 있음
- 이는 정적 팩터리 메서드가 객체 생성을 얼마나 유연하게 처리할 수 있는지 보여준다!

> 5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.
>

이 장점은 정적 팩터리 메서드의 유연성을 활용하여 서비스 제공자 프레임워크를 구현하는 방법을 설명하고 있다.

- `JDBC (Java Database Connectivity)`는 이러한 서비스 제공자 프레임워크의 대표적인 예시로, 다양한 데이터베이스 드라이버를 동적으로 로드하고 연결을 관리하는 방식으로 이를 보여줌
- 클라이언트는 `DriverManager.getConnection` 메서드를 통해 데이터베이스 연결을 요청하며, 실제로는 다양한 드라이버 구현체 중에서 적절한 드라이버가 런타임에 동적으로 선택된다.
- 이 접근 방식은 컴파일 시점에 구체적인 클래스가 존재하지 않아도, 런타임에 필요한 객체를 유연하게 생성하고 제공할 수 있게 해준다.

---

### 단점

> 1. 하위 클래스를 만들 수 없다.
>

정적 팩터리 메서드는 클래스의 인스턴스를 생성하는 방법을 캡슐화할 수 있지만, 해당 메서드가 제공하는 객체가 구체적인 구현을 통해 반환되는 경우, 상속을 통해 하위 클래스를 만들기 어렵다. 생성자를 사용하면 하위 클래스를 쉽게 만들 수 있지만, 정적 팩터리 메서드만 사용하면 하위 클래스의 인스턴스를 반환할 수 없다.

```java
// ArrayList의 정적 팩터리 메서드를 사용해 List 생성
List<String> list = ArrayList.of("A", "B", "C");
```

- `ArrayList.of(...)`는 정적 팩터리 메서드를 사용하여 `List` 객체를 생성함
- 하지만 이 방법은 `ArrayList`의 인스턴스만을 반환하며, `List` 인터페이스를 구현하는 다른 하위 클래스를 반환할 수 없다.
- 상속을 통해 새로운 `List` 구현체를 만들고 싶다면, `ArrayList`의 생성자나 인터페이스를 직접 구현해야된다.

> 2. 프로그래머가 찾기 어렵다.
>

정적 팩터리 메서드는 생성자와 달리 클래스의 인스턴스를 생성하는 메서드가 어떤 역할을 하는지 코드베이스를 탐색해야 알 수 있다. 문서화가 부족하거나 명명 규칙이 불명확한 경우, 프로그래머가 적절한 정적 팩터리 메서드를 찾기 어려울 수 있다.

- **정적 팩터리 메서드 명명 방식**

| **명칭** | **설명** | **예시** |
| --- | --- | --- |
| **from** | 매개변수를 하나 받아 해당 타입의 인스턴스를 반환하는 형 변환 메서드 | `Date d = Date.from(instant);` |
| **of** | 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드 | `EnumSet<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);` |
| **valueOf** | `from`과 `of`의 더 자세한 버전으로, 매개변수로부터 인스턴스를 생성하는 메서드 | `BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);` |
| **instance** | (매개변수를 받는다면) 매개변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지 않음 | `StackWalker luke = StackWalker.getInstance(options);` |
| **getInstance** | `instance`와 같지만, 좀 더 일반적인 명칭. 매개변수로 명시한 인스턴스를 반환하며, 같은 인스턴스임을 보장하지 않음 | `StackWalker luke = StackWalker.getInstance(options);` |
| **create** | `instance` 혹은 `getInstance`와 같지만, 매번 새로운 인스턴스를 생성해 반환함을 보장함 | `Object newArray = Array.newInstance(classObject, arrayLen);` |
| **newInstance** | `create`와 같지만, 생성할 클래스의 이름을 명시적으로 사용하여 새 인스턴스를 생성함을 보장함 | `BufferedReader br = Files.newBufferedReader(path);` |
| **getType** | 생성할 클래스가 아닌 다른 클래스에서 팩토리 메서드를 정의할 때 사용하며, "Type"은 반환할 객체의 타입임을 나타냄 | `FileStore fs = Files.getFileStore(path);` |
| **newType** | `newInstance`와 같지만, 생성할 클래스의 이름이 아닌 다른 클래스에서 정의될 때 사용하며, "Type"은 반환할 객체의 타입임을 나타냄 | `BufferedReader br = Files.newBufferedReader(path);` |
| **list** | "Type"과 "newType"의 간결한 버전으로, 다른 클래스로부터 목록을 변환함 | `List<Complaint> litany = Collections.list(legacyLitany);` |