# try-finally보다는 try-with-resources를 사용하라

`BufferedReader`, `Connection`과 같은 자원들은 실제로 파일, 네트워크, 데이터베이스 연결 등 외부 자원에 접근하는 객체들이고, 이들은 운영체제의 자원을 사용하기 때문에 사용 후에는 **명시적으로 닫아줘야** 한다.

자원이 닫히지 않으면 해당 자원에 대한 **파일 핸들**이나 **네트워크 소켓** 등이 계속 열려 있어, **메모리 누수**나 **자원 고갈**이 발생할 수 있다.

1. **파일 입출력 자원 (File I/O)**

파일을 읽고 쓰는 작업은 반드시 자원을 회수해야 한다. 파일이 닫히지 않으면 다른 프로세스에서 접근하지 못할 수 있고, 메모리 누수와 성능 저하를 초래할 수 있다.

1. **데이터베이스의 연결 (Database Connection)**

데이터베이스 연결도 반드시 자원을 해제해야 한다. 연결이 닫히지 않으면 데이터베이스에 과부하가 걸리거나, 새로운 연결을 할 수 없는 상황이 발생할 수 있다.

### try-finally와 try-with-resources

자바에서는 꼭 회수해야 할 자원을 다룰 때 `try-with-resources`를 사용하는 것이 권장된다. 이 방식은 자원을 자동으로 닫아주며, 코드가 간결하고 명확하게 작성될 수 있다.

- **예외 처리의 효율성**: `try-with-resources`는 자원이 자동으로 해제될 뿐만 아니라, 예외가 발생했을 때 더 유용한 정보를 제공한다. 추가적인 예외 정보를 놓치지 않고 명확하게 처리할 수 있다.
- **코드 간결성**: `try-finally`와 비교하면 `try-with-resources`를 사용한 코드는 더 짧고 가독성이 좋다. 여러 자원을 다루거나, 예외 처리 과정에서 코드가 복잡해질 때도 `try-with-resources`는 실수 없이 정확하게 자원을 관리할 수 있다.
- **자원의 자동 해제**: `AutoCloseable` 인터페이스를 구현한 자원을 사용하고 해제하는 경우에는, `try-with-resources` 구문에서 자원이 더 이상 사용되지 않을 때 자동으로 닫히게 해준다.
- 예를 들어 `BufferedReader`, `FileReader`, `InputStream`, `OutputStream`, `Connection` 등은 `AutoCloseable` 인터페이스를 구현하고 있기 때문에 `try-with-resources` 구문에서 사용할 수 있다.
- 만약 자원이 `AutoCloseable` 인터페이스를 구현하지 않았다면, `try-with-resources` 구문을 사용할 수 없다. 이런 경우에는 직접 구현해서 사용하면 된다.

### 사용 예시

BufferedReader 자원 사용 후 해제하는 방법

```java
// 방법 1.try-finally 구문에서는 자원을 수동으로 해제해야 함
BufferedReader br = null;
try {
    br = new BufferedReader(new FileReader(path));
    return br.readLine(); // 작업 수행
} finally {
    if (br != null) {
        try {
            br.close(); // 자원을 명시적으로 해제
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

- 여기서는 자원을 명시적으로 닫기 위해 `finally` 블록에서 `br.close()`를 호출한다.
- 추가로, `close()` 호출 자체도 예외를 던질 수 있기 때문에 또 한 번 `try-catch`로 감싸야 한다. 즉, 코드가 장황해지고 실수할 가능성이 커진다.

```java
// 방법 2.try-with-resources에서는 자원이 자동으로 해제됨
try (BufferedReader br = new BufferedReader(new FileReader(path))) {
    return br.readLine(); // 작업 수행
} catch (IOException e) {
    e.printStackTrace();
}
```

- 방법 1과 다르게 `try()`구문 안에서 BufferedReader 객체가 생성되었으며, 자원이 자동으로 해제되며 자원을 닫는 코드는 따로 작성할 필요가 없다.
- `br.close()`를 명시적으로 호출하지 않아도, `try` 블록이 끝날 때 자동으로 자원을 해제한다.

이처럼 `try-with-resources`를 사용하면, 자원을 닫는 작업을 **자동으로 처리해 주므로** 자원을 수동으로 닫을 필요가 없다.

### 결론

- 자바에서 자원을 다룰 때는 항상 `try-with-resources`를 사용하는 것이 더 안전하고, 효율적이다.
- `try-with-resources`: 자원이 자동으로 해제됨 (`close()` 호출이 자동)
- `try-finally`: 자원을 직접 해제해야 함 (`close()`를 명시적으로 호출해야 함)