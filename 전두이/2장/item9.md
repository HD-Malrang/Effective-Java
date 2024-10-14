# try-finally보다는 try-with-resources를 사용하라

자바에서는 꼭 회수해야 할 자원을 다룰 때 `try-with-resources`를 사용하는 것이 권장된다. 이 방식은 자원을 자동으로 닫아주며, 코드가 간결하고 명확하게 작성될 수 있다.

- **예외 처리의 효율성**: `try-with-resources`는 자원이 자동으로 해제될 뿐만 아니라, 예외가 발생했을 때 더 유용한 정보를 제공한다. 추가적인 예외 정보를 놓치지 않고 명확하게 처리할 수 있다.
- **코드 간결성**: `try-finally`와 비교하면 `try-with-resources`를 사용한 코드는 더 짧고 가독성이 좋다. 여러 자원을 다루거나, 예외 처리 과정에서 코드가 복잡해질 때도 `try-with-resources`는 실수 없이 정확하게 자원을 관리할 수 있다. 내부적으로는 **AutoCloseable** 인터페이스의 `close()` 메서드가 호출되면서 자원이 해제됨
- **비교 사례**: 복수 자원을 다루거나 자원을 반드시 닫아야 하는 상황에서 `try-finally`를 사용할 경우 코드가 지저분해지고 실수 가능성이 커지지만, `try-with-resources`를 사용하면 이러한 문제 없이 자원을 정확하게 회수할 수 있다.


### try-with-resources 사용 예시

`BufferedReader`, `Connection`과 같은 자원들은 실제로 파일, 네트워크, 데이터베이스 연결 등 외부 자원에 접근하는 객체들이고, 이들은 운영체제의 자원을 사용하기 때문에 사용 후에는 **명시적으로 닫아줘야** 한다.

자원이 닫히지 않으면 해당 자원에 대한 **파일 핸들**이나 **네트워크 소켓** 등이 계속 열려 있어, **메모리 누수**나 **자원 고갈**이 발생할 수 있다.

1. **파일 입출력 자원 (File I/O)**

파일을 읽고 쓰는 작업은 반드시 자원을 회수해야 한다. 파일이 닫히지 않으면 다른 프로세스에서 접근하지 못할 수 있고, 메모리 누수와 성능 저하를 초래할 수 있다.

```java
public void readFile(String filePath) {
    try (BufferedReader reader = new BufferedReader(new FileReader(filePath))) {
        String line;
        while ((line = reader.readLine()) != null) {
            System.out.println(line);
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

1. 데이터베이스의 연결 **(Database Connection)**

데이터베이스 연결도 반드시 자원을 해제해야 한다. 연결이 닫히지 않으면 데이터베이스에 과부하가 걸리거나, 새로운 연결을 할 수 없는 상황이 발생할 수 있다.

```java
public void executeQuery(String query) {
    try (Connection conn = DriverManager.getConnection(url, user, password);
         Statement stmt = conn.createStatement()) {
        ResultSet rs = stmt.executeQuery(query);
        while (rs.next()) {
            System.out.println(rs.getString("column_name"));
        }
    } catch (SQLException e) {
        e.printStackTrace();
    }
}
```

try-finally와 비교

```java
// try-finally 사용 예시
public void executeQueryWithFinally(String query) {
    Connection conn = null;
    Statement stmt = null;
    ResultSet rs = null;
    try {
        conn = DriverManager.getConnection(url, user, password);
        stmt = conn.createStatement();
        rs = stmt.executeQuery(query);
        while (rs.next()) {
            System.out.println(rs.getString("column_name"));
        }
    } catch (SQLException e) {
        e.printStackTrace();
    } finally {
        try {
            if (rs != null) rs.close();
            if (stmt != null) stmt.close();
            if (conn != null) conn.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}

```

이처럼 `try-with-resources`를 사용하면, 자원을 자동으로 닫는 작업을 **자동으로 처리해 주므로** 자원을 수동으로 닫을 필요가 없다. 즉, 별도의 `finally` 블록을 사용해 수동으로 자원을 닫지 않아도 된다는 의미이다.

### 결론

- 자바에서 자원을 다룰 때는 **항상 `try-with-resources`를 사용**하는 것이 더 안전하고, 효율적이다.
- 예외 처리와 자원 관리를 동시에 쉽게 구현할 수 있어, 실수나 누락을 방지하고 코드의 품질을 높일 수 있다.