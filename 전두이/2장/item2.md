# 생성자에 매개변수가 많다면 빌더를 고려하라

정적 팩터리와 생성자에는 공통적으로 선택적 매개변수가 많을 때 적절히 대응하기가 어렵다는 제약이 있다.

선택적 매개변수는 객체를 생성할 때 필요에 따라 제공될 수 있는 추가적인 값들로, 이러한 경우 객체 생성 메서드가 복잡해지거나 비효율적일 수 있다. 이 문제를 좀 더 구체적으로 살펴보자

---

## **점층적 생성자 패턴 (telescoping constructor pattern)**

> 확장하기 어렵다.
>

### 특징

- 여러 개의 생성자를 정의하여 객체를 생성할 때 필요한 다양한 조합의 매개변수를 지원하는 패턴
- 이 패턴에서는 각 생성자가 다른 매개변수 조합을 처리하며, 매개변수의 수가 많아질수록 생성자 목록이 점차 증가

예시

```java
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public NutritionFacts(int servingSize, int servings) {
        this(servingSize, servings, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories) {
        this(servingSize, servings, calories, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat) {
        this(servingSize, servings, calories,  fat, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
        this(servingSize, servings, calories,  fat,  sodium, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
        this.servingSize = servingSize;
        this.servings = servings;
        this.calories = calories;
        this.fat = fat;
        this.sodium = sodium;
        this.carbohydrate = carbohydrate;
    }
}
```

### 문제점

- **많은 생성자**: 매개변수의 조합이 많아질수록 생성자의 수가 증가한다. 각 생성자는 다른 조합의 매개변수를 처리하므로, 생성자의 목록이 점점 길어지게됨
- **매개변수 순서 실수**: 생성자가 매개변수를 순서대로 받아들여야 하므로, 호출 시 매개변수의 순서를 잘못 입력하는 실수가 발생할 수 있음
- **유지보수의 어려움**: 생성자 수가 많아지면 코드가 복잡해지며, 새로운 매개변수 조합을 추가하거나 기존 코드를 수정하는 데 어려움이 있음

---

## 자바빈즈 패턴 (**JavaBeans pattern**)

> 일관성이 깨지고, 불변으로 만들 수 없다.
>

### 특징

- 매개변수가 없는 생성자로 객체를 생성한 후, setter 메서드를 호출 해 원하는 매개변수 값을 설정하는 방식
- Java에서 객체를 만들고 조작하는 데 널리 사용되는 디자인 패턴이다.

예시

```java
public class NutritionFacts {
    private int servingSize = -1;
    private int servings = -1;
    private int calories = 0;
    private int fat = 0;
    private int sodium = 0;
    private int carbohydrate = 0;

    public NutritionFacts() {}

    public void setServingSize(int servingSize) {
        this.servingSize = servingSize;
    }

    public void setServings(int servings) {
        this.servings = servings;
    }

    public void setCalories(int calories) {
        this.calories = calories;
    }

    public void setFat(int fat) {
        this.fat = fat;
    }

    public void setSodium(int sodium) {
        this.sodium = sodium;
    }

    public void setCarbohydrate(int carbohydrate) {
        this.carbohydrate = carbohydrate;
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        NutritionFacts cocaCola = new NutritionFacts();
        cocaCola.setServingSize(240);
        cocaCola.setServings(8);
        cocaCola.setCalories(100);
        cocaCola.setFat(0);
        cocaCola.setSodium(35);
        cocaCola.setCarbohydrate(27);
    }
}
```

### 문제점

- **제한된 기능**: 자바빈즈 패턴은 기본적인 데이터 캡슐화와 접근 방식을 제공하지만, 복잡한 비즈니스 로직이나 고급 기능을 처리하는 데는 적합하지 않을 수 있다.
- **성능 저하**: `getter`와 `setter` 메서드가 지나치게 많아지면, 성능 저하를 일으킬 수 있다. 많은 속성을 가진 클래스에서는 `getter`와 `setter` 메서드의 사용이 코드의 복잡성을 증가시킬 수 있다.
- **일관성이 깨짐**: 객체 하나를 만들려면 메서드를 여러 개 호출해야 하고, 객체가 완전히 생성되기 전까지는 `일관성(consistency)`이 무너진 상태에 놓이게 된다. 이는 버그 때문에 런타임에 문제를 겪는 코드가 버그를 심은 코드와 물리적으로 멀리 떨어져 있기 때문에 디버깅도 어렵다.

---

## 빌더 패턴 (Builder **pattern**)

> 점층적 생성자 패턴과 자바빈즈 패턴의 장점만 취했다.
>

### 특징

- 이 패턴은 생성자가 많은 매개변수를 요구하거나 선택적 매개변수가 많은 경우, 객체를 보다 단계적으로 생성할 수 있게 해줌
- 이 패턴은 특히 객체의 상태가 `불변(immutable)`일 때 유용하며, 클라이언트가 명확하고 가독성 높은 코드를 작성할 수 있게 돕는다.

예시

```java
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    private NutritionFacts(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }

    public static class Builder {
        // 필수 매개변수
        private final int servingSize;
        private final int servings;

        // 선택 매개변수 - 기본값으로 초기화
        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        // 필수 매개변수만을 담은 Builder 생성자
        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        public Builder calories(int val) {
            calories = val;
            return this;
        }

        public Builder fat(int val) {
            fat = val;
            return this;
        }
        
        public Builder sodium(int val) {
            sodium = val;
            return this;
        }
        
        public Builder carbohydrate(int val) {
            carbohydrate = val;
            return this;
        }
       
        // 최종 객체 생성
        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }
}
```

빌더를 사용하여 `NutritionFacts` 객체를 다음과 같이 생성할 수 있습니다. 이 빌더의 setter 메서드는 빌더 자신을 반환하기 때문에 연쇄적으로 호출할 수 있다. `(method chaining)`

```java
NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
    .calories(100)
    .sodium(35)
    .carbohydrate(27)
    .build();

```

### 빌더 패턴의 장점

1. **가독성:** 매개변수가 많고 선택적인 경우에도 객체 생성 코드를 읽기 쉬움
2. **유연성**: 선택적 매개변수를 쉽게 설정할 수 있으며, 객체의 상태를 불변으로 유지하면서 필요한 매개변수만 설정할 수 있음
3. **안전성**: 불변 객체를 만들 수 있으며, 매개변수 검증을 빌더에서 수행하여 객체 생성 시 잘못된 상태를 방지할 수 있다.
4. **디자인 유연성**: 객체를 생성할 때의 복잡성이 낮으며, 다양한 조합의 매개변수를 사용하여 객체를 생성할 수 있다.

아래와 같이 Lombok의 @Builder 어노테이션을 사용하여 NutritionFacts 클래스를 리팩토링 하면 훨씬 코드가 간결해짐

```java
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

- 이 어노테이션을 사용하면 Lombok이 빌더 패턴을 구현해준다. 필수 매개변수와 선택적 매개변수를 설정할 수 있는 빌더 클래스를 자동으로 생성한다.
- 이 방식은 코드의 가독성을 높이고, 유지보수성을 개선하는 데 도움을 줌

### 계층적으로 설계된 클래스와 잘 어울리는 빌더 패턴 예시

Pizza 클래스

```java
public abstract class Pizza{
   public enum Topping { HAM, MUSHROOM, ONION, PEPPER, SAUSAGE }
   final Set<Topping> toppings;

   abstract static class Builder<T extends Builder<T>> {
      EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
      public T addTopping(Topping topping) {
         toppings.add(Objects.requireNonNull(topping));
         return self();
      }

      abstract Pizza build();

      // 하위 클래스는 이 메서드를 overriding하여 this를 반환하도록 해야 한다.
      protected abstract T self();
   }

   Pizza(Builder<?> builder) {
      toppings = builder.toppings.clone();
   }
}

```

- 피자의 공통된 속성을 정의하고, 피자를 생성하기 위한 기본적인 빌더 클래스를 제공하는 추상 클래스

NyPizza 클래스

```java
public class NyPizza extends Pizza {
   public enum Size { SMALL, MEDIUM, LARGE }
   private final Size size;

   public static class Builder extends Pizza.Builder<Builder> {
      private final Size size;

      public Builder(Size size) {
         this.size = Objects.requireNonNull(size);
      }

      @Override public NyPizza build() {
         return new NyPizza(this);
      }

      @Override protected Builder self() { return this; }
   }

   private NyPizza(Builder builder) {
      super(builder);
      size = builder.size;
   }
}
```

- `NyPizza`는 `Pizza`의 서브 클래스이며, 뉴욕 스타일의 피자를 모델링

Calzone 클래스

```java
public class Calzone extends Pizza {
   private final boolean sauceInside;

   public static class Builder extends Pizza.Builder<Builder> {
      private boolean sauceInside = false;

      public Builder sauceInside() {
         sauceInside = true;
         return this;
      }

      @Override public Calzone build() {
         return new Calzone(this);
      }

      @Override protected Builder self() { return this; }
   }

   private Calzone(Builder builder) {
      super(builder);
      sauceInside = builder.sauceInside;
   }
}
```

계층적 빌더를 사용하는 클라이언트 코드 사용 예시

```java
public class Main {
    public static void main(String[] args) {
        NYPizza pizza = new NYPizza.Builder(SMALL)
                .addTopping(SAUSAGE)
                .addTopping(ONION)
                .build();

        Calzone calzone = new Calzone.Builder()
                .addTopping(HAM)
                .sauceInside()
                .build();
    }
}
```

- **점층적 생성자 문제 해결**: 빌더 패턴을 사용함으로써 많은 선택적 매개변수를 갖는 생성자를 관리하는 복잡성을 줄일 수 있다. 각 클래스는 필요한 필드만을 포함하는 빌더를 제공하므로, 클라이언트 코드는 원하는 속성만을 설정하여 객체를 생성할 수 있다.
- **빌더 패턴 사용**: `NYPizza`와 `Calzone` 객체는 빌더 패턴을 사용하여 생성됨. 빌더 패턴을 통해 객체 생성 시 다양한 선택적 매개변수를 유연하게 설정할 수 있다.
- **구체적인 객체 생성**: 빌더 패턴은 객체를 생성하는데 필요한 설정을 체이닝 방식으로 지정할 수 있게 해줌. 이로 인해 클라이언트 코드에서 객체 생성의 복잡성을 줄일 수 있다.
- **불변성 유지**: `NYPizza`와 `Calzone` 객체는 생성 후 변경할 수 없는 불변 객체이다. 이는 객체의 일관성을 유지하며, 멀티스레드 환경에서 안전하게 사용할 수 있다.

이 구조는 복잡한 객체를 안전하고 유연하게 생성할 수 있게 해주며, 특히 다양한 옵션을 가진 객체를 만들 때 유용함. 빌더 패턴을 사용하면 코드의 가독성과 유지보수성이 높아지고, 객체 생성 과정에서의 오류를 줄일 수 있다.

---

## 결론

생성자나 정적 팩터리가 처리해야 할 매개변수가 많다면 빌더 패턴을 선택하는 게 더 낫다.

특히, 매개변수 중 다수가 필수가 아니거나 같은 타입이면 더욱 그렇다.