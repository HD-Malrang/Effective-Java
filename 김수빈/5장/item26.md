## 로 타입은 사용하지 말라

 

### 로 타입 : 제네릭 타입에서 타입 매개변수를 전혀 사용하지 않은 것

ex) List<E>의 로 타입 : List

- 쓰면 안 되는 이유 : 오류를 컴파일할 때가 아닌 런타임시 발견할 수 있다.

ex) 실수도 Stamp 대신 Coin을 넣어도 아무 오류 없이 컴파일되고 실행됨.
```java
// 제네릭을 지원하기 전엔 컬렉션을 아래와 같이 선언했다.
private final Collection stamps = ...;

stamps.add(new Coin(...));

for (Iterator it = stamps.iterator(); i.hasNext();) {
  Stamp stamp = (Stamp) i.next(); // ClassCastException
  stamp.cancel();
}
```
```java
// 타입 안전성 확보!
private final Collection<Stamp> stamps = ...;
 ```
 

- 로 타입이 있는 이유 : 제네릭이 도입되기 이전 코드와의 호환성을 위해 제공.

 

### List<Object\>

- 로 타입인 List와 매개변수화 타입인 List<Object\>의 차이

  - List<Object\>는 모든 타입을 허용한다는 의사를 컴파일러에 명확히 전달.

  - 매개변수로 List를 받는 메서드에 List<String\>을 넘길 수 있지만, List<Object\>를 받는 메서드에는 넘길 수 없다.

-> List<Object\> 같은 매개변수화 타입을 사용할 때와 달리 List 같은 로 타입을 사용하면 타입 안전성을 잃게 된다.

 

### 비한정적 와일드카드 타입

- Set<?>

- 와일드카드 타입은 안전하고, 로 타입은 안전하지 않다.

  - 비 한정적 와일드타입에는 null 이외의 어떤 원소도 넣을수도(add) 없고, 가져와도(get) 타입을 알 수 없는 제약이 있다.

  - Object에 정의되어 있는 기능만 사용 가능.  equals(), toString(), hashCode()…

  - 이 제약에서 벗어나고 싶다면 ? extends 클래스와 같이 한정적 와일드카드 타입을 이용해 어떤 클래스의 하위 클래스를 받을 것인지 명시하면 된다.

 

 

### 로 타입을 써야 할 때

1.  class 리터럴

  - 자바 명세는 class 리터럴에 매개변수화 타입을 사용하지 못하게 했다. (배열과 기본 타입은 허용)

  - List.class, String[].class, int.class는 허용하고, List<String\>.class와 List<?>.class는 허용하지 않는다.

2.  instanceof 연산자

  - 런타임에는 제네릭 타입 정보가 지워지므로 instanceof 연산자는 비한정적 와일드카드 타입 이외의 매개변수화 타입에는 적용할 수 없다.

  - 그리고 로 타입이든 비한정적 와일드카드타입 이든 instanceof는 완전히 똑같이 동작한다. 
```java
if (o instanceof Set) {  // 로 타입
  Set<?> s = (Set<?>) o; // 와일드카드 타입
  ...
}
```
