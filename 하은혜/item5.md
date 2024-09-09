# ITEM 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

> **클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자원이 클래스 동작에 영향을 준다면 싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다. 이 자원들을 클래스가 직접 만들게 해서도 안 된다. 대신 필요한 자원을 (혹은 그 자원을 만들어주는 팩터리를) 생성자에 (혹은 정적 팩터리나 빌더에) 넘겨주자. 의존 객체 주입이라 하는 이 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 기막히게 개선해준다.**

## 잘못된 예

- 많은 클래스가 하나 이상의 자원에 의존한다. 이때 아래와 같이 구현한 모습을 드물지 않게 볼 수 있다. 
  - static 유틸리티를 잘못 사용한 예
    ```java
    public class SpellChecker {
        private static final Lexicon dictionary; // 의존하는 리소스 (의존성)
        private SpellChecker() {} // 객체 생성 방지
        
        public static boolean isValid(String word) { }
        public static List<String> suggestions(String typo) { }
    }
     ```

  - 싱글턴을 잘못 사용한 예
    ```java
    public class SpellChecker { 
        private final Lexicon dictionary;
      
        private SpellChecker(Lexicon dictionary) {
            this.dictionary = Objects.requireNonNull(dictionary);
        }
      
        public boolean isValid(String word) { return true; }
        public List<String> suggestions(String typo) { return null; }
    
        public static void main(String[] args) {
            Lexicon lexicon = new KoreanDictionary();
            SpellChecker spellChecker = new SpellChecker(lexicon);
            spellChecker.isValid("hello");
        }
    }
  
    interface Lexicon{}
  
    class KoreanDictionary implements Lexicon {}
    class TestDictionary implements Lexicon {} // test가 가능해짐
     ```
- 위 두 방식 모두 사전을 단 하나만 사용한다고 가정한다는 점에서 좋은 코드가 아니다.
- 직접 명시되어 고정되어 있는 변수는 테스트를 하기 힘들게 만든다.
- **사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.**
- **의존성을 바깥으로 분리하여 외부로부터 주입받도록 해야한다.**

## 의존성 객체 주입
:의존성 객체 주입은 유연성과 테스트 용이성을 높여준다.

```java
public class SpellChecker { 
    private final Lexicon dictionary;
    
    private SpellChecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    }
    
    public boolean isValid(String word) { return true; }
    public List<String> suggestions(String typo) { return null; }
}
 ```
### 팩터리 메서드 패턴
:생성자에 자원 팩터리를 넘겨주는 방식

- 예시 : Supplier\<T\> 인터페이스
  ```java
  @FunctionalInterface
  public interface Supplier<T> {
      /**
       * Gets a result.
       *
       * @return a result
       */
      T get(); // T 타입 객체를 찍어낸다
  }
  ```

  ```java
  Mosaic create(Supplier<? extends Tile> tileFactory) { }
  ```
  - Supplier\<T\>를 입력으로 받는 메서드는 한정적 와일드카드 타입을 사용해 팩터리 타입 매개변수를 제한

  ```java
  public class SpellChecker {
      private final Lexicon dictionary;
    
      private SpellChecker(Supplier<Lexicon> dictionary) {
          this.dictionary = Objects.requireNonNull(dictionary);
      }
    
      public boolean isValid(String word) { return true; }
      public List<String> suggestions(String typo) { return null; }
    
      public static void main(String[] args) {
          Lexicon lexicon = new KoreanDictionary();
          SpellChecker spellChecker = new SpellChecker(() -> lexicon);
          spellChecker.isValid("hello");
      }
  }
  
  interface Lexicon{}
  
  class KoreanDictionary implements Lexicon {}
  class TestDictionary implements Lexicon {} // test가 가능해짐
  ```
- 의존성 주입이 유연함과 테스트 용이성을 개선해주긴 하지만, 의존성이 수천 개나 되는 큰 프로젝트에서는 코드를 어지럽게 만들기도 한다. 이런 점은 대거(Dagger), 주스(Guice), 스프링(Spring) 같은 의존 객체 주입 프레임워크를 사용하면 해소할 수 있다.
