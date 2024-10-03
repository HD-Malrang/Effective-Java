# ITEM 2. 생성자에 매개변수가 많다면 빌더를 고려하라

> **생성자나 정적 팩터리가 처리해야 할 매개변수가 많다면 빌더 패턴을 선택하는 게 더 낫다.**<br/>매개변수 중 다수가 필수가 아니거나 같은 타입이면 특히 더 그렇다. 빌더는 점층적 생성자보다 클라이언트 코드를 읽고 쓰기가 훨씬 간결하고, 자바빈즈보다 훨씬 안전하다.

- [점층적 생성자 패턴(생성자 체이닝)](#점층적-생성자-패턴생성자-체이닝)
- [자바빈즈 패턴](#자바빈즈-패턴)
- [빌더 패턴](#빌더-패턴)
- [계층형 빌더 패턴](#계층형-빌더-패턴)

<br/><br/>

## 점층적 생성자 패턴(생성자 체이닝)
- 매개변수를 늘려가며 생성자를 생성하는 방식이다.
- 중복되는 코드를 줄일 수 있고 코드가 비교적 덜 지저분하다.
- 매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵고, 함수를 사용할 때마다 사용한 함수의 코드를 확인해야하는 번거로움이 있다.
   ```java
    /** 점층적 생성자 패턴 적용 이전 코드 **/
  
    public class NutritionFacts {
        private final int servingSize;  // (mL, 1회 제공량)     필수
        private final int servings;     // (회, 총 n회 제공량)   필수
        private final int calories;     // (1회 제공량당)        선택
        private final int fat;          // (g/1회 제공량)       선택
        private final int sodium;       // (mg/1회 제공량)      선택
        private final int carbohydrate; // (g/1회 제공량)       선택
        
        public NutritionFacts(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
            this.calories = 0;
            this.fat = 0;
            this.sodium = 0;
            this.carbohydrate = 0;
        }
        
        public NutritionFacts(int servingSize, int servings, int calories) {
            this.servingSize = servingSize;
            this.servings = servings;
            this.calories = calories;
            this.fat = 0;
            this.sodium = 0;
            this.carbohydrate = 0;
        }
        
        public NutritionFacts(int servingSize, int servings, int calories, int fat) {
            this.servingSize = servingSize;
            this.servings = servings;
            this.calories = calories;
            this.fat = fat;
            this.sodium = 0;
            this.carbohydrate = 0;
        }
        
        public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
            this.servingSize = servingSize;
            this.servings = servings;
            this.calories = calories;
            this.fat = fat;
            this.sodium = sodium;
            this.carbohydrate = 0;
        }
        
        public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
            this.servingSize = servingSize;
            this.servings = servings;
            this.calories = calories;
            this.fat = fat;
            this.sodium = sodium;
            this.carbohydrate = carbohydrate;
        }
        
        
        public static void main(String[] args) {
            NutritionFacts cocaCola = new NutritionFacts(10, 10);
        }
    
    }
    ```
    ```java
    /** 점층적 생성자 패턴 적용 코드 **/
  
    public class NutritionFacts {
        private final int servingSize;  // (mL, 1회 제공량)     필수
        private final int servings;     // (회, 총 n회 제공량)   필수
        private final int calories;     // (1회 제공량당)        선택
        private final int fat;          // (g/1회 제공량)       선택
        private final int sodium;       // (mg/1회 제공량)      선택
        private final int carbohydrate; // (g/1회 제공량)       선택
        
        public NutritionFacts(int servingSize, int servings) {
            this(servingSize, servings, 0);
        }
        
        public NutritionFacts(int servingSize, int servings, int calories) {
            this(servingSize, servings, calories, 0);
        }
        
        public NutritionFacts(int servingSize, int servings, int calories, int fat) {
            this(servingSize, servings, calories, fat, 0);
        }
        
        public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
            this(servingSize, servings, calories, fat, sodium, 0);
        }
        
        public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
            this.servingSize = servingSize;
            this.servings = servings;
            this.calories = calories;
            this.fat = fat;
            this.sodium = sodium;
            this.carbohydrate = carbohydrate;
        }
        
        
        public static void main(String[] args) {
            NutritionFacts cocaCola = new NutritionFacts(10, 10);
        }
    
    }
    ```
** 지금은 인텔리제이의 cmd+P를 통한 파라미터 확인이나 파라미터 입력 시 자동으로 어떤 파라미터에 어떤 값이 들어가는 지를 보여주는 기능들이 있다. 하지만 해당 기능은 지원한지 오래되지 않았고, 현재도 해당 기능을 지원하지 않는 IDE도 있다.

## 자바빈즈 패턴
- 매개변수가 없는 생성자로 객체를 만든 후, setter 메서드를 호출해 원하는 매개변수의 값을 설정한다.
- 장점
  - 객체 생성이 간단해진다.
- 단점
  - 객체가 완전히 생성되기 전까지는 일관성(consistency)이 무너진 상태에 놓이게 되고, 필수 값을 받지 못하는 상황에 처할 수도 있는 위험을 감수해야한다.
  - 해당 객체를 사용할 때 어느 값까지 세팅을 해줘야 완전한 상태로 일관성이 보장되는지 알기가 어렵다. 주석을 남기고 문서화하는 방법밖에 없는데 이 방법은 좋은 방법이 아니다.
  - setter를 열어주기 때문에 불변 객체로 만들 수가 없다.
- 예시
    ```java
    /** 자바빈즈 패턴 **/
    /** 일관성이 깨지고, 불변으로 만들 수 없다. **/
  
    public class NutritionFacts {
      // 필드(기본값이 있다면) 기본값으로 초기화 된다.
        private final int servingSize   = -1;   // (mL, 1회 제공량)     필수; 기본값 없음
        private final int servings      = -1;   // (회, 총 n회 제공량)   필수; 기본값 없음
        private final int calories      = 0;    // (1회 제공량당)        선택
        private final int fat           = 0;    // (g/1회 제공량)       선택
        private final int sodium        = 0;    // (mg/1회 제공량)      선택
        private final int carbohydrate  = 0;    // (g/1회 제공량)       선택
      
        public NutritionFacts() { }
      
        // Setter
        public void setServingSize(int val) { servingSize = val; }
        public void setServings(int val) { servings = val; }
        public void setCalories(int val) { calories = val; }
        public void setFat(int val) { fat = val; }
        public void setSodium(int val) { sodium = val; }
        public void setCarbohydrate(int val) { carbohydrate = val; }
  
        public static void main(String[] args) {
            NutritionFacts cocaCola = new NutritionFacts();
            cocaCola.setServingSize(240);
            cocaCola.setServings(8);
            cocaCola.setCalories(100);
            cocaCola.setFat(35);
            cocaCola.setSodium(35);
            cocaCola.setCarbohydrate(27);
        }
  
    }
    ```
- 여기서 절충안으로 아래 코드와 같이 점층적 생성자 패턴과 자바빈즈 패턴을 혼합하여 어느정도 단점을 상쇄할 수 있는데 여전히 setter가 열려있어 불변 객체로 만들 수는 없다는 단점이 있다.
  ```java
    /** 점층적 생성자 패턴과 자바빈즈 패턴 혼합 **/
  
    public class NutritionFacts {
        // 필수 매개변수
        private final int servingSize;         // (mL, 1회 제공량)     필수; 기본값 없음
        private final int servings;            // (회, 총 n회 제공량)   필수; 기본값 없음
        
        // 선택 매개변수 - 기본값으로 초기화 한다.
        private final int calories     = 0;    // (1회 제공량당)        선택
        private final int fat          = 0;    // (g/1회 제공량)       선택
        private final int sodium       = 0;    // (mg/1회 제공량)      선택
        private final int carbohydrate = 0;    // (g/1회 제공량)       선택
        
        public NutritionFacts(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }
        
        // Setter
        public void setCalories(int val) { calories = val; }
        public void setFat(int val) { fat = val; }
        public void setSodium(int val) { sodium = val; }
        public void setCarbohydrate(int val) { carbohydrate = val; }
    
        public static void main(String[] args) {
            NutritionFacts cocaCola = new NutritionFacts(240, 8);
            cocaCola.setCalories(100);
            cocaCola.setFat(35);
            cocaCola.setSodium(35);
            cocaCola.setCarbohydrate(27);
        }
    
    }
    ```
- 책에서는 추가로 freezing 이라는 방법을 소개하기는 했는데, 책에서도 해당 방법은 다루기가 어려워 실전에서는 거의 쓰이지 않는다고 한다. 사용한다고 하더라도 객체 사용전에 freeze 메서드를 확실히 호출했는지를 보장할 방법이 없어 런타임오류에 취약하다고 쓰여있다.

## 빌더 패턴
- 빌더 패턴을 사용하는 경우 아래 코드 main매서드에서 활용하는 것처럼 Fluent API(또는 메소드 체이닝)로 작성할 수 있게된다.
- 장점
  - 필수 속성에 해당하는 것들을 설정할 수 있어 자바빈즈보다 안전하게 객체를 만들 수 있다.
  - 옵션 속성에 해당하는 것들은 넣어도 되고 안넣어도 되는데 최종적으로 빌드를 호출한 후에야 해당 객체를 사용할 수 있게 되기 때문에 완전한 상태의 일관성도 확보할 수 있다.
  - 즉 생성자의 매개변수는 줄어들고 필수적인 매개변수만 남으며, 옵션값들은 부가적으로 사용할 수 있게되면서 객체도 안전하게 사용할수 있다.
- 단점
  - 코드를 이해하기 어렵게 만든다.
  - 코드가 중복되는 것들이 있다.
  - 코드가 길다.
- 필수 속성, 옵션 속성이 둘 다 있고 객체를 이뮤터블(immutable, 생성 후 그 상태가 변하지 않는)한 객체를 만들고 싶을 때 빌더 패턴을 활용하면 좋다.
- 추가로 책에는 나와있지 않지만 Lombok을 활용하면 코드를 줄일 수 있는 방법이 있다.
- 예시
    ```java
    /** 빌더 패턴 **/
  
    public class NutritionFacts {
    
        private final int servingSize;  // (mL, 1회 제공량)     필수
        private final int servings;     // (회, 총 n회 제공량)   필수
        private final int calories;     // (1회 제공량당)        선택
        private final int fat;          // (g/1회 제공량)       선택
        private final int sodium;       // (mg/1회 제공량)      선택
        private final int carbohydrate; // (g/1회 제공량)       선택
        
        public static class Builder {
            // 필수 매개변수
            private final int servingSize;
            private final int servings;
            
            // 선택 매개변수 - 기본값으로 초기화 한다.
            private final int calories     = 0;
            private final int fat          = 0;
            private final int sodium       = 0;
            private final int carbohydrate = 0;
            
            public Builder(int servingSize, int servings) {
                this.servingSize = servingSize;
                this.servings = servings;
            }
            
            public Builder calorise(int val)
            { calories = val;   return this; }
            
            public Builder fat(int val)
            { fat = val;   return this; }
            
            public Builder sodium(int val)
            { sodium = val;   return this; }
            
            public Builder carbohydrate(int val)
            { carbohydrate = val;   return this; }
            
            public NutritionFacts builder() { return new NutritionFacts(this); }
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
            NutritionFacts cocaCola = new Builder(240, 8)
                            .setCalories(100)
                            .setSodium(35)
                            .setCarbohydrate(27)
                            .build();
        }
    
    }
    ```
    ```java
    /** 빌더 패턴(Lombok 사용) **/
  
    import lombok.Builder;
    
    @Builder
    public class NutritionFacts {
    
        private final int servingSize;  // (mL, 1회 제공량)     필수
        private final int servings;     // (회, 총 n회 제공량)   필수
        private final int calories;     // (1회 제공량당)        선택
        private final int fat;          // (g/1회 제공량)       선택
        private final int sodium;       // (mg/1회 제공량)      선택
        private final int carbohydrate; // (g/1회 제공량)       선택
    
        public static void main(String[] args) {
            NutritionFacts cocaCola = new NutritionFactsBuilder()
                            .setServingSize(100)
                            .setServings(10)
                            .setCalories(100)
                            .setSodium(35)
                            .setCarbohydrate(27)
                            .build();
                            
            NutritionFacts cocaCola2 = new NutritionFacts(100, 10, 100, 35, 27); // Builder 어노테이션 사용에 따라 가능해진 코드
        }
    
    }
    ```
  ** Lombok 사용 시 코드가 굉장히 간결해지는 장점이 있으나 해당 어노테이션을 설정하는 순간 기본적으로 모든 매개변수를 받는 생성자가 생기게되어 외부에서 빌더가 아닌 그냥 생성자로도 해당 객체를 만들 수 있게된다는 점과 필수 매개변수를 지정할 수 없다는 점이 단점이다.


## 계층형 빌더 패턴
- 빌더를 상속 계층에서 만들었을 때 유용하게 사용할 수 있는 장치인 self()
  ```java
    public abstract class Pizza {
        public enum Topping { HAM, MUSHROOM, ONION, PEPPER, SAUSAGE }
        final Set<Topping> toppings;
    
        abstract static class Builder<T extends Builder<T>> {
            EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
            public T addTopping(Topping topping) {
                toppings.add(Objects.requireNonNull(topping));
                return self();
            }
    
            abstract Pizza build();
    
            // 하위 클래스는 이 메서드를 재정의(overriding)하여
            // "this"를 반환하도록 해야 한다.
            protected abstract T self();
        }
        
        Pizza(Builder<?> builder) {
            toppings = builder.toppings.clone(); // 아이템 50 참조
        }
    }
    ```
- return값에 this를 사용하여 구현할 수도 있는데 이 경우 this 가 반환하는 것은 상위타입인 Pizza의 빌더로 상위 타입관련 자원만 사용할 수 있게 된다. 이렇게 되면 하위타입(서브클래스) 고유의 소스들은 사용할 수 없게 되어 self() 코드를 더 지향한다.
    ```java
    public abstract class Pizza {
        public enum Topping { HAM, MUSHROOM, ONION, PEPPER, SAUSAGE }
        final Set<Topping> toppings;
    
        abstract static class Builder<T extends Builder<T>> {
            EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
            public Builder<T> addTopping(Topping topping) {
                toppings.add(Objects.requireNonNull(topping));
                return this;
            }
    
            abstract Pizza build();
    
            // 하위 클래스는 이 메서드를 재정의(overriding)하여
            // "this"를 반환하도록 해야 한다.
            protected abstract T self();
        }
        
        Pizza(Builder<?> builder) {
            toppings = builder.toppings.clone(); // 아이템 50 참조
        }
    }
    ```