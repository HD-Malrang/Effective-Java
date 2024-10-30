# try-finally 보다는 try-with-resources를 사용하라
- 자바 라이브러리에는 InputStream, Connection 등 close 메서드를 호출해 직접 닫아줘야 하는 자원이 많다. 
- 자원을 닫는 것은 놓치기 쉬워 예측하기 어려운 성능 문제로 이어지기도 한다. 
- 상당 수가 안정망으로 finalizer, cleaner 등을 활용하지만 실행을 보장하지 못한다.

### try-finally
```java
static String firstLineOfFile(String path) throws IOException {
  BufferedReader br = new BufferedReader(new FileReader(path));
  try {
    br.readLine();
  } finally {
    br.close();
  }
  return "";
}

static void copy(String src, String dst) throws IOException {
  InputStream in = new FileInputStream(src);
  try {
    OutputStream out = new FileOutputStream(dst);
    try {
      byte[] buf = new byte[1024];
      int n;
      while ((n = in.read(buf)) >= 0)
        out.write(buf, 0, n);
    } finally {
      out.close();
    }
  } finally {
    in.close();
  }
}
```
finally 블록은 try 블록에서 예외가 발생해도 실행하기 때문에 close에서 발생한 예외는 기록되지 않고, readLine에서 발생한 예외만 기록된다. <br>즉, 첫 번째 예외에 대한 정보는 남지않기 때문에 디버깅이 어려워 진다.

### try-with-resources
```java
static String firstLineOfFile(String path) throws IOException {
  try (BufferedReader br = new BufferedReader(new FileReader(path))) {
    br.readLine();
  }
  return "";
}

static void copy(String src, String dst) throws IOException {
  try (InputStream in = new FileInputStream(src); OutputStream out = new FileOutputStream(dst)) {
    byte[] buf = new byte[1024];
    int n;
    while ((n = in.read(buf)) >= 0)
      out.write(buf, 0, n);
  }
}
```
- 코드가 간결해지고, 이중 try 구문도 가독성이 좋아졌다. 하지만, AutoCloseable 구현이 없으므로 해당 기능으론 close에 대한 보장이 되지않는다.
- finally와의 차이점은 try 안에 파라미터로 자원을 넘겨주는 것이다. 또한 try 구문안에 여러 자원을 넣을 수 있다. 마찬가지로 catch 문도 사용이 가능하다.
---
#### 회수해야 하는 자원을 다루는 모든 경우에서 try-finally가 아닌 try-with-resources를 사용하자. <br>코드가 더 짧아지고 가독성이 높아지며, 정확하고 쉽게 자원을 회수할 수 있다. <br>또한 close를 보장하고 싶은 경우 Closeable 인터페이스를 구현하자.