## 멤버 클래스는 되도록 static으로 만들어라

### <code>중첩 클래스</code> : 다른 클래스 안에 정의된 클래스
- 중첩 클래스는 자신을 감싼 바깥 클래스에서만 쓰여야한다. 그 외의 쓰임새가 있다면 톱레벨 클래스로 만들어야한다.
- 종류
  - 정적 멤버 클래스
  - (비정적) 멤버 클래스
  - 익명 클래스
  - 지역 클래스
1. 정적 멤버 클래스

- 다른 클래스 안에 선언되고, 바깥 클래스의 private 멤버 에도 접근할 수 있음 (일반 클래스와의 차이점)

- 일반 클래스처럼 독립적으로 동작할 수 있으며, 바깥 클래스의 인스턴스 없이도 생성 가능.

- 멤버 클래스에서 바깥 인스턴스에 접근할 일이 없다면 무조건 static을 붙여서 정적 멤버 클래스로 만들자.
```java
public class OuterClass {

    // private 멤버
    private final String secret2 = "This is a secret!";

    // 정적 멤버 클래스
    public static class NestedStaticClass {

        public void printSecret() {
            
            // 바깥 클래스의 private 멤버에 접근
            OuterClass outerClass = new OuterClass();
            System.out.println("Accessing private member: " + outerClass.secret2);

        }
    }

    public static void main(String[] args) {
        // 정적 멤버 클래스의 인스턴스 생성
        OuterClass.NestedStaticClass nested = new OuterClass.NestedStaticClass();
        nested.printSecret(); // 출력: Accessing private member: This is a secret!
    }

}
```
 

 

2. 비정적 멤버 클래스

- static이 없는 내부 클래스.비정적 멤버 클래스의 인스턴스는 바깥 클래스의 인스턴스와 암묵적으로 연결

- 비정적 멤버 클래스는 바깥 인스턴스 없이는 생성 불가능.

- 내부 클래스가 외부의 멤버를 사용하지 않아도, 숨겨진 외부 참조가 생성됨.
```java
OuterClass$NestedStaticClass.class

class OuterClass$NestedStaticClass {
    int innerClass;

    OuterClass$NestedStaticClass(final OuterClass this$0) {
        this.this$0 = this$0;
        this.innerClass = 20;
    }
}
 ```

 

클래스명.this 형태로 바깥 클래스의 이름을 명시하는 용법을 사용하여 바깥 인스턴스의 참조를 가져올 수 있음.

- 정규화 this : 일반적으로는 내부 클래스에서 바깥 클래스의 멤버를 자연스럽게 접근할 수 있으므로 필요한 경우에만 사용

 

 
```java
public class OuterClass {
    private final String instanceSecret = "Instance secret!";

    // 정적 멤버 클래스
    class NestedStaticClass {

        public void printInstanceSecret() {
            // 바깥 클래스의 인스턴스를 통해 private 멤버에 접근
            System.out.println("Accessing instance private member: " + OuterClass.this.instanceSecret);
            System.out.println("Accessing private member: " + instanceSecret);
        }
    }

    public static void main(String[] args) {
        OuterClass outer = new OuterClass(); // 바깥 클래스 인스턴스 생성
        outer.new NestedStaticClass().printInstanceSecret();
    }
}
 ```

### static을 생략하면 바깥 인스턴스로의 숨은 외부 참조를 갖게 됨. 참조 관리에 신경쓰지 않으면 메모리 누수 발생 가능.

비정적 멤버 클래스의 인스턴스가 <code>바깥 클래스의 인스턴스를 암묵적으로 참조</code>하므로, 바깥 클래스 인스턴스는 비정적 멤버 클래스의 참조가 살아 있는 한 수거되지 않음
3. 익명 클래스

- 쓰이는 시점에  선언과 동시에 인스턴스가 만들어짐. 코드의 어디서든 생성 가능

- 자바8부터 람다가 대체함 (함수형 인터페이스-추상메서드 하나 인 경우)
```java
import java.util.Arrays;
import java.util.Comparator;

public class Main {
    public static void main(String[] args) {
        String[] names = {"Alice", "Bob", "Charlie"};

        Arrays.sort(names, new Comparator<String>() {
            @Override
            public int compare(String s1, String s2) {
                return s1.length() - s2.length();
            }
        });

        System.out.println(Arrays.toString(names));
    }
}
```
람다 표현식으로 대체 : 
```java
import java.util.Arrays;

public class Main {
    public static void main(String[] args) {
        String[] names = {"Alice", "Bob", "Charlie"};

        Arrays.sort(names, (s1, s2) -> s1.length() - s2.length());

        System.out.println(Arrays.toString(names));
    }
}
 

- 익명 클래스의 또 다른 주 쓰임은 정적 팩터리 메서드를 구현할 때.

public abstract class Transport {
    public abstract void move();

    // 정적 팩터리 메서드
    public static Transport createCar(String model) {
        return new Transport() { // 익명 클래스
            private String carModel = model;

            @Override
            public void move() {
                System.out.println("The car " + carModel + " is moving.");
            }
        };
    }

    public static Transport createBicycle() {
        return new Transport() { // 익명 클래스
            @Override
            public void move() {
                System.out.println("The bicycle is moving.");
            }
        };
    }
}
 ```

 

 

4. 지역 클래스

- 지역 변수와 선언 위치나 유효 범위가 같음

  - 선언 위치 : 메서드 내부, 조건문이나 반복문 블록 내부, try-catch 블록 내부 ..
  - 유효 범위 : 정의된 블록 내부
- 익명 클래스와 비슷한 용도로 사용되지만, 이름이 있고 여러 메서드와 필드를 정의할 수 있음
```java
public class Main {
    public static void main(String[] args) {
        int[] array1 = {1, 2, 3};
        int[] array2 = {4, 5, 6};

        calculateSum(array1, array2);
    }

    static void calculateSum(int[] array1, int[] array2) {
        // 지역 클래스 정의
        class ArraySumCalculator {
            private int[] arr1;
            private int[] arr2;

            ArraySumCalculator(int[] arr1, int[] arr2) {
                this.arr1 = arr1;
                this.arr2 = arr2;
            }

            int[] calculate() {
                int length = Math.min(arr1.length, arr2.length);
                int[] sumArray = new int[length];
                for (int i = 0; i < length; i++) {
                    sumArray[i] = arr1[i] + arr2[i];
                }
                return sumArray;
            }
        }

        // 지역 클래스 사용
        ArraySumCalculator calculator = new ArraySumCalculator(array1, array2);
        int[] result = calculator.calculate();

        System.out.println("Sum of arrays: ");
        for (int value : result) {
            System.out.print(value + " ");
        }
    }
}
 ```

### 정리

- 메서드 밖에서도 사용해야 하거나 메서드 안에 정의하기엔 너무 길다면 멤버 클래스로 만든다.

- 멤버 클래스의 인스턴스 각각이 바깥 인스턴스를 참조한다면 <code>비정적</code>으로, 그렇지 않으면 <code>정적</code>

- 중첩 클래스가 한 메서드 안에서만 쓰이면서 그 인스턴스를 생성하는 지점이 단 한 곳이고 해당 타입으로 쓰기에 적합한 클래스나 인터페이스가 이미 있다면 <code>익명 클래스</code>로 만들고, 그렇지 않으면 <code>지역 클래스</code>