# ITEM 6. 불필요한 객체 생성을 피하라 

## 문자열

- 자바에서는 JVM이 내부적으로 문자열을 다 풀어서 캐싱을 하고있다. 따라서 문자열을 사용할 때마다 매번 새로 만들 필요가 없다.
- 이에 new String("")을 사용하지 않고 문자열 리터럴("")을 사용해 기존의 동일한 문자열을 재사용하는 것이 좋다.
- 같은 문자열임을 판단하는 경우 equals() 사용을 권장한다.
```java
public class Strings { 
    public static void main(String[] args) {
        String hello = "hello";
        String hello2 = new String("hello");        // 이 방법은 권장하지 않음.
        String hello3 = "hello";
    
        System.out.println(hello == hello2);        // false - 같은 문자열임에도 불구하고 인스턴스가 다르기 때문에 false로 나온다
        System.out.println(hello.equals(hello2));   // true
        System.out.println(hello == hello3);        // true
        System.out.println(hello.equals(hello3));   // true
    }
}
```

## 정규식(Pattern)
- 생성 비용이 비싼 객체라서 반복해서 생성하기 보다, 캐싱(필드로 선언)해서 재사용하는 것이 좋다.

## 오토 박싱(auto boxing)
- 기본타입을 그게 상응하는 박싱된 기본타입으로 상호 변환해주는 기술로 기본 타입과 박싱된 기본타입을 섞어서 사용하면 변환하는 과정에서 불필요한 객체가 생성될 수 있다.

### **"객체 생성은 비싸니 피하라."는 뜻으로 오해하면 안 된다.**