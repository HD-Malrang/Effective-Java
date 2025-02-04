## 톱 레벨 클래스는 한 파일에 하나만 담으라

### 두 클래스가 한 파일에 정의되어 있을 때

Utensil.java
```java
class Utensil {
  static final String NAME = "pan";
}

class Dessert {
  static final String NAME = "cake";
}
```
Dessert.java
```java
class Utensil {
  static final String NAME = "pot";
}

class Dessert {
  static final String NAME = "pie";
}
```
Main.java
```java
public class Main {
  public static void main(String[] args) {
    System.out.println(Utensil.NAME + Desert.NAME);
  }
}
```
- 위는 Utensil.java와 Dessert.java라는 두 개의 파일에 중복으로 정의된 2개의 클래스의 예이다.
- 위와 같은 경우 <code>javac</code> 명령어에 들어가는 인수에 따라 실행결과가 달라진다.
  - javac Main.java Dessert.java: 에러, Utensil과 Dessert 클래스가 중복 정의되었습니다.
  - javac Main.java, javac Main.java Utensil.java: "pancake" 출력
  - javac Dessert.java Main.java: "potpie" 출력
 
- 파일을 나누면 위와 같은 복잡한 동작원리도 알 필요 없고, 잠재적 에러도 없으므로 Utensil.java와 Dessert.java 각 파일별로 클래스는 1개씩만 선언하는 것이 좋다.
### 굳이 한 파일에 여러 클래스를 정의하고 싶다면?
```java
public class Test {
  public static void main(String[] args) {
    System.out.println(Utensil.NAME + Dessert.NAME);
  }

  private static class Utensil {
    final String NAME = "pan";
  }

  private static class Dessert {
    final String NAME = "cake";
  }
}
```
### 정리

- 소스파일 하나에는 반드시 톱레벨 클래스(혹은 톱레벨 인터페이스)를 하나만 담자.
- 한 파일 내부에 클래스를 여러 개를 정의하면 아무런 이득 없이 괜히 에러만 발생한다.