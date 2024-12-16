## 배열보다는 리스트를 사용하라

배열 vs 제네릭타입
1. 공변 / 불공변

- 배열은 공변이다.(함께 변한다)
<br>즉, Sub가 Super의 하위 타입이라면 배열 Sub[]는 배열 Super[]의 하위 타입이 된다.
- 제네릭은 불공변이다.
<br>즉 서로 다른 타입 Type1과 Type2가 있을 때, List<Type1>은 List<Type2>의 하위타입도 상위타입도 아니다.
 

ex 1) 배열과 리스트의 차이 1
```java
// 배열에 넣기
Object[] objectArray = new Long[1];
objectArray[0] = "Long에 문자열 넣기";       // 런타임 시 ArrayStoreException 발생

// 리스트에 넣기
List<Object> objectList = new ArrayList<Long>();
objectList.add("Long에 문자열 넣기");        // 컴파일 단계에서 오류 발생
```
- Long으로 선언된 배열과 리스트에 String을 넣을 수 없다.
- 배열의 경우 런타임 때 오류를 발견하지만, 리스트는 컴파일 시에 바로 알 수 있다.


2. 실체화

배열은 런타임에도 자신이 담기로 한 원소의 타입을 인지하고 확인한다.
<br>제네릭은 원소타입을 컴파일타임에만 검사하며, 런타임에는 알수조차 없다.
<br>즉, 제네릭은 타입 정보가 런타임에는 소거된다.
<br>소거는 제네릭이 지원되기 전의 레거시 코드와 제네릭 타입을 함께 사용할 수 있게 해주는 매커니즘으로, 자바 5가 제네릭으로 순조롭게 전환될 수 있도록 함
 

 

### 위의 주요 차이들로 인해 배열과 제네릭은 잘 어우러질 수 없다.

배열은 제네릭 타입, 매개변수화 타입, 타입 매개변수로 사용할 수 없다.

ex) new List<E>[], new List<String>[], new E[] 식으로 작성 시 컴파일할 때 오류가 발생한다.


### 배열로 형변환 할 때 오류가 발생하는 경우
배열로 형변환할 때 제네릭 배열 생성 오류나 비검사 형변환 경고가 뜨는 경우 대부분은 배열인 E[] 대신 컬렉션인 List<E>를 사용하면 해결된다.
<br>코드가 조금 복잡해지고 성능이 살짝 나빠질 수 있지만, 그 대신 <code>타입 안정성과 상호운용성</code>은 좋아진다.

<b>ex) 제네릭을 적용해야하는 소스</b>
```java
public class Chooser {
    private final Object[] choiceArray;
    
    public Chooser(Collection choices) {
        this.choiceArray = choices.toArray();
    }
    
    public Object choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceArray[rnd.nextInt(choiceArray.length)];
    }
}
```
- 이 클래스는 컬렉션 안의 원소 중 하나를 무작위로 선택해 반환하는 choose 메서드가 있다.
- 이 클래스를 사용하려면 choose 메서드를 호출할 때마다 반환된 Object를 원하는 타입으로 형변환해야한다.
- 혹시나 다른 타입의 원소가 들어있다면 런타임 시에 형변환을 하며 오류가 발생할 것이다.

<b> ex) 위 소스를 제네릭으로 적용</b>
```java
public class Chooser<T> {
    private final T[] choiceArray;

    public Chooser(Collection<T> choices) {
        this.choiceArray = (T[]) choices.toArray();
    }
	
    public Object choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceArray[rnd.nextInt(choiceArray.length)];
    }
}
``` 
- 위와 같이 제네릭으로 적용 시, 비검사 경고가 발생한다.
- T가 무슨 타입인 지 알 수 없으니 컴파일러는 이 형변환이 런타임에도 안전한 지 보장할 수 없다는 메시지
- 제네릭에서는 원소의 타입 정보가 소거되어 런타임에는 무슨 타입인 지 알 수 없다는 것을 기억해야한다.
- 이 소스는 동작하지만, 컴파일러가 안전을 보장하지 못한다.
- 이 경고를 제거하려면, 배열 대신 리스트를 사용하면 된다.

<b> ex) 리스트로 적용</b>
```java
class Chooser<T> {
    private final List<T> choiceList;

    public Chooser(Collection<T> choices) {
        this.choiceList = new ArrayList<>(choices);
    }

    public T choose() {
        Random rnd = ThreadLocalRandom.current();
        return choiceList.get(rnd.nextInt(choiceList.size()));
    }
}
```
- 코드가 복잡해지고 조금 느려졌겠지만, 런타임 시 ClassCastException을 피할 수 있다.

### 정리
- 배열은 공변이고 실체화되어 런타임 시 타입 안전하지 않지만, 컴파일 시에는 그렇지 않다.
- 반면, 제네릭은 불공변이고 타입 정보가 소거되어 그 반대다.
- 위와 같은 특성으로 인해 둘을 섞어쓰기는 쉽지 않으며, 둘을 섞어 사용하다가 오류나 경고를 만나면 배열을 리스트로 대체해보자. 