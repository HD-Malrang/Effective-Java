# ITEM 3. private 생성자나 열거 타입으로 싱글턴임을 보증하라

> **대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.**
- [싱글턴(singleton)](#싱글턴singleton)
- [싱글턴을 만드는 방식](#싱글턴을-만드는-방식)
  - [1. public static 멤버가 final 필드인 방식](#1-public-static--final--)
  - [2. 정적 팩터리 메서드를 public static 멤버로 제공하는 방식](#2----public-static---)
  - [위 두 방식의 문제점](#위-두-방식의-문제점)
  - [3. 원소가 하나인 열거 타입을 선언하는 방식](#3------)

<br/>

## 싱글턴(singleton)
: 인스턴스를 오직 하나만 생성할 수 있는 클래스

- 장점
  - 한번의 객체 생성으로 재 사용이 가능하기 때문에 메모리 낭비를 방지할 수 있다.
  - 싱글톤으로 생성된 객체는 무조건 한번 생성으로 전역성을 띄기에 다른 객체와 공유가 용이하다.
- 단점
  - private 생성자를 가지고 있기 때문에 상속할 수 없다.
  - 테스트하기가 어렵다.
  - 서버환경에서는 싱글턴이 하나만 만들어지는 것을 보장하지 못한다.
  - 싱글톤의 사용은 전역 상태를 만들 수 있기 때문에 바람직하지 못하다.

## 싱글턴을 만드는 방식
### 1. public static 멤버가 final 필드인 방식
: 생성자는 private으로 감춰두고, 유일한 인스턴스에 접근할 수 있는 수단으로 public static 멤버를 하나 마련해둔다.
```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() { }

    public void leaveTheBuilding() { }
}
 ```
- 장점
  - 해당 클래스가 싱글텀임이 API에 명백히 드러난다.
  - 간결하다.
- 예외
  - AccessibleObject.setAccessible을 사용해 private 생성자를 호출할 수 있다.
- 예외 해결방법
  - 생성자를 수정하여 두번째 객체가 생성되려할 때 예외를 던지게 하면 된다.
  ```java
  public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() { 
        if(INSTANCE != null) {
            throw new RuntimeException("생성자를 호출할 수 없습니다!");
        } 
    }
  
    public void leaveTheBuilding() { }
  }
  ```
<br/>

### 2. 정적 팩터리 메서드를 public static 멤버로 제공하는 방식
```java
public class Elvis { 
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() { }
    public static Elvis getInstance() { return INSTANCE; }
    
    public void leaveTheBuilding() { }
}
 ```

- 장점
  - API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.<br/>
    (getInstance()호출부 수정 없이 내부에서 private static 이 아닌 새 인스턴스를 생성해주면 된다)
    유일한 인스턴스를 반환하던 팩터리 메서드가 호출하는 스레드 별로 다른 인스턴스를 넘겨주게 할 수 있다.
  - 원한다면 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다.
  - 정적 팩터리의 메서드 참조를 공급자(supplier)로 사용할 수 있다.<br/>
    Supplier: get메서드 만을 가지고 아무 type이나 리턴할 수 있는 인터페이스.

### 위 두 방식의 문제점
- 각 클래스를 직렬화한 후 역직렬화할 때 새로운 인스턴스를 만들어서 반환한다.
- 이를 해결하기 위해서는 readResolve 에서 싱글턴 인스턴스를 반환하고, 모든 필드에 transient(직렬화 제외) 키워드를 넣는다.
```java
public class Elvis { 
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() { }
    public static Elvis getInstance() { return INSTANCE; }
    
    public void leaveTheBuilding() { }
    
    private Obejct readResolve() { 
        return INSTANCE; 
    }
}
 ```

### 3. 원소가 하나인 열거 타입을 선언하는 방식
```java
public enum Elvis {
    INSTANCE;
    
    public String getName() { 
        return "Elvis"; 
    }
    
    public void leaveTheBuilding() { }
}
 ```
- enum 클래스는 기본적으로 생성자를 선언해서 인스턴스를 만들 수 없기 때문에 복잡한 직렬화 상황이나 리플렉션 공격에도 제 2의 인스턴스가 생기는 일을 완벽히 막아준다.
- 단, 만들려는 싱글턴이 Enum 외의 클래스를 상속해야한다면 이 방법은 사용할 수 없다.