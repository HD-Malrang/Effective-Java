## 인터페이스는 타입을 정의하는 용도로만 사용하라
 

### 상수 인터페이스 안티패턴

```java
public interface PhysicalConstants {

    static final double AVOGADROS_NUMBER = 6.022_140_857e23;
    static final double BOLTZMANN_CONSTANT = 1.380_648_52e-23;
    static final double ELECTRON_MASS = 9.109_383_56e-31;

}
```
1. 클래스 내부에서 사용하는 상수는 내부 구현에 해당. 상수 인터페이스는 내부 구현을 외부로 노출하는 행위.

2. 사용자에게 혼란을 줌

-  final이 아닌 클래스가 상수 인터페이스를 구현하게 되면 하위 클래스가 상수들로 오염됨.

 

### 상수를 정의하는 방법

1. 사용되는 클래스나 인터페이스 자체에 추가.

2. 인스턴스화 할 수 없는 유틸리티 클래스에 담기.

```java
public final class PhysicalConstants {

    private PhysicalConstants() { } // 인스턴스화 방지

    public static final double AVOGADROS_NUMBER = 6.022_140_857e23;
    public static final double BOLTZMANN_CONSTANT = 1.380_648_52e-23;
    public static final double ELECTRON_MASS = 9.109_383_56e-31;

}
```
<b>인터페이스는 타입을 정의하는 용도로만 사용해야 한다. 상수 공개용 수단으로 사용하지 말자.</b>

