Item 4.  인스턴스화를 막으려거든 private 생성자를 사용하라

```java
public class UtilityClass {
	// 인스턴스화 방지용
	private Utilityclass() {
	throw new AssertionError();
	}
}
```
- Private 생성자를 통해 인스턴스화를 방지
-> 정적 팩토리메소드나 기타 개발자가 의도한 방향대로 생성자를 호출하도록 강제 가능
- 이 방식은 상속을 불가능하게 하는 효과도 존재