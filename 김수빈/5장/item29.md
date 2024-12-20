## 이왕이면 제네릭 타입으로 만들라

 

클래스 안에 다른 객체들을 담는 역할을 하는 클래스들은 <code>제네릭 타입</code>으로 만들면 유용하다.

ex) stack

- 제네릭을 사용할때 장점

  - 클라이언트 코드에서 형 변환 을 사용하지 않도록 만들 수 있다.

  - 또한 형 변환 을 잘못 사용했을 때 발생할 수 있는 <code>ClassCastException</code> 을 미연에 방지 할 수 있다.
```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null; // 다 쓴 참조 해제
        return result;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
 ```

### 제네릭 타입으로 변경하는 두 가지 방법

1. 배열을 만들때 형 변환을 1번만 하는 방법

- Stack<E> 로 제네릭 타입을 선언한 뒤 E[] 제네릭 타입의 배열을 사용

- 제네릭 배열을 만들 수 없기 때문에 Object 배열을 만든 뒤 E[] 제네릭 타입의 배열로 형 변환 을 해줘야한다.

- 런 타임 에는 E[] 부분이 소거 되기 때문에 결국 Object 타입의 배열로 동작하게 된다.
- 배열의 런타임 타입( Object )이 컴파일타입( E ) 과 달라 <code>힙 오염</code>을 일으키는 단점
```java
public class Stack<E> {
    private E[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    // 배열 elements는 push(E)로 넘어온 E 인스턴스만 담는다.
    // 따라서 타입 안전성을 보장하지만,
    // 이 배열의 런타임 타입은 E[]가 아닌 Object[]다!
    @SuppressWarnings("unchecked")
    public Stack() {
        elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(E e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public E pop() {
        if (size == 0)
            throw new EmptyStackException();
        E result = elements[--size];
        elements[size] = null; // 다 쓴 참조 해제
        return result;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
 ```

 

2. 힙 오염이 발생하지 않는 방법

- 제네릭 배열(E[]) 대신 Object 배열을 사용

- 대신 담아뒀던 객체를 꺼낼 때 제네릭 타입으로 형 변환 을 해야한다.
```java
public class Stack<E> {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(E e) {
        ensureCapacity();
        elements[size++] = e;
    }

    // 비검사 경고를 적절히 숨긴다.
    public E pop() {
        if (size == 0)
            throw new EmptyStackException();

        // push에서 E 타입만 허용하므로 이 형변환은 안전하다.
        @SuppressWarnings("unchecked") E result = (E) elements[--size];

        elements[size] = null; // 다 쓴 참조 해제
        return result;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
 ```

### 힙오염

힙 메모리의 데이터 <code>타입 안전성(type safety)</code>이 손상되는 상황

힙 메모리에 저장된 객체의 실제 타입과 컴파일러가 예상하는 타입이 불일치하게 된다.

이는 보통 제네릭 타입의 불변성을 위반하거나, raw type(비제네릭 타입)을 사용할 때 발생하며, 런타임 시에 ClassCastException과 같은 오류를 일으킬 가능성을 높임.

힙 오염으로 인해 힙 메모리의 타입 정보와 실제 저장된 객체 타입이 불일치하면:

 - 프로그램의 타입 안정성이 손상,
- 런타임 예외(ClassCastException)가 발생,
- JVM의 메모리 관리와 최적화에 부정적인 영향을 줄 수 있음
 

### 정리

클라이언트에서 직접 형변환해야 하는 타입보다 제네릭 타입이 더 안전하고 쓰기 편하다.

그러니 새로운 타입을 설계할 때는 형변환 없이도 사용할 수 있도록 하라.

그렇게 하려면 제네릭 타입으로 만들어야 할 경우가 많다.

기존 타입 중 제네릭이었어야 하는 게있다면 제네릭 타입으로 변경하자. 


