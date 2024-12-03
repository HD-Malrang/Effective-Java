## 태그 달린 클래스보다는 계층구조를 활용하라

### 태그 달린 클래스의 예
```java
public class Item23Test {
    static class Figure {
        enum Shape { RECTANGLE, CIRCLE };

        // 태그 필드 - 현재 모양을 나타낸다.
        final Shape shape;

        // 다음 필드들은 모양이 사각형(RECTANGLE)일 때만 쓰인다.
        double length;
        double width;

        // 다음 필드는 모양이 원(CIRCLE)일 때만 쓰인다.
        double radius;

        // 원용 생성자
        Figure(double radius) {
            shape = Shape.CIRCLE;
            this.radius = radius;
        }

        // 사각형용 생성자
        Figure(double length, double width) {
            shape = Shape.RECTANGLE;
            this.length = length;
            this.width = width;
        }

        double area() {
            switch(shape) {
                case RECTANGLE -> {
                    return this.length * this.width;
                }
                case CIRCLE ->  {
                    return Math.PI * (radius * radius);
                }
                default -> {
                    throw new AssertionError(shape);
                }
            }
        }
    }

    @Test
    public void tagClassTest() {
        Figure figure = new Figure(10, 10);
        System.out.println("figure.shape = " + figure.shape);
    }
}
```
- 두가지 이상의 의미 중 현재 표현하는 의미를 태그 값 (fianl Shape) 으로 알려주고 있다.
- 태그 달린 클래스는 장황하고, 오류를 내기 쉽고, 비효율적이다.
  - 여러 구현이 한 클래스에 혼합되어 있어서 가독성이 나쁘다.
  - 또 다른 의미를 추가하려면 코드를 수정해야 한다.
  - 인스턴스의 타입만으로는 현재 나타내는 의미를 알 수가 없다.

### 태그 달린 클래스를 클래스 계층구조로 바꾸기
```java
abstract static class Figure {
    abstract double area();
}

static class Circle extends Figure {
    final double radius;

    Circle(double radius) {
        this.radius = radius;
    }

    @Override
    double area() {
        return Math.PI * (radius * radius);
    }
}

static class Rectangle extends Figure {
    final double width;
    final double length;

    public Rectangle(double width, double length) {
        this.width = width;
        this.length = length;
    }

    @Override
    double area() {
        return width * length;
    }
}

static class Square extends Rectangle {
    Square(double side) {
        super(side, side);
    }
}
```
- 각 의미를 독립된 클래스에 담아 관련 없던 데이터 필드를 모두 제거했다.
- 타입이 의미별로 존재하니 변수의 의미를 명시하거나 제한 할 수 있고, 또 특정 의미만 매개변수로 받을 수 있다.

### 정리
- 새 클래스를 작성하는데 태그 필드가 등장하면, 태그를 없애고 계층구조로 대체하는 방법을 생각해보자.
- 기존 클래스가 태그 필드를 사용하고 있다면 계층구조로 리팩터링 하는 것을 고려해보자.