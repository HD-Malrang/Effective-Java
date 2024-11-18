# ITEM 8. finalizer와 cleaner 사용을 피하라

> **cleaner(자바 8까지는 finalized)는 안전망 역할이나 중요하지 않은 네이티브 자원 회수용으로만 사용하자. 물론 이런 경우라도 불확실성과 성능 저하에 주의해야 한다.**

- cleaner 는 자바9 버전부터 새로 들어온 기능으로 finalizer와 같이 자원을 반납하는 기능이다.
- finalizer와 cleaner는 즉시 수행된다는 보장이 없다.
- finalizer와 cleaner는 실행되지 않을 수도 있다.
- finalizer 동작 중에 예외가 발생하면 정리 작업이 처리되지 않을 수도 있다.
- finalize와 cleaner는 심각한 성능 문제가 있다.
- finalizer는 보안 문제가 있다.
- **반납한 자원이 있는 클래스는 AutoCloseable을 구현하고 클라이언트에서 close()를 호출하거나 try-with-resource를 사용해야 한다.**
- Cleaner 사용법
  - 자원 반납용 안전망으로 사용할 수 있다.
    - PhantomReference를 사용한다.
    - 호출되리라는 보장이 없지만 없는 것 보다는 나을 수 있다.
  - 네이티브 피어 자원 회수
    - 단, 성능 저하를 감당할 수 있고 네이티브 피어가 심각한 자원을 가지고 있지 않을 때에만 해당한다.
    - 네이티브 피어가 사용하는 자원을 즉시 회수해야 한다면 close() 메소드를 호출해야 한다.