# ITEM 7. 다 쓴 객체 참조를 해제하라

> **메모리 누수는 겉으로 잘 드러나지 않아 시스템에 수년간 잠복하는 사례도 있다. 이런 누수는 철저한 코드 리뷰나 힙 프로파일러 같은 디버깅 도구를 동원해야만 발견되기도 한다. 그래서 이런 종류의 문제는 예방법을 익혀두는 것이 매우 중요하다.**

- 어떤 객체에 대한 레퍼런스가 남아있다면 해당 객체는 가비지 컬렉션의 대상이 되지 않는다.
- 자기 메모리를 직접 관리하는 클래스라면 메모리 누수에 주의해야 한다.
  - 예) 스택, 캐시, 리스너 또는 콜백
  - 아래 코드의 Stack이 커졌다 줄어들었을 때 Stack에서 꺼내진 객체들을 GC가 회수하지 않는다.
  ```java
  public class Stack { 
      private Object[] elements;
      private int size = 0;
      private static final int DEFAULT_INITIAL_CAPACITY = 16;
      
      public Stack() {
          elements = new Object[DEFAULT_INITIAL_CAPACITY];
      }
      
      public void push(Object e) {
          ensureCapacity();
          elements[size++] = e;
      }
      
      public Object pop() {
          if (size == 0) 
              throw new EmptyStackException();
          return elements[--size];
      }
      
      /**
       * 원소를 위한 공간을 적어도 하나 이상 확보한다.
       * 배열 크기를 늘려야 할 때마다 대략 두 배씩 늘린다.
       */
      private void ensureCapacity() {
          if (elements.length == size)
              elements = Arrays.copyOf(elements, 2 * size + 1);
      }
  }
  ```
- 참조 객체를 null 처리하는 일은 예외적인 경우이며 가장 좋은 방법은 유효범위 밖으로 밀어내는 것이다.

## 참조 해제법
- 객체 참조에 직접 null 처리를 해준다.
  - 위에서 봤던 Stack과 같은 예시에서 사용한다.
  - Stack과 같은 케이스는 객체 자체가 아니라 객체 참조를 담는 elements 배열로 저장소 풀을 만들어 스스로 원소들을 관리한다. 이 때 배열 활성 영역에 속한 원소들이 사용되고 비활성 영역은 쓰이지 않는데 GC가 이 부분까지는 알 수가 없다는 것이다.
  - 따라서 비활성 영역의 객체를 더이상 쓰지 않을 것이라 null로 처리하여 준다.
- 특정한 자료구조를 활용한다.(weak reference)
- 직접 객체를 넣거나 빼준다.
- 스레드를 사용해 주기적으로 clean up 작업을 해준다.(ScheduledTheadPoolExecutor)