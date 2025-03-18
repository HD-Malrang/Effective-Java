## 익명 클래스보다는 람다를 사용하라

### 익명 클래스 :  클래스의 선언과 객체 생성과 동시에 단 한번 사용할 수 있게 만든 클래스

```java
Collections.sort(words,new Comparator<String>(){
@Override
public int compare(String o1,String o2){
        return Integer.compare(o1.length(),o2.length());
        }
        });
```

- 자바8부터는 추상 메소드가 하나만 존재는 인터페이스는 람다식을 사용해 만들 수 있게 되었다.

Comparator 타입은 추상메서드 하나만 구현하면 되기 때문에 람다로 대체 가능.

```java
Collections.sort(words,(o1,o2)->Integer.compare(o1.length(),o2.length()));

```

- 비교자 메서드와 sort 메서드를 이용하면 더욱 짧아짐

```java
words.sort(Comparator.comparingInt(String::length));
```

타입을 명시해야 코드가 더 명확할 때만 제외하고는 람다의 모든 매개변수 타입을 생략하자

컴파일러가 타입을 알 수 없다는 오류를 내면 그 때 타입을 적어도 된다.

### 열거 타입에서 활용

```java
enum Operation {
    PLUS("+") {
        @Override
        public double apply(double x, double y) {
            return x + y;
        }
    },
    MINUS("-") {
        @Override
        public double apply(double x, double y) {
            return x - y;
        }
    },
    TIMES("*") {
        @Override
        public double apply(double x, double y) {
            return x * y;
        }
    },
    DIVIDE("/") {
        @Override
        public double apply(double x, double y) {
            return x / y;
        }
    };

    private final String symbol;

    Operation(String symbol) {
        this.symbol = symbol;
    }

    @Override
    public String toString() {
        return symbol;
    }

    public abstract double apply(double x, double y);
}
```

단순히 각 열거 타입 상수의 동작을 람다로 구현해 생성자에 넘기고, 생성자는 이 람다를 인스턴스 필드로 저장하여 사용

```java
enum Operation {
    PLUS("+", (x, y) -> x + y),
    MINUS("-", (x, y) -> x - y),
    TIMES("*", (x, y) -> x * y),
    DIVIDE("/", (x, y) -> x / y);

    private final String symbol;
    private final BinaryOperator<Double> op;

    Operation(String symbol, BinaryOperator<Double> op) {
        this.symbol = symbol;
        this.op = op;
    }

    @Override
    public String toString() {
        return symbol;
    }

    public double apply(double x, double y) {
        return op.apply(x, y);
    }
}
```

### 주의할 점

- 람다는 이름도 없고 문서화도 못하기 때문에 코드 자체로 동작이 명확히 설명되지 않거나 코드 줄 수가 많아지면 람다를 쓰지 않는 것이 좋다.

- 람다의 this 키워드는 바깥을 가리키므로 주의해야 한다.

  반면 익명 클래스의 this 키워드는 인스턴스 자신을 가리키므로 함수 객체가 자신을 참조해야 한다면 익명 클래스를 사용하자.

```java
class SelfReferenceExample {
    public void print() {
        System.out.println("Hello from " + this);
    }

    public Supplier<SelfReferenceExample> getSelf() {
        // return () -> this; //  람다 사용 불가 (컴파일 오류 발생)
        return new Supplier<>() {
            @Override
            public SelfReferenceExample get() {
                return this; //  익명 클래스 사용 가능
            }
        };
    }

    public static void main(String[] args) {
        SelfReferenceExample example = new SelfReferenceExample();
        Supplier<SelfReferenceExample> supplier = example.getSelf();
        System.out.println(supplier.get()); // 익명 클래스의 toString() 출력됨
    }
}
```

- 람다도 익명 클래스처럼 직렬화 형태가 구현별로（가령 가상머신별로） 다를 수 있다. 따라서 람다를 직렬화하는 일은 극히 삼가야 한다


