# 다 쓴 객체 참조를 해제하라

 

### 가비지 컬렉터가 있더라도 메모리 관리에 신경써야 함! *메모리 누수*

 

가비지 컬렉션 언어에서는 메모리 누수를 찾기가 어려움.

객체 참조를 하나 살려두면 그 객체 + 그 객체가 참조하는 모든 객체 +.. 를 회수 하지 못 함.

 

# 해법!

 

## 1. 해당 참조를 다 썼을 때 null 처리 (참조 해제)

**예외적인 경우에 사용 : 자기 메모리를 직접 관리하는 클래스일때

ex. 스택 클래스에서 pop 메서드를 제대로 구현 (꺼낸 객체는 참조가 더 이상 필요 없음)

```java
public Object pop() {
		if (size = 0)
			throw new EmptyStackException();
		Object result = elements[--size];
		elements[size] = null; // 다 쓴 참조 해제
		return result;
}
 ```

## 2. WeakHashMap

: 더이상 사용하지 않는 객체를 GC할 때 자동으로 삭제해주는 Map

** 캐시 외부에서 키를 참조하는 동안만 엔트리가 살아 있는 캐시가 필요한 상황.

```java
public static void main(String[] args) {
		WeakHashMap<Integer, String> weakHashMap = new WeakHashMap<Integer, String>();

		Integer key = new Integer(1);
		weakHashMap.put(key, "1");
		key = null;

		// If GC is generated, the output changes to {}.
		while (true) {
			System.out.println(weakHashMap);
			System.gc();
			if (weakHashMap.size() == 0) {
				break;
			}
		}

		System.out.println("End");
	}
```
해당 코를 실행해보면 key를 강하게 참조하는 곳이 없기 때문에 GC할 때 캐시가 지워진다.

 
<br>

```
참고로, String 클래스를 Key로 하는 WeakHashMap을 사용하면 의미가 없다. 왜냐하면 규칙 5에서 설명했듯이 String은 내부적으로 한 번 생성된 String에 대해 Constant Pool에 항상 참조가 존재하기 때문이다.

[출처] https://camel-context.tistory.com/42
```
 

리스너나 콜백도 약한 참조로 저장하면 가비지 컬렉터가 즉시 수거해감

- 콜백함수 : 다른 코드의 인수로서 넘겨주는 실행 가능한 코드

 <br>

메모리 누수는 겉으로 잘 드러나지 않아 시스템에 수년간 잠복하는 사례도 있다. 

이런 누수는 철저한 코드 리뷰나 힙 프로파일러 같은 디버깅 도구를 동원해야만 발견되기도 한다. 

그래서 이런 종류의 문제는 예방법을 익혀두는 것이 매우 중요하다.