## item34 예제, 열거 타입의 제약

ex 1. 데이터와 메서드를 갖는 열거 타입

public enum Planet { MERCURY(3.302e+23, 2.439e6), VENUS  (4.869e+24, 6.052e6), EARTH  (5.975e+24, 6.378e6), MARS   (
6.419e+23, 3.393e6), JUPITER(1.899e+27, 7.149e7), SATURN (5.685e+26, 6.027e7), URANUS (8.683e+25, 2.556e7), NEPTUNE(
1.024e+26, 2.477e7);

```java
  private final double mass;           // 질량(단위: 킬로그램)
private final double radius;         // 반지름(단위: 미터)
private final double surfaceGravity; // 표면중력(단위: m / s^2)

// 중력상수(단위: m^3 / kg s^2)
private static final double G=6.67300E-11;

        // 생성자
        Planet(double mass,double radius){
        this.mass=mass;
        this.radius=radius;
        surfaceGravity=G*mass/(radius*radius);
        }

public double mass(){return mass;}
public double radius(){return radius;}
public double surfaceGravity(){return surfaceGravity;}

public double surfaceWeight(double mass){
        return mass*surfaceGravity;  // F = ma
        }
        }
```

```java
public class WeightTable {
    public static void main(String[] args) {
        double earthWeight = Double.parseDouble(args[0]);
        double mass = earthWeight / Planet.EARTH.surfaceGravity();
        for (Planet p : Planet.values())
            System.out.printf("%s에서의 무게는 %f이다.%n",
                    p, p.surfaceWeight(mass));
    }
}
```

열거 타입은 자신 안에 정의된 상수들의 값을 배열에 담아 반환하는 정적 메서드인 values를 제공.

값들은 선언된 순서로 저장된다.  (그렇다고 이 순서에 의존하는 코드를 작성하는 것은 매우 위험)

ex 2. 값에 따라 분기하는 열거 타입

```java
public enum Operation {
    PLUS, MINUS, TIMES, DIVDE;

    public double apply(double x, double y) {
        switch (this) {
            case PLUS:
                return x + y;
            case MINUS:
                return x - y;
            case TIMES:
                return x * y;
            case DIVDE:
                return x / y;
        }
        throw new AssertionError("알 수 없는 연산:" + this);
    }
}
```

-> 깨지기 쉬운 코드 : 새로운 상수 추가시 해당 case문도 추가해야한다. 혹시라도 깜빡하게 되면 오류가 발생한다.

ex 3. 상수별 클래스 몸체와 데이터를 사용한 열거 타입

- 열거 타입에 apply라는 추상 메서드를 선언하고 각 상수별 클래스 몸체, 즉 각 상수에서 자신에 맞게 재정의하는 방법.

-> apply가 추상 메서드이므로 재정의하지 않았다면 컴파일 오류로 알려준다. (깜빡할 수가 없다.)

```java
public enum Operation {
    PLUS("+") {
        public double apply(double x, double y) {
            return x + y;
        }
    },
    MINUS("-") {
        public double apply(double x, double y) {
            return x - y;
        }
    },
    TIMES("*") {
        public double apply(double x, double y) {
            return x * y;
        }
    },
    DIVDE("/") {
        public double apply(double x, double y) {
            return x / y;
        }
    }
}
```

```java
    private final String symbol;

        Operation(String symbol){
        this.symbol=symbol;
        }

@Override
public String toString(){
        return symbol;
        }

public abstract double apply(double x,double y);
        }
```

ex 4. 열거 타입용 fromString 메서드 구현하기

- toString 재정의시 toString이 반환하는 문자열을 해당 열거 타입 상수로 변환해주는 fromString 메서드 구현도 고려.

```java
private static final Map<String, Operation> stringToEnum=
        Stream.of(Operation.values())
        .collect(Collectors.toMap(Operation::toString,operation->operation));
//  {문자열, 열거 타입 상수}

//Optional로 반환하여 값이 존재하지않을 상황을 클라이언트에게 알린다.
public static Optional<Operation> fromString(String symbol){
        return Optional.ofNullable(stringToEnum.get(symbol));
        }

```

열거 타입의 제약

1. 생성자에서 접근할 수 있는 것은 상수 변수뿐이다.

- 열거 타입의 생성자가 실행되는 시점에는 정적 필드가 초기화되기 전이기 때문에 생성자에서 정적 필드를 참조하려고 시도하면 컴파일 에러가 발생한다.

따라서 자신의 인스턴스를 추가하지 못하게 하는 제약이 존재.

(It is illegal to access static member 'ENUM' from enum constructor or instance initializer)

열거 타입 상수 생성 순서

2. 열거 타입 상수끼리 코드를 공유하기 어렵다.

-> 전략 열거 타입 패턴 (내부에 또 다른 enum을 정의해서 선택하도)

```java
public enum PayrollDay {
    MONDAY(PayType.WEEKDAY),
    TUESDAY(PayType.WEEKDAY),
    WEDNESDAY(PayType.WEEKDAY),
    THURSDAY(PayType.WEEKDAY),
    FRIDAY(PayType.WEEKDAY),
    SATURDAY(PayType.WEEKEND),
    SUNDAY(PayType.WEEKEND);

    private final PayType payType;

    PayrollDay(PayType payType) {
        this.payType = payType;
    }

    int pay(int minutesWorked, int payRate) {
        return payType.pay(minutesWorked, payRate);
    }

    private enum PayType {
        WEEKDAY {
            int overtimePay(int minutesWorked, int payRate) {
                return minutesWorked <= MINS_PER_SHIFT ?
                        0 : (minutesWorked - MINS_PER_SHIFT) * payRate / 2;
            }
        },
        WEEKEND {
            int overtimePay(int minutesWorked, int payRate) {
                return minutesWorked * payRate / 2;
            }
        };

        abstract int overtimePay(int minutesWorked, int payRate);

        private static final int MINS_PER_SHIFT = 8 * 60;

        int pay(int minutesWorked, int payRate) {
            int basePay = minutesWorked * payRate;
            return basePay + overtimePay(minutesWorked, payRate);
        }
    }
}
```

3. 코드를 수정 할 수 없는 기존 열거 타입에 상수별 동작을 넣을 때는 switch문이 좋은 선택이 될 수 있다.

```java
public static Operation inverse(Operation operation){
        switch(operation){
        case PLUS:
        return Operation.MINUS;
        case MINUS:
        return Operation.PLUS;
        case TIMES:
        return Operation.DIVDE;
        case DIVDE:
        return Operation.TIMES;
        }
        throw new AssertionError("알 수 없는 연산 : "+operation);
        }
```
