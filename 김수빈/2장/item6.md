# 불필요한 객체 생성을 피하라

 

## 1. String s = new String("bikini"); -> String s = "bikini";

-  new String 은 실행될 때 마다 String 인스턴스를 새로 만든다.

- 개선된 코드는 인스턴스를 매번 만드는 대신 하나의 String 인스턴스를 사용함.

- jvm에서는 문자열을 캐싱한다.

```
자바는 문자열 리터럴이 동일하다면 String 객체를 공유하도록 되어 있다. name1과 name2 변수가 동일한 문자열 리터럴을 참조할 경우 name1과 name2는 동일한 String 객체를 참조하게 된다
출처: https://woonys.tistory.com/221 [WOONY's 인사이트:티스토리]
```

```
Java의 Heap에는 String Pool 이라는 특별한 영역에서 String 객체들을 관리합니다. 이 String Pool의 실체는 HashMap입니다. 다음 코드와 같이 문자열 리터럴을 사용하여 String 객체를 생성하면 String Pool에 기존에 같은 값을 가지는 String 객체가 있는지 검사하고 있으면 그 객체의 참조값을, 없으면 String Pool에 새로 String 객체를 생성하고 그 참조값을 리턴합니다.

출처: https://dololak.tistory.com/718 [코끼리를 냉장고에 넣는 방법:티스토리]
```
 <br>

## 2. 생성자 대신 정적 팩터리 메서드를 사용

``
Boolean(String) -> Boolean.valueOf(String)
``

팩터리 메서드는 생성자처럼 호출할때마다 새로운 객체를 만들지 않는다.

 
<br>

## 3. 생성 비용이 비싼 개체가 반복해서 필요할 경우 캐싱하여 재사용

ex. Pattern은 인스턴스 생성 비용이 높음 .클래스 초기화 과정에서 직접 생성해 캐싱해두고 , 나중에 필요할때마다 이 인스턴스를 재사용.

```java
public class RomanNumerals {
	private static final Pattern ROMAN = Pattern.compile(
		"^?=.)M*(:[MD] |D?C{0f3})"
		+ "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$")；
        
	static boolean isRomanNumeral(String s) {
		return ROMAN.matcher(s).matches();
	}
}
```
<br>
 

## 4. 어댑터(뷰)는 실제 작업은 할 수 없고 실제 작업을 하는 뒷단 객체의 인스턴스 역할을 함.

뒷단 객체 하나당 어댑터 하나만 만들어지면 충분

ex. Map(뒷단 객체) 인터페이스 keySet 메서드(어댑터)는 호출할때마다 같은 인스턴스를 반환한다.

keySet을 여러 개 만들 필요가 없음.

 <br>

## 5. 오토박싱 : 기본 타입과 박싱된 기본 타입을 섞어 쓸 때 자동으로 상호 변환해주는 기술

```java
private static long sum() {
	Long sum = 0L;
	for (long i = 0; i <= Integer.MAX_VALUE; i++)
		sum += i;
	return sum;
}
```
박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의

 <br>
 ------------------------------------------------------------------------------
 <br>

방어적 복사가 필요한 상황에서 객체를 재사용했을 때의 피해가,
필요 없는 객체를 반복 생성했을 때의 피해보다 훨씬 크다는 사실을 기억하자.


방어적 복사에 실패하면 언제 터져 나올지 모르는 버그와 보안 구멍으로 이어지지만, 

불필요한 객체 생성은 그저 코드 형태와 성능에만 영향을 준다.