## Item 1. 생성자 대신 정적 팩토리 메소드를 고려하라

- 정적 팩터리 메서드와 public 생성자는 각자의 쓰임새가 있으니 상대적인 장단점을 이해하고 사용하는 것이 좋다. 그렇다 하더라도 정적 팩터리를 사용하는게 유리한 경우가 더 많으므로 무작정 public 생성자를 제공하던 습관이 있다면 고치자. 

```md
1. 이름을 가질 수 있다.
2. 호출될 때 마다 인스턴스를 새로 생성하지 않아도 된다 (Singletone)
3. 반환 타입의 하위 타입 객체를 반환할 수 있다.
4. 입력 파라미터에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
5. 정적 팩토리 메소드를 작성하는 시점에 반환할 객체의 클래스가 존재하지 않아도 된다.
```

1. 이름을 가질 수 있다.
- `from`： 매개변수를 하나 받아서 해당 타입의 인스턴스를 반환하는 형 변환 메서드
- `of`： 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드
- `valueOf`： from과 of의 더 자세한 버전
- `instance` or `getlnstance`： 매개변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지는 않는다.
- `create` 혹은 `newlnstance`： instance 혹은 getlnstance와 같지만, 매번 새로운 인스턴스를 생성해 반환함을 보장한다.
- `getType`： getlnstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다. 
`Type`은 팩터리 메서드가 반환할 객체의 타입이다.
- `newType` : newlnstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다. 
`Type`은 팩터리 메서드가 반환할 객체의 타입이다.
- `type` getType과 neu/Type의 간결한 버전

- 생성자의 오버로딩과 오버라이딩으로 표현하기 어려운 메소드의 의미를 직관적으로 표현하고 설계 목적을 알릴 수 있다.
- but, 컨벤션이 정립되지 않은 상황에서 무분별하게 사용할 경우, 오히려 코드의 복잡도를 올릴 수 있다.

2. 호출될 때 마다 인스턴스를 새로 생성하지 않아도 된다 (Singletone)

```java
class Singleton {
    private static Singleton instance;

    private Singleton() {}

    // Static Factory Method
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

- 오로지 하나의 객체만 반환하도록 하여 메모리를 아낄 수 있도록 유도

3. 반환 타입의 하위 타입 객체를 반환할 수 있다.

```java
interface SmarPhone {}

class Galaxy implements SmarPhone {}
class IPhone implements SmarPhone {}
class Huawei implements SmarPhone {}

class SmartPhones {
    public static SmarPhone getSamsungPhone() {
        return new Galaxy();
    }

    public static SmarPhone getApplePhone() {
        return new IPhone();
    }

    public static SmarPhone getChinesePhone() {
        return new Huawei();
    }
}
```

- 클래스의 다형성의 특징을 응용한 Static Factory Method의 특징이다.
메소드 호출을 통해 얻을 객체의 인스턴스를 자유롭게 선택할 수 있는 유연성을 가질 수 있다.


4. 입력 파라미터에 따라 매번 다른 클래스의 객체를 반환할 수 있다.

```java
interface SmartPhone {
    public static SmartPhone getPhone(int price) {
        if(price > 100000) {
            return new IPhone();
        }

        if(price > 50000) {
            return new Galaxy();
        }

        return new Huawei();
    }
}
```

- SmartPhone 객체 외부에서는 가격 제약조건을 알지 못하지만, 파라미터로 들어온 가격 제약조건에 따라,
각 SmartPhone이 어떤 구현체를 반환하는지 이해할 수 있다.
- 도메인 주도 개발(Domain Driven Develop) 패턴과 잘 어울릴 듯..?

5. 

6. 잘 활용하려면?
- 컨벤션, 엄격한 규칙 아래에서 정적 팩토리 메소드 활용
- 생성자를 `private`으로 생성하고, 이를 활용하는 정적 팩토리 메소드를 `public`으로 접근 제어
