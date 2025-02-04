## 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라

### 상속용 클래스가 지켜야 할 것들
- 재정의할 수 있는 메서드들을 내부적으로 어떻게 이용하는지 문서로 남겨야 한다.
  - 어떤 순서로 호출하는지, 호출 결과가 이어지는 처리에 어떤 영향을 주는지도 담아야 한다.
  - 재정의 가능 메서드란 public, protected 중 final이 아닌 모든 메서드를 말한다.
  - 재정의 가능한 메서드를 호출할 수 있는 모든 상황을 문서로 남기는 것이 좋다.
    - 백그라운드 스레드나 정적 초기화 과정에서 호출될 수도 있으므로 유의하자.

### Implentation Requirements와 @implSpec 태그 - remove() 의 예
API 문서 메서드 설명 끝에서 발견할 수 있는 문구 중 <code>Implementation Requirements</code>가 있다. <br>이 절은 메서드 주석에 <code>@implSpec</code> 태그를 붙이면 자바독 도구가 생성해준다.

```java
/**
* {@inheritDoc}
*
* @implSpec
* This implementation iterates over the collection looking for the
* specified element.  If it finds the element, it removes the element
* from the collection using the iterator's remove method.
*
* <p>Note that this implementation throws an
* {@code UnsupportedOperationException} if the iterator returned by this
* collection's iterator method does not implement the {@code remove}
* method and this collection contains the specified object.
*
* @throws UnsupportedOperationException {@inheritDoc}
* @throws ClassCastException            {@inheritDoc}
* @throws NullPointerException          {@inheritDoc}
*/
public boolean remove(Object o) {
  Iterator<E> it = iterator();
  if (o==null) {
      while (it.hasNext()) {
          if (it.next()==null) {
              it.remove();
              return true;
          }
      }
  } else {
      while (it.hasNext()) {
          if (o.equals(it.next())) {
              it.remove();
              return true;
          }
      }
  }
  return false;
}
```
- <code>@implSpec</code>은 이 클래스를 상속하여 메서드를 재정의했을 때 나타날 효과를 상세히 설명하고 있다.
  - 이 주의점을 통해 우리는 어떤 메서드를 어떤 방식으로 상속해야 할지 알 수 있다.
  - 상속용 클래스가 지켜야 할 좋은 문서화의 예이다. 

### 훅(hook) 메서드 공개하기 - removeRange() 의 예
```java 
/**
 * Removes from this list all of the elements whose index is between
 * {@code fromIndex}, inclusive, and {@code toIndex}, exclusive.
 * Shifts any succeeding elements to the left (reduces their index).
 * This call shortens the list by {@code (toIndex - fromIndex)} elements.
 * (If {@code toIndex==fromIndex}, this operation has no effect.)
 *
 * <p>This method is called by the {@code clear} operation on this list
 * and its subLists.  Overriding this method to take advantage of
 * the internals of the list implementation can <i>substantially</i>
 * improve the performance of the {@code clear} operation on this list
 * and its subLists.
 *
 * @implSpec
 * This implementation gets a list iterator positioned before
 * {@code fromIndex}, and repeatedly calls {@code ListIterator.next}
 * followed by {@code ListIterator.remove} until the entire range has
 * been removed.  <b>Note: if {@code ListIterator.remove} requires linear
 * time, this implementation requires quadratic time.</b>
 *
 * @param fromIndex index of first element to be removed
 * @param toIndex index after last element to be removed
 */
protected void removeRange(int fromIndex, int toIndex) {
    ListIterator<E> it = listIterator(fromIndex);
    for (int i=0, n=toIndex-fromIndex; i<n; i++) {
        it.next();
        it.remove();
    }
}
```
- 주석에서 이 메서드가 clear()에 의해 호출됨을 알리고 있다.
- clear()를 고성능으로 만들기 쉽게 하기 위해 이 메서드를 외부로 공개하고 있다.
- 이렇게 특정한 이유로 <code>protected</code> 접근 제어자로 메서드를 노출해야 할 필요가 있는 경우도 있다.

### 상속용 클래스와 그 제약
- 클래스를 상속용으로 설계하려면 엄청난 노력이 들고 그 클래스 안에 제약도 상당하다.
- 상속용으로 설계하지 않은 클래스는 상속을 금지하는 편이 버그를 줄일 수 있다.
  - 클래스를 <code>final</code>로 만들어 상속을 금지한다.
  - 모든 생성자를 private 혹은 package-private으로 선언하고 public 정적 팩터리를 만든다.
- 혹여나 일반 클래스에서 상속을 허용하고 싶다면, 재정의 가능 메서드는 절대 사용하지 않도록 문서에 표기하자.

### 정리
- 상속용 메서드를 만들 때는 클래스 내부에서 스스로를 어떻게 사용하는지 문서로 남기자.
- 문서화한 것은 그 클래스가 쓰이는 한 반드시 지키자.
  - 그렇지 않을 경우 하위 클래스의 오동작을 만들 수 있다.
- 클래스를 확장해야 할 명확한 이유가 떠오르지 않으면 상속을 금지하자.
  - 클래스를 <code>final</code>로 만들거나 생성자를 모두 외부에서 접근 불가능하게 바꾸면 된다.