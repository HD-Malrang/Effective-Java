# Comparable을 구현할지 고려하라
### Comparable 인터페이스란?
- Comparable 인터페이스는 객체를 정렬하는데 사용되는 메서드인 compareTo를 정의하고 있다.
- Comparable 인터페이스를 구현한 클래스는 반드시 compareTo를 정의해야 한다.
### Comparable 인터페이스 특징
- 자바에서 같은 타입의 인스턴스를 비교해야만 하는 클래스들은 모두 Comparable 인터페이스를 구현하고 있다.
- Boolean 타입을 제외한 래퍼 클래스와 알파벳, 연대같이 순서가 명확한 클래스들은 모두 정렬이 가능하다.
- 기본 정렬 순서는 작은 값에서 큰 값으로 정렬되는 오름차순이다.
### Comparable 인터페이스의 compareTo 메서드
```
compareTo 는 해당 객체와 전달된 객체의 순서를 비교한다.
```
- compareTo는 Object의 euqals와 두가지 차이점이 있다. compareTo는 equals와 달리 단순 동치성에 더해 순서까지 비교할 수 있으며, 제네릭하다.

- Comparable을 구현했다는 것은 그 클래스의 인스턴스에 자연적인 순서가 있음을 뜻한다. <br>예를 들어, Comparable을 구현한 객체들의 배열은 Arrays.sort(a)로 쉽게 정렬이 가능하다.
### equals와 compareTo의 차이
```java
...
		final BigDecimal bigDecimal1 = new BigDecimal("1.0");
        final BigDecimal bigDecimal2 = new BigDecimal("1.00");

        final HashSet<BigDecimal> hashSet = new HashSet<>();
        hashSet.add(bigDecimal1);
        hashSet.add(bigDecimal2);

        System.out.println(hashSet.size());

        final TreeSet<BigDecimal> treeSet = new TreeSet<>();
        treeSet.add(bigDecimal1);
        treeSet.add(bigDecimal2);

        System.out.println(treeSet.size());
...
// 실행결과 
hashSet: 2
treeSet: 1
```
HashSet과 TreeSet은 서로 다른 메서드로 객체의 동치성을 비교한다.<br>
HashSet은 equals를 기반으로 비교하기 때문에 추가된 두 BigDeciaml이 다른값으로 인식되어 크기가 2가 된다.<br>
반면에, TreeSet은 compareTo를 기반으로 객체에 대한 동치성을 비교하기 때문에 같은값으로 인식되어 compareTo가 0을 반환하기 때문에 크기가 1이 된다.

## 정리
순서를 고려해야하는 값 클래스를 작성한다면 꼭 Comparable 인터페이스를 구현하여 그 인스턴스들을 쉽게 정렬하고, 검색하고, 비교 기능을 제공하는 컬렉션과 어우러지도록 해야한다.<br>
compareTo 메서드에서 필드의 값을 비교할 때 <와> 연산자는 지양해야 한다. 그 대신 박싱된 기본 타입 클래스가 제공하는 정적 compare 메서드나 Comparator가 제공하는 비교자 생성 메서드를 이용하자.