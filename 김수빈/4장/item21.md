## 인터페이스는 구현하는 쪽을 생각해 설계하라
 

### 기존 인터페이스에 디폴트 메서드 구현을 추가하는 것은 위험한 일이다.

1. 디폴트 메서드는 구현 클래스에 대해 아무것도 모른 채 합의 없이 무작정 '삽입' 될 뿐이다.

 

- 디폴트 메서드 : 인터페이스에 있는 구현 메서드 (메서드 앞에 default 예약어. 구현부 {} 가 있다.)

     - 기본 메서드는 이미 구현되어 있기 때문에 호출하여 사용할 수 있다.
```java
public interface MyInterface {
    // 추상 메서드
    void abstractMethod();

    // 기본 메서드
    default void defaultMethod() {
        // 기본 구현 코드
        System.out.println("This is a default method.");
    }
}
```
```java
public class MyClass implements MyInterface {
    @Override
    public void abstractMethod() {
        // 추상 메서드 구현 코드
        System.out.println("Abstract method implementation.");
    }
    
     public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.abstractMethod(); // 출력 : Abstract method implementation.
        obj.defaultMethod();  // 출력 : This is a default method.
    }
}
```
 

 

예시 ) Collection에 있는 removeIf 메서드

```java
// Collection.java
default boolean removeIf(Predicate<? super E> filter) {
    Objects.requireNonNull(filter);
    boolean removed = false;
    final Iterator<E> each = iterator();
    while (each.hasNext()) {
        if (filter.test(each.next())) {
            each.remove();
            removed = true;
        }
    }
    return removed;
}
```
```java
static class SynchronizedCollection<E> implements Collection<E>, Serializable {

    ...
    public int size() {
        synchronized (mutex) {return c.size();}
    }
    ...
}
```
-> removeIf는 동기화 처리가 되어있지 않지만 SyncronizedCollection 인스턴스에서 호출 가능.

-> 모르고 호출하면 동시성 관련 오류 발생할 수 있다. (ConcurrentModificationException)


```java
public class HaveToOverride implements Collection {

    // 위험한 default 메서드를 재정의해서, 사용하지 못하도록 한다. 
    @Override
    public boolean removeIf(Predicate filter) {
        throw new UnsupportedOperationException();
    }
}
 ```

 

2. 디폴트 메서드는 기존 구현체에 런타임 오류를 일으킬 수 있다.

- 자바의 메서드 접근 규칙에 따른 오류 발생 : 자바는 인터페이스보다 클래스가, 상속한 클래스가 우선순위를 가진다. 
```java
public class SuperClass {
    private void hello() {
        System.out.println("hello class");
    }
}

public interface MarkerInterface {

    default void hello() {
        System.out.println("hello interface");
    }
}
```
```java
public class SubClass extends SuperClass implements MarkerInterface{
    public static void main(String[] args) {
        SubClass subClass = new SubClass();
        subClass.hello();
    }
}
```
위에서 SubClass는 SuperClass를 상속했기 때문에 인터페이스의 메서드보다 클래스의 메서드로 먼저 접근한다.

그런데 접근하고보니 접근한 메서드는 private이었기 때문에 위와 같은 에러가 발생한다. 

<b>default 메서드가 추가되었고, 만약 구현체에서 사용하고 싶지 않다면 default 메서드를 오버라이딩 해서 사용하지 못하도록 한다. </b>
