# ITEM 9. try-finally 보다 try-with-resouces를 사용하라

> **꼭 회수해야하는 자원을 다룰 때는 try-finally 말고, try-with-resources를 사용하자. 예외는 없다. 코드는 더 짧고 분명해지고, 만들어지는 예외 정보도 훨씬 유용하다. try-finally로 작성하면 실용적이지 못할 만큼 코드가 지저분해지는 경우라도, try-with-resources로는 정확하고 쉽게 자원을 회수할 수 있다.**

## Try-Finally

- try-finally는 더이상 최선의 방법이 아니다.(자바7부터)
- 이전에는 try-finally 구문을 이용해서 자원을 회수하는 경우가 많았는데, 회수해야 할 자원이 많아지면 코드가 복잡해지면서 가독성이 나빠지고 자원을 제대로 회수하지 못할 수 있다.

```java
// 코드 9-1 : 간단한 try-finally 예제
// 문제 없이 정상동작한다.
static String firstLineOfFile(String path) throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(path));
    try {
        return br.readLine();
    }finally {
        br.close();
    }
}
```

```java
// 코드 9-2 : 회수해야할 자원이 둘 이상인 try-finally 예제
// 코드가 복잡해져 가독성이 나빠진다.
public class Copy {

    private static final int BUFFER_SIZE = 8 * 1024;

    static void copy(String src, String dst) throws IOException {

        FileInputStream in = new FileInputStream(src);
        try {
            FileOutputStream out = new FileOutputStream(dst);
            try {
                byte[] buf = new byte[BUFFER_SIZE];
                int n;
                while ((n = in.read(buf)) >= 0)
                    out.write(buf, 0, n);
            }finally {
                out.close();
            }
        }finally {
            in.close();
        }

    }

}
```

```java
// 단일 try-finally로 구성한 예제
// 오류 발생 시 자원이 제대로 회수되지 않을 수 있다.
static void copyLeak(String src, String dst) throws IOException {
    FileInputStream in = new FileInputStream(src);
    FileOutputStream out = new FileOutputStream(dst);
    try {
        byte[] buf = new byte[BUFFER_SIZE];
        int n;
        while ((n = in.read(buf)) >= 0)
            out.write(buf, 0, n);
    }finally {
        // 여기서 에러 발생하면
        out.close();
        // 이건 실행 안됨.
        in.close();
    }
}
```

## Try-With-Resources

- try-with-resources를 사용하면 코드가 더 짧고 분명하다.

```java
static String firstLineOfFileWithResources(String path) throws IOException {
    try(BufferedReader br = new BadBufferReader(new FileReader(path));) {
        return br.readLine();
    }
}
```

- 만들어지는 예외 정보도 훨씬 유용하다.
    - try-finally로 작성할 경우, 첫번째 발생한 예외는 보여주지 않고, 마지막 예외만 보여지는 형태가 된다
    - 하지만 실제로는 첫번째 발생한 예외가 후속 예외를 발생시키는 경우가 많아 어떤 지점에서 예외가 발생되었는지를 명확하게 파악하기 위해서는 첫번째 발생한 예외가 중요하다.
    - try-with-resources 모든 예외를 보여준다.
