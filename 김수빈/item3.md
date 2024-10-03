# private 생성자나 열거 타입으로 싱글턴임을 보증하라
 

## 싱글턴 : 인스턴스를 오직 하나만 생성할 수 있는 클래스

싱글턴 인스턴스는 가짜 구현으로 대체할 수 없기 때문에 이를 사용하는 클라이언트를 테스트하기가 어려워질 수 있다.

(타입을 인터페이스로 정의한 다음 그 인터페이스를 구현해서 만든게 아니라면.)

 

### 보통 싱글턴을 만드는 방식.

1. public static final 필드 방식의 싱글턴

```java
public class Elvis {
 public static final Elvis INSTANCE = new Elvis();
 private Elvis() { ... }
 public void leaveTheBuilding() { ... }
}
```
장점 : <br>
- 간결함, 해당 클래스가 싱글턴임이 API에 명백히 드러난다.

 

 

2. 정적 팩터리 방식의 싱글턴

```java
public class Elvis {
 private static final Elvis INSTANCE = new Elvis() ;
 private Elvis() { ... }
 public static Elvis getlnstance() { return INSTANCE; }
 public void leaveTheBuilding() { ... }
}
```
장점 : <br>
- API를 바꾸지 않고도 싱글턴이 아니게 변경 가능.

- 제네릭 싱글턴 팩터리로 만들 수 있음.

   - 여러 타입의 인스턴스 반환 가능.

```java
public class Elvis<T> {
 private static final Elvis<Object> INSTANCE = new Elvis() ;
 private Elvis() { ... }
 public static Elvis<T> getlnstance() { return (Elvis<T>)INSTANCE; }
}
```
```java
@Test
public void genericTest() {
    Elvis<String> elvis1 =  Elvis.getlnstance();
    Elvis<Integer> elvis2 = Elvis.getlnstance();
    Elvis<Elvis> elvis3 = Elvis.getlnstance();

    set.add("ab");
    set2.add(123);
    set3.add(Elvis.INSTANCE);
}
```

 

- 정적 팩터리의 메서드 공급자를 사용 가능

  - 매개변수를 받지 않고 단순히 반환하는 추상 메서드 존재.

  - 해당 조건과 부합하는 메서드는 공급자 인스턴스를 직접 구현하지 않아도 구현체처럼 사용 가능

Elvis::get Instance를 Supplier로 사용하는 식

```java
public class Concert {
 public void start(Supplier<Singer> singSupplier) {
 	Singer singer = singSupplier.get();
    singer.sing();
    }

	public static void main(String[] args) {
    	Concert concert = new Concert();
        concert.start(Elvis::getInstance);
      }
}
```
 

3. 열거 타입 방식의 싱글턴 - 바람직한 방법

```java
public enum Elvis {
 INSTANCE;
 
 public void leaveTheBuilding() { ... }
}
```

- Enum 이외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없음
