# 불필요한 객체 생성을 피하라

똑같은 객체를 매번 생성하기보다는, 객체 하나를 재사용하는 것이 속도와 메모리 사용 측면에서 훨씬 효율적일 때가 많다.

아래는 주어진 문자열이 유효한 로마 숫자인지 확인하는 메서드를 작성한 예시이다.

```java
static boolean isRomanNume ral(String s) {
    return s.matches("(?=• )M*(C[MD] |D?C{0,3})" 
       + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
}
```

- `String.matches` 메서드는 내부적으로 `Pattern` 인스턴스를 사용한다.
- 다시 말해, `String.matches()`는 주어진 정규 표현식을 컴파일하여  `Pattern` 객체를 만들고, 그 패턴을 사용해 문자열이 정규식에 일치하는지 확인하는 과정을 수행한다.
- 이는 정규 표현식으로 문자열 형태를 확인하는 가장 쉬운 방법이지만, 성능이 중요한 상황에서 반복해서 사용하기에는 적합하지 않다.
- 이 메서드가 내부에서 만드는 정규표현식용 Pattern 인스턴스는, 한 번 쓰고 버려져서 곧바로 GC(Garbage Collection) 대상이 된다.
- Pattern은 입력받은 정규표현식에 해당하는 유한 상태 머신(finite state machine)을 만들기 때문에 인스턴스 생성 비용이 높다.
- 유한 상태 머신?
    - Parttern 클래스가 정규 표현식이 어떻게 문자열과 일치하는지 결정하는 처리 구조를 말함
    - 이는 문자열을 한 글자씩 처리하기 때문에 인스턴스 비용이 높아짐

성능을 개선하려면 Pattern 인스턴스 클래스 초기화 과정에서 캐싱해두고, 이를 나중에 재사용하면 된다.

```java
public class RomanNumerals { 
    private static final Pattern ROMAN = Pattern.compile(
            "시?=.)M시(:[MD] |D?C{0f3})" 
               + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$")；
		
    static boolean isRomanNume ral(String s) {
        return ROMAN.matcher(s).matches();
    }
}
```

- 정규표현식을 표현하는 (불변인) Pattern 인스턴스를 클래스 초기화(정적 초기화) 과정에서 직접 생성해 캐싱해두고, 나중에
  isRomanNumeral 메서드가 호출될 때마다 이 인스턴스를 재사용한다.

### 오토박싱과 언박싱

```java
Integer num = 10;  // int 타입인 10이 자동으로 Integer 객체로 변환
int n = num;  // Integer 객체 num이 자동으로 int로 변환
```

- **기본 타입(Primitive Types)**: Java의 원시 자료형으로, 성능을 최적화하기 위해 객체가 아닌 메모리 효율적인 방식으로 값을 저장
    - 예: `int`, `char`, `boolean`, `double` 등
- **박싱된 타입(Wrapper Types)**: 기본 타입을 객체로 감싸는 클래스로, 기본 타입을 객체로 다룰 수 있도록 해줌
    - 예: `Integer` (for `int`), `Character` (for `char`), `Boolean` (for `boolean`), `Double` (for `double`) 등

| 특성 | **Primitive Types** | **Wrapper Types** |
| --- | --- | --- |
| **메모리** | 스택에 값이 직접 저장 | 힙 메모리에 객체로 저장 |
| **초기화 값** | 0 | null |
| **null 허용 여부** | 불가능 | 가능 |
| **성능** | 더 빠름 | 더 느림 |
| **객체 기능 제공** | 제공되지 않음 | 다양한 메서드 제공 |
- **오토방식(Auto Boxing)**: 기본 타입 → 박싱된 타입으로 자동 변환되는 과정
- 오토박싱이 제공하는 편리함 뒤에는 **객체 생성 비용**이 숨어 있다. **박싱된 타입은 객체**이므로, 새로운 객체가 매번 생성되면 메모리와 성능에 영향을 미친다.
- 특히, **반복적으로 오토박싱**이 일어나는 경우 불필요한 객체가 다수 생성될 수 있으며, 이는 GC(Garbage Collection)의 부담을 가중시킬 수 있다.

## 결론

- 불필요한 객체 생성을 피하자
- 박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의하자