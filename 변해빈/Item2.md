## Item 2. 생성자에 매개변수가 많다면 빌더를 고려하라

- 빌더 패턴을 사용하는 클라이언트는 **빌더 객체**를 얻어서 객체를 생성한다.
  - 클라이언트는 빌더 객체를 최초로 생성할 때 **필수 파라미터**를 넘겨야 한다.
  - 클라이언트가 상황에 맞게 필요한 **옵셔널 파라미터**를 주입하려면, 빌더 객체의 setter와 비슷한 메서드를 호출하면 된다.
  - 마지막으로, 클라이언트는 객체를 얻기 위해 빌드 완성 메서드를 호출하면 된다.



## 1. 빌더 패턴 만들기

- 가장 먼저 필요한 것은, 빌더 클래스를 따로 만드는 것이다. 이 클래스는 빌드하려는 <u>객체와 강하게 연관되어 있으므로 내부 정적 클래스로 정의한다.

```java
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        // 필수 매개변수
        private final int servingSize;
        private final int servings;

        // 선택 매개변수 - 기본값으로 초기화한다.
        private int calories      = 0;
        private int fat           = 0;
        private int sodium        = 0;
        private int carbohydrate  = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings    = servings;
        }

        public Builder calories(int val)
        { calories = val;      return this; }
        public Builder fat(int val)
        { fat = val;           return this; }
        public Builder sodium(int val)
        { sodium = val;        return this; }
        public Builder carbohydrate(int val)
        { carbohydrate = val;  return this; }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize  = builder.servingSize;
        servings     = builder.servings;
        calories     = builder.calories;
        fat          = builder.fat;
        sodium       = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }

    public static void main(String[] args) {
        NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
                .calories(100).sodium(35).carbohydrate(27).build();
    }
}
```

빌더 객체를 만드는 방법은 다음 4가지 과정을 거친다.

>1. 빌더 객체를 사용하려면 가장 먼저 필수 파라미터를 입력하도록 한다.
>2. 옵셔널 파라미터는 선택적으로 호출되도록 한다.
>3. 옵셔널 세팅 작업이 완료되면 완성 메서드를 호출한다.
>4. 객체는 반드시 해당 객체의 Builder 객체로만 생성할 수 있도록 한다.


위와 같이 구현해도 괜찮지만, Lombok은 @Builder Annotaion을 제공하므로 더 직관적으로 만들 수 있다.
```
@Builder
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;
	
    // 사용 예시
    public static void main(String[] args) {
        NutritionFacts.builder()
                .servingSize()
                .servings()
                .calories()
                .fat()
                .sodium()
                .carbohydrate()
                .build();
    }
}
```

## 2. 코틀린에서 빌더 패턴 사용법

- 코틀린은 빌더 패턴이 필요 없다. 자바가 가진 단점들을 모두 커버할 수 있기 때문이다.
- 역시 코틀린 최고...

```kotlin
fun main() {
    val member1 = Member(name = "myname", email = "email");
    val member2 = Member(name = "myname", email = "email", height = 10);
    val member3 = Member(name = "myname", email = "email", weight = 20);
    val member4 = Member(name = "myname", email = "email", height = 10, weight = 30);

}
class Member(val name: String, 
             val email: String, 
             var height: Int? = null, 
             val weight: Int? = null) {
    init {
        height?.let {
            if (it <= 0) {
                throw IllegalArgumentException("height must be more than 0");
            }
        }
        weight?.let {
            if (it <= 0) {
                throw IllegalArgumentException("weight must be more than 0");
            }
        }
    }
}
```