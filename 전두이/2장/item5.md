# 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

## 정적 유틸리티를 잘못 사용한 예

```java
public class Spellchecker {
    private static final Lexicon dictionary = ...;
		
    private SpellChecker() {} // 객체 생성 방지
		
    public static boolean isValid(String word) { ... }
    public static List<String> suggestions(String typo) { ... }
}
```

- 이는 사전을 단 하나의 방식으로 사용한다고 가정한다는 점에서 문제가 발생한다. 사전은 언어별로 따로 있고, 특수 어휘용 사전을 별도로 두기도 한다. 
- 즉, 사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식은 적합하지 않다. (유연성이 떨어짐)

## 의존 객체 주입 패턴 (유연하고, 테스트 하기 쉽다)

```java
public class Spellchecker {
    private final Lexicon dictionary;
		
    public Spellchecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNulKdictionary);
    }

    public boolean isValid(String word) { ... }
    public List<String> suggestions(String typo) { ... }}
```

- 이는 `dictionary`라는 딱 하나의 자원만 사용하지만, 자원이 몇 개든 의존 관계가 어떻든 상관없이 잘 작동한다. 이러한 의존 객체 주입 패턴은 아주 단순하여 수많은 개발자가 이 자연스럽게 사용해오고 있다.
- 이 패턴의 변형으로, 생성자에 자원 팩터리를 넘겨주는 방식이 있다.
- 팩터리란 호출할 때마다 특정 타입의 인스턴스를 반복해서 만들어주는 객체를 말한다. 즉, `팩터리 메서드 패턴(Factory Method pattem)`을 구현한 것이다.
- 이 방식을 사용해 클라이언트는 자신이 명시한 타입의 하위 타입이라면 무엇이든 생성할 수 있는 팩터리를 넘길 수 있다.

---
## 결론

- 클래스가 특정 자원에 의존하고, 그 자원이 클래스의 동작에 중요한 역할을 한다면, 싱글턴 패턴이나 정적 유틸리티 클래스를 사용하는 것은 적절하지 않다. 또한, 클래스 내부에서 필요한 자원을 직접 생성하는 것도 피하는 것이 좋다.
- 대신, 클래스 외부에서 필요한 자원이나 그 자원을 생성해줄 수 있는 팩터리를 제공하고, 이를 생성자나 정적 팩터리, 또는 빌더를 통해 클래스에 전달하는 것이 좋다.
- 이 방법을 `의존성 주입(Dependency Injection)`이라고 하며, 이를 활용하면 클래스의 유연성, 재사용성, 그리고 테스트하기 쉬운 코드 구조를 만들 수 있다.