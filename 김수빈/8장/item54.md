## null이 아닌, 빈 컬렉션이나 배열을 반환하라



- 따라하면 안 되는 코드 (컬렉션이 비었으면 null을 반환)
```java
private final List<Cheese> cheesesInStock = ...;
/**
* @return 매장 안의 모든 치즈 목록을 반환한다.
* 단, 재고가 하나도 없다면 null을 반환한다.
  */
  public List<Cheese> getCheeses() {
  return cheesesInStock.isEmpty() ? null
  : new ArrayList<>(cheesesInStock);
  }
```



-- null을 반환하게 되면 클라이언트는 항상 방어 코드를 넣어줘야함, 방어코드를 빼먹으면 오류가 발생할 수 있

getCheeses()를 사용하는 클라이언트는 null 확인을 추가로 해야한다.
```java
if (cheeses != null && cheeses.contains(Cheese.STILTON)) {
... }
```



### null 대신 빈 컬렉션 할당시 비용.

1. 빈 컬렉션을 반환해도 사실상 신경 쓸 만한 성능 차이가 나지 않는다.

2. 빈 컬렉션과 배열은 새로 할당하지 않고도 반환할 수 있다.

- 빈 불변 컬렉션 반환 : Collections.emptyList(), Collections.emptySet(), Collections.emptyMap()

: 최적화가 필요할 때만 사용.



### 빈 컬렉션을 반환하는 올바른 예
```java
public List<Cheese> getCheeses() {
    return new ArrayList<>(cheesesInStock);
}
```



### 빈 배열을 반환하는 올바른 예
```java
public Cheese[] getCheeses() {
    return cheesesInStock.toArray(new Cheese[0]);
}
```



- 최적화
```java
private static final Cheese[] EMPTY_CHEESE_ARRAY = new Cheese[0];

public Cheese[] getCheeses() {
    return cheesesInStock.toArray(EMPTY_CHEESE_ARRAY);
}
```





### 핵심 정리

null이 아닌, 빈 배열이나 컬렉션을 반환하라. null을 반환하는 API는 사용하기 어렵고 오류 처리 코드도 늘어난다.

그렇다고 성능이 좋은 것도 아니다.