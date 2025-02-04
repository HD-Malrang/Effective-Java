## 추상 클래스보다는 인터페이스를 우선하라

### 추상 클래스와 인터페이스
- 공통점
  - 메서드의 시그니쳐만 만들고 구현을 구현 클래스에게 맡긴다.
- 차이점
  - <code>추상클래스</code>: 단일 상속만 가능하다. 구현체는 추상클래스의 하위 클래스가 된다.
  - <code>인터페이스</code>: 다중 상속이 가능하다. 인터페이스를 구현했다면, 같은 타입으로 취급된다.

### 인터페이스의 장점 활용하기
1. 믹스인 (mixin)
- 믹스인: 구현 클래스에 '선택적 행위'를 제공한다고 선언하는 효과를 준다.
- ex) Comparable, Iterable, AutoCloseable, Serializable
<b>추상 클래스는 이미 다른 클래스를 상속하는 클래스의 경우, 해당 클래스가 두 부모 클래스를 가질 수는 없으므로 믹스인으로 사용될 수 없다.</b>

2. 계층이 없는 타입 프레임워크
```java
public class Item20Test {
    interface Singer {
        void Sing();
    }

    interface Songwriter {
        void compose(int chartPosition);
    }

    interface SingerSongWriter extends Singer, Songwriter {
        void strum();
    }
}
```
- <code>SingerSongWriter</code> 인터페이스처럼 인터페이스는 다른 인터페이스를 상속할 수 있다.
- 인터페이스의 상속은 상속이라는 단어는 사용하지만, 클래스의 상속처럼 부모, 자식 계층이 존재하지 않는다.
  - 부모 클래스의 생성자를 호출할 필요 없다.
  - 부모 클래스의 구현 내용도 이어받지 않는다.
  - 정의된 메서드들만 구현하면 된다.
- 클래스로 이와 같은 구조를 구현하려면, 상하 관계를 따져보며 차례로 단일 상속을 받아야 한다.
  - 만든 이후에도 클래스 상속이 갖는 여러가지 제약을 갖게 된다.

3. 골격 구현(템플릿 메서드 패턴) 활용
- 인터페이스로는 타입 정의 및 몇가지 디폴트 클래스를 구현한다.
- 골격 구현 추상 클래스로 나머지 메서드들까지 구현한다.
```java
static List<Integer> intArrayAsList(int[] a) {
  Objects.requireNonNull(a);

  return new AbstractList<>() {
    @Override
    public Integer get(int i) {
      return a[i];
    }

    @Override
    public Integer set(int i, Integer val) {
      int oldVal = a[i];
      a[i] = val;
      return oldVal;
    }

    @Override
    public int size() {
      return a.length;
    }
  }
}
```
- 이 코드는 골격 구현의 구체 클래스이다.
  - 사용자는 몇가지 오버라이딩 메서드만을 통해 원하는 바를 구현했다.
- int 배열을 받아 Integer 인스턴스의 리스트 형태로 보여주는 어댑터이기도 하다.

골격 구현 클래스는 추상 클래스처럼 구현을 도와주는 동시에 추상 클래스로 타입을 정의할 때 따라오는 심각한 제약에서는 자유롭다. <br>인터페이스 디폴트 메서드가 갖는 한계를 추상클래스를 이용해서 벗어난다.


인터페이스를 구현한 클래스에서 골격 구현을 확장한 private 내부 클래스를 정의하고, 각 메서드 호출을 내부 클래스의 인스턴스에 전달하여 활용할 수도 있다. <br>이는 래퍼 클래스에서의 활용법과 비슷하다. <br>이 방식을 시뮬레이트한 다중 상속이라 하고, 다중 상속의 많은 장점을 제공하며 동시에 단점은 피하게 해준다.