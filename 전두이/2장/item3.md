# private 생성자나 열거 타입으로 싱글턴임을 보증하라

## 싱글턴(singleton)

- 인스턴스를 오직 하나만 생성할 수 있는 클래스
- 함수와 같은 무상태 객체나 설계상 유일해야 하는 시스템 컴포넌트를 예로 들 수 있다.
- 클래스를 싱글턴으로 만들면, 이를 사용하는 클라이언트를 테스트할 때 어려울 수 있다.
- 타입을 인터페이스로 정의한 다음 그 인터페이스를 구현해서 만든 싱글턴이 아니라면 싱글턴 인스턴스를 가짜(mock) 구현으로 대체할 수 없기 때문이다.

## public static final 필드 방식의 싱글턴 (+private 생성자)

```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    
    public void leaveTheBuilding() { ... } 

}
```



이는 구성이 간결하고, 해당 클래스가 싱글턴임이 API에 명백히 드러난다.
private 생성자는 인스턴스를 초기화할 때 딱 한 번만 호출된다. 

public 이나 protected 생성자가 없으므로 Elvis 클래스가 초기화될 때 만들어진 인스턴스가 전체 시스템에서 하나뿐임이 보장된다.

### 단점

- 권한이 있는 클라이언트는 리플렉션 API인 `AccessibleObject`. `setAccessible` 을 사용해 private 생성자를 호출할 수 있다. 이러한 공격을 방어하려면 생성자를 수정하여 두 번 째 객체가 생성되려 할 때 예외를 던지게 하면 된다.
- 역직렬화 할 때 새로운 인스턴스가 생길 수 있다.

## 정적 팩터리 방식의 싱글턴 (+private 생성자)

```java
public class Elvis {

    private static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    public static Elvis getlnstanceO { return INSTANCE; }
	
    public void leaveTheBuilding() { ... } 
}
```

- API를 변경하지 않고 싱글턴이 아니게 변경할 수 있다.
- 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다.
- 정적 팩터리의 메서드 참조를 공급자(Supplier)로 사용할 수 있다

단점은 위의 public 방식과 동일함

## 열거 타입 방식의 싱글턴

```java
public enum Elvis {
    INSTANCE;
    public void leaveTheBuilding() { ... }
}
```

- 동기화 문제, 클래스 로딩 문제, 리플랙션, 직렬화 역직렬화 문제 등 해결 가능
- 대부분의 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.
- 단, 만들려는 싱글턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없다.