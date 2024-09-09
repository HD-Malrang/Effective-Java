# 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라
 

클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자원이 클래스 동작에 영향을 줄 경우

싱글톤, 정적 유틸리티 클래스 ( 한 번 로드가 되면 수정이 불가능 ) 대신 의존 객체 주입 방식이 적절함.

-> 인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식

```java
public class Spellchecker {
	private final Lexicon dictionary;
	public Spellchecker(Lexicon dictionary) {
		this.dictionary = Objects.requireNonNulKdictionary);
	}
	public boolean isValid(String word) { ... }
	public List<String> suggestions(String typo) { ... }
}
```
장점 : 클래스의 유연성, 재사용성, 테스트 용이성(가짜 객체 주입 가능) 개선

 

### 변경 패턴 : 팩터리 메서드 패턴

객체를 생성할 때 어떤 클래스의 인스턴스를 만들 지 서브 클래스에서 결정하게 한다.

클래스의 인스턴스를 만드는 일을 서브클래스에게 맡기는 것

(잘 이해가 안 가서 찾아보고 내용 추가 필요)
