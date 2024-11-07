## 변경 가능성을 최소화하라

### 불변 클래스 : 그 인스턴스의 내부 값을 수정할 수 없는 클래스
  예 ) String, Wrapper Class, BigInteger, BigDecimal
  - 설계 구현 사용이 쉽다.
  - 오류 여지가 적고 안전하다.

 

### 불변 클래스 5가지 규칙
-  객체의 상태를 변경하는 메서드(변경자)를 제공하지 않는다. (setter)
- 클래스를 확장할 수 없게 한다. (extend X)
- 모든 필드를 final로 선언한다
- 모든 필드를 private으로 선언한다.
- 자신 외에는 내부의 가변 컴포넌트에 접근할 수 없도록 한다. 

 

### 불변 객체의 장점
- 근본적으로 스레드 안전 : 동기화 할 필요가 없다.

- 불변객체는 안심하고 공유 가능 : 스레드 간 영향을 주고받을 수 없기 때문

- 방어적 복사도 필요없다 > clone메서드 복사 생성자 필요 없다
  - String 클래스의 복사생성자는 되도록 사용하지 말자

- 한번 만든 인스턴스 최대한 재활용 가능
- 불변객체는 자유롭게 공유할 수 있음은 물론, 불변 객체끼리는 내부 데이터를 공유할 수 있다.
```java
public class BigInteger extends Number implements Comparable<BigInteger> {
  final int signum;
  final int[] mag;
  (...)

  public BigInteger negate() {
    return new BigInteger(this.mag, -this.signum);
    //this.mag 배열은 비록 가변이지만 복사하지 않고 원본 인스턴스와 공유한다.
  }

  // 내부 패키지에서만 생성할 수 있음, 
  BigInteger(int[] magnitude, int signum) {
    this.signum = magnitude.length == 0 ? 0 : signum;
    this.mag = magnitude;
    if (this.mag.length >= 67108864) {
      this.checkRange();
    }
  }
}
```
- 객체를 만들 때 다른 불변 객체들을 구성요소로 사용하면 이점이 많다 : 구조가 복잡해도 불변식 유지가 수월

- 불변객체는 그 자체로 실패 원자성을 제공한다 
  - 실패 원자성 : 메서드에서 예외가 발생한 후에도 그 객체는 메서드 호출전 상태와 같은 유효한 상태를 가진다.
 

### 불변 객체의 단점
- 값이 다르면 반드시 독립된 객체로 만들어야 한다. 값의 가짓수가 많으면 이를 모두 만드는데 큰 비용이 필요하다.
 

### 불변클래스를 만드는 설계 방법
- final 클래스 : 상속을 막는다.

- 정적 팩터리를 제공하는 방법 

  - 모든 생성자를 private 혹은 package-private으로 만들고 public 정적 팩터리를 제공한다. (캐싱 기능 추가도 가능하다.) 
  - public이나 protected 생성자가 없으니 다른 패키지에서는 이 클래스를 확장하는게 불가능하기 때문.

 


 

### 정리
- 클래스가 꼭 필요한 경우가 아니면 불변이어야 한다. (getter가 있다고 해서 setter를 무조껀 만들지는 말자)
- 무거운 값객체의 경우 성능때문에 어쩔수 없다면, 불변 클래스와 쌍을 이루는 가변 동반 클래스를 public 클래스로 제공하자.
- 불변으로 만들 수 없는 클래스라도 변경할 수 있는 부분을 최소한으로 줄이자
- 다른 합당한 이유가 없다면 모든 필드는 private final이어야 한다.