# toString을 항상 재정의하라

- Java에서 `toString()` 메서드를 재정의하는 이유는 객체의 상태를 문자열로 표현하여 **유용한 정보를 제공**하고, **디버깅**을 쉽게 할 수 있기 때문이다.
- 기본적으로 `Object` 클래스에서 제공하는 `toString()` 메서드는 클래스의 이름과 해시코드를 포함한 문자열을 반환하지만, 이것은 우리가 실제로 객체에 대해 얻고 싶은 정보가 아닐 때가 많다.
- 따라서 구체적인 클래스에서는 **명확하고 읽기 쉬운 정보를 반환하도록** `toString()`을 재정의하는 것이 권장된다.
- 특히 디버깅과 로깅에서 유용하게 사용하기 위함. 객체가 어떤 상태에 있는지 명확하게 알 수 있음

## 사용 예시

```java
public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + "}";
    }
}
```

```java
Person person = new Person("Alice", 30);
System.out.println(person); // Person@74a14482 출력

Person person = new Person("Alice", 30);
System.out.println(person);// Person{name='Alice', age=30} 출력
```

---

## 결론

- `toString()`은 **객체의 중요한 정보를 명확하게 표현**하는 데 필수적
- **디버깅, 로깅, 테스트**에서 객체의 상태를 쉽게 확인하는 데 도움을 준다.