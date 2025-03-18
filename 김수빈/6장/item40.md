## @Override 애너테이션을 일관되게 사용하라

### @Override가 없을 때 하기 쉬운 실수

```java
public class Item40Test {
    static class Bigram {
        private final char first;
        private final char second;

        public Bigram(char first, char second) {
            this.first = first;
            this.second = second;
        }

        public boolean equals(Bigram b) {
            return b.first == first && b.second == second;
        }

        public int hashCode() {
            return 31 * first + second;
        }
    }

    @Test
    public void bigramTest() {
        Set<Bigram> s = new HashSet<>();
        for (int i = 0; i < 10; i++) {
            for (char ch = 'a'; ch <= 'z'; ch++) {
                s.add(new Bigram(ch, ch));
            }
        }

        Assertions.assertEquals(26, s.size()); // 실제 값 260
    }
}
```

- bigramTest() 가 원한 결과는 s.size()가 26인 것이지만 실제로는 260이 나왔다.
- equals()에 @Override 애너테이션을 붙이지 않아서 생긴 실수가 있다.
    - Object에서 상속받는 equals()는 원래 Object 타입의 파라미터를 받는데, Bigram의 파라미터를 받고 있다.
    - 그래서 Set에서 비교에 사용되는 equals()가 제대로 정의되지 않고, 오직 객체 주소의 동치만 비교하는 기본 Object의 equals()가 쓰이고 있던 것이다.
    - 따라서 같은 소문자를 소유한 바이그램 10개 각각이 서로 다른 객체로 인식되고, 결국 260을 출력한 것이다.
    - 실제 오버라이딩(재정의)된 것이 아니라 오버로딩을 하고 있는 것이다.

### 실수 고치기

```java
@Override
public boolean equals(Object o){
        if(!(o instanceof Bigram)){
        return false;
        }

        Bigram b=(Bigram)o;
        return b.first==first&&b.second==second;
        }
```

- 위와 같이 @Override 애너테이션을 달면, 실제로 상속받은 메서드가 아니면 에러를 내주기 때문에 실수할 확률이 적어진다.
- 잘못한 부분을 명확히 알려주므로 곧장 올바르게 수정할 수 있다.

### 핵심 정리

- 재정의한 모든 메서드에 @Override 애너테이션을 달아 실수를 방지하자.
- 단, 구체 클래스에서 상위 클래스의 추상 메서드를 재정의한 경우엔 이 애너테이션을 달지 않아도 된다.