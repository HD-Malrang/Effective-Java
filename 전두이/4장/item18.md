# 상속보다는 컴포지션을 사용하라

## 상속이란

상속은 자식 클래스가 부모 클래스의 메서드와 필드를 그대로 물려받아 사용할 수 있게 하는 강력한 도구이다.
하지만 이 과정에서 `캡슐화(Encapsulation)`가 깨질 수 있다.

- 캡슐화: 객체의 내부 구현을 외부에 숨기고, 필요한 부분만 노출하는 원리

상속을 사용하면 자식 클래스가 부모 클래스의 내부 구현에 의존하게 되어, 부모 클래스가 변경될 때 자식 클래스의 동작에도 예상치 못한 오류가 생길 수 있다.

### 상속으로 인한 의도치 않은 동작의 예

- `HashSet` 이라는 클래스의 기능을 확장해, 추가된 원소의 개수를 기록하는 `InstrumentedHashSet` 클래스
- `add` 메서드를 재정의해, 원소가 추가될 때마다 추가된 횟수를 세도록 함

```java
public class InstrumentedHashSet<E> extends HashSet<E> {
    private int addCount = 0;

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }

    public int getAddCount() {
        return addCount;
    }
}
```

- 이때 `addAll` 메서드를 호출하여 원소 3개를 추가했을 때, addCount의 값이 3을 반환할 것이라고 기대하겠지만, 실제로는 6이 반환되는 문제가 생긴다.
- 이는 `HashSet`의 `addAll` 메서드가 `add` 메서드를 호출해 구현되기 때문에 발생하는 문제이다. `addAll`이 각 원소를 `add`로 추가하면서 `addCount`가 중복으로 증가하게 된 것이다.

위의 예시를 바탕으로 상속의 문제점을 정리하자면 다음과 같다.

### 상속의 문제점

- **상위 클래스의 변경에 따른 위험**: 상위 클래스가 릴리스마다 내부 구현을 변경할 수 있고, 그 여파로 하위 클래스가 예상치 못한 동작을 할 수 있다.
- **상위 클래스의 메서드 추가**: 만약 상위 클래스에 새로운 메서드가 추가되면, 자식 클래스가 그 메서드를 재정의하지 못한 상태에서 의도치 않은 행동을 할 수 있다.
- **메서드 시그니처 충돌**: 다음 릴리스에서 상위 클래스에 하필 자식 클래스의 메서드와 같은 이름을 가진 메서드가 추가된다면 컴파일 오류가 발생하거나, 자식 클래스의 메서드가 상위 클래스의 새로운 규약을 충족하지 못하게 될 수 있다.

## 상속의 대안: Composition, forwarding

**컴포지션**은 기존 클래스를 새로운 클래스의 필드로 포함하는 방식으로, 새로운 기능을 추가하거나 기존 기능을 변경할 때 사용된다.

컴포지션은 내부 객체를 통해 원본 객체의 기능을 사용하며, 필요에 따라 추가 로직을 덧붙일 수 있다.

포워딩은 포함된 객체(컴포지션된 객체)에게 메서드 호출을 전달하는 방식이다. 새로운 클래스에서 메서드를 호출하면 내부에 포함된 객체가 해당 메서드를 실행하는 구조로, 상속보다 유연하게 기존 객체의 기능을 활용할 수 있다.

### 래퍼 클래스 (Wrapper Class)

- 컴포지션과 포워딩 방식을 결합하여 기존 클래스의 기능을 확장하는 방법

래퍼 클래스는 내부에 원본 객체를 필드로 포함하고, 원본 객체의 기능을 활용하면서도 추가적인 기능을 덧붙일 수 있다.  상속과 달리 기존 객체의 인터페이스에 영향을 미치지 않으므로, **안정적이고 유연하게 기능을 확장**할 수 있다.

예시: `ForwardingSet`과 `InstrumentedSet`이라는 래퍼 클래스를 사용해 기존 `Set` 인터페이스를 확장

여기서는 `Set`의 요소 추가 시, 총 추가된 요소 개수를 기록하는 기능을 덧붙였다.

```java
public class ForwardingSet<E> implements Set<E> {
    private final Set<E> s;

    public ForwardingSet(Set<E> s) {
        this.s = s;
    }

    @Override
    public boolean add(E e) {
        return s.add(e);
    }

    @Override
    public boolean remove(Object o) {
        return s.remove(o);
    }

    @Override
    public boolean contains(Object o) {
        return s.contains(o);
    }

    @Override
    public int size() {
        return s.size();
    }

    @Override
    public void clear() {
        s.clear();
    }

    // Set의 다른 메서드들도 같은 방식으로 구현...
}
```

```java
public class InstrumentedSet<E> extends ForwardingSet<E> {
    private int addCount = 0;

    public InstrumentedSet(Set<E> s) {
        super(s);
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e); // 원래 기능 호출
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c); // 원래 기능 호출
    }

    public int getAddCount() {
        return addCount;
    }
}
```

- `ForwardingSet` 클래스는 주어진 `Set`의 메서드를 전달하는 역할을 한다. 이 클래스는 다른 `Set` 구현체에 대해 유연하게 사용될 수 있다.

  → 유연하다 의미: `Set` 기능을 모두 유지하면서도 `addCount`라는 추가적인 기능을 제공할 수 있음

- `InstrumentedSet` 클래스는 `ForwardingSet`을 상속하여, `add`와 `addAll` 메서드를 오버라이드함으로써 요소가 추가될 때마다 카운트를 증가시킨다.

래퍼 클래스는 **기존 클래스의 기능을 수정하거나 추가할 때** 유용하다.

하지만 콜백 프레임워크를 함께 사용할 때는 주의해야 한다.

- 콜백 프레임워크: 특정 이벤트나 조건이 발생했을 때 미리 정의된 메서드를 호출하는 방식을 의미
- 이 패턴은  객체가 특정 작업을 완료한 후 다른 객체에 알려줄 수 있게 해준다.
  (예시: 버튼 클릭 이벤트, 비동기 작업 처리 등)

### SELF 문제

래퍼 클래스가 내부 객체를 감싸고 있고, 내부 객체가 콜백 메서드를 호출할 때 자신의 참조를 넘긴다면, 콜백 메서드에서 원본 객체의 기능만 호출하게 된다.

이로 인해 래퍼 클래스에서 추가한 기능이 무시되거나 작동하지 않을 수 있다.

---

## 결론

- 상속은 강력하지만, 상위 클래스에 대한 의존성 때문에 캡슐화를 해칠 수 있다. 또한, 상속은 순수한 is-a 관계일 때만 사용하는 것이 안전하다.
- 상속의 취약점을 피하려면, 상속 대신 컴포지션과 포워딩 방식을 사용하는 것이 좋다.
- **래퍼 클래스**는 상속보다 유연하며, 기존 객체의 기능을 수정하거나 추가하는 데 적합하다.
- **데코레이터 패턴**은 래퍼 클래스를 이용해 기존 객체에 동적으로 다양한 기능을 덧붙일 수 있는 패턴으로, 기능 조합을 유연하게 구성할 수 있다.