# ITEM 10. equals는 일반 규약을 지켜 재정의하라

> **꼭 필요한 경우가 아니면 equals를 재정의하지 말자. 많은 경우에 Object의 equals가 여러분이 원하는 비교를 정확히 수행해준다. 재정의해야 할 때는 그 클래스의 핵심 필드 모두를 빠짐없이, 다섯 가지 규약을 확실히 지켜가며 비교해야 한다.**

## equals를 재정의 하지 않는 것이 최선

- 다음의 경우에 해당한다면 equals를 재정의 할 필요가 없다.
- 각 인스턴스가 본질적으로 고유하다.
- 인스턴스의 ‘논리적 동치성’을 검사할 필요가 없다.
  - String과 같은 자료형
- 상위 클래스에서 재정의한 equls가 하위 클래스에도 적절하다.
  - list나 set의 경우, list는 abstract list에 set은 abstract set에 구현되어 있다.
- 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없다.
  - public클래스는 해당 클래스를 이용해 equals 메서드가 호출될 일이 있을 지 아닐 지 알 수가 없기 때문에 equals 메서드를 호출하지 않을 것이라 장담할 수 없다. 하지만 제한되어 있는 클래스인 경우, equals를 안쓸꺼라는 것을 어느정도 보장할 수 있다.
- equals를 재정의 해야 하는 경우
  - 객체 식별성(object identity, 두 객체가 물리적으로 같은가)이 아닌 “논리적 동치성”을 확인해야 하는데, 상위 클래스의 equals가 논리적 동치성을 비교하도록 재정의 되지 않았을 때 (주로 값 클래스)

## equals 규약

- 반사성 : A.equals(A) == true
- 대칭성 : A.equals(B) == B.equals(A)
  - CaseInsensitiveString
  - Date와 Timestamp는 이 대칭성 규약이 깨져있다.(둘은 상속관계)
  - 안정적으로 대칭성이 유지된 코드를 작성하고 싶다면 상속관계를 사용하지 않고 내부에서 선언하여 각각의 필드가 같은지 확인하는 식으로 코드를 작성하면 된다.
- 추이성 : A.equals(B) && B.equal(C), A.equals(C)
  - Point, ColorPoint(inherit), CounterPointer, ColorPoint(comp)
- 일관성 : A.equals(B) == A.equals(B)
  - 일관성을 지키고 싶다면 너무 복잡하게 구성하면 안된다.
  - 복잡하게 되어있는 예시로는 URL에 있는 equals 이다. URL에 있는 equals는 그 URL이 가르키는 ip까지를 확인하기 때문에 일관성이 깨진다.
- null-아님 : A.equals(null) == false

## equals 구현 방법

- == 연산자를 사용해 자기 자신의 참조인지 확인한다.
- instanceof 연산자로 올바른 타입인지 확인한다.
- 입력된 값을 올바른 타입으로 형변환 한다.
- 입력 객체와 자기 자신의 대응되는 핵심 필드가 일치하는지 확인한다.
- 추가.
  - float이나 double과 같은 부동소수점의 영향을 받는 자료형들은 equals가 아닌 해당 자료형에서 제공하는 compare()함수를 이용하여 비교하라.
  - 레퍼런스 타입의 경우는 레퍼런스 타입이 가지고 있는 equals를 사용하여 비교하면 된다.
  - null을 허용하고 싶다면 Objecs.equals()를 사용하면 된다.

- 구글의 AutoValue 또는 Lombok을 사용한다.
- IDE의 코드 생성 기능을 사용한다.
  - 코드가 약간 지저분해질 수 있고, 필드가 늘어날 때마다 다시 만들어줘야한다는 단점이 있다.
- Record 기능
  - 자바14버전부터 들어가 있고, 자바 버전의 15버전부터 본격적으로 사용할 수 있다.
  - 자바 17버전이상을 사용한다면 고려할 수 있는 방법.

- 주의사항
  - equals를 재정의 할 때 hashCode도 반드시 재정의하자(아이템11)
  - 너무 복잡하게 해결하지 말자.
  - Object가 아닌 타입의 매개변수를 받는 equals 메서드는 선언하지 말자.
  - 논리적인 동치성을 보장하려면 반드시 equals 메소드를 orverride 해야한다.
