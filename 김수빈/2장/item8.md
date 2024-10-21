# finalizer와 cleaner 사용을 피하라

 

### 자바가 제공하는 두가지 객체 소멸자

 

## 1. finalizer

- 예측 할 수 없고, 상황에 따라 위험할 수 있어 일반적으로 불필요.

 - 오동작, 낮은 성능, 이식성 문제의 원인

- finalizer 스레드는 다른 애플리케이션 스레드보다 우선순위가 낮아서 실행될 기회를 제대로 얻지 못할 수 있다.

- finalizer 동작 중 발생한 예외는 무시되며, 처리할 작업이 남았더라도 그 순간 종료된다.

- finalizer를 사용한 클래스는 finalizer 공격에 노출되어 심각한 보안 문제를 일으킬 수 있다.

 

## 2. cleaner 

- 자바 9에서 finalizer 대안으로 소개

- 덜 위험하지만 여전히 예측할 수 없고, 느리고, 일반적으로 불필요

- 여전히 백그라운드에서 수행되며 가비지 컬렉터의 통제하에 있으니 즉각 수행되리라는 보장이 없다.

 
<br>

##  ** 위 두가지 대신 쓸 수 있는 AutoCloseable 클래스

- 사용 방법 : AutoCloseable을 구현해주고, 클라이언트에서 인스턴스를 다 쓰고 나면 close 메서드를 호출

(일반적으로 예외가 발생해도 제대로 종료되도록 try-with-resources 를 사용)
```java
public class AutoCloseableExample {
    public static void main(String[] args) {
        try (CustomResource customResource = new CustomResource()){
    		//close() 호출
        } catch (Exception e){
            e.printStackTrace();
        }
    }
}


public class CustomResource implements AutoCloseable {
    @Override
    public void close() throws Exception {
        System.out.println("CustomResource Closed!");
    }
}
```