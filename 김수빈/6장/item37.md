## ordinal 인덱싱 대신 EnumMap을 사용하라

```java
class Plant {
    enum LifeCycIe {AMMUAL, PERENNIAL, BIEMMIAL}

    final String name;
    final LifeCycIe lifeCycle;

    Plant(String name, LifeCycIe lifeCycle) {
        this.name = name;
        this.lifeCycle = lifeCycle;
    }

    @Override
    public String toString() {
        return name;
    }
}
```

### ordinal() 을 배열 인덱스로 사용한 예 - 따라하면 안 됨

```java
public static void usingOrdinalArray(List<Plant> garden){
// 1.  배열과 제네릭의 호환성 문제
        Set<Plant>[]plantsByLifeCycle=(Set<Plant>[])new Set[LifeCycle.values().length];
        for(int i=0;i<plantsByLifeCycle.length;i++){
        plantsByLifeCycle[i]=new HashSet<>();
        }

        for(Plant plant:garden){
        // 2. 열거 타입의 ordinal 사용
        plantsByLifeCycle[plant.lifeCycle.ordinal()].add(plant);
        }

        for(int i=0;i<plantsByLifeCycle.length;i++){
        // 인덱스의 의미를 모르니 출력 결과에 직접 레이블을 달아야 한다
        System.out.printf("%s : %s%n",LifeCycle.values()[i],plantsByLifeCycle[i]);
        }
        }
```

제네릭 배열을 생성하지 못하는 이유

- ordinal() 사용시 :  정수는 열거 타입과 달리 타입 안전하지 않기때문에 정확한 정수값을 사용하는지 개발자가 직접 보증해야 한다. 잘못된 값을 사용하여 잘못된 동작을 하더라도 에러가 발생하지 않을 수
  있다.

### EnumMap 사용

```java
public static void usingEnumMap(List<Plant> garden){Map<LifeCycle, Set<Plant>>plantsByLifeCycle=new EnumMap<>(
        LifeCycle.class);

        for(LifeCycle lifeCycle:LifeCycle.values()){
        plantsByLifeCycle.put(lifeCycle,new HashSet<>());
        }

        for(Plant plant:garden){
        plantsByLifeCycle.get(plant.lifeCycle).add(plant);
        }
        System.out.println(plantsByLifeCycle);

        }
```

- 더 짧고 명료하고 안전하고 성능도 원래 버전과 비등

:  내부적으로 배열을 사용하므로 배열의 성능을 유지하면서도, 열거 타입의 타입 안정성을 활용

- 안전하지 않은 형변환은 쓰지 않고, 맵의 키인 열거 타입이 그 자체로 출력용 문자열을 제공

### 스트림 사용

```java
public static void streamEx1(List<Plant> garden){Map plantsByLifeCycle=garden.stream().collect(
        Collectors.groupingBy(plant->plant.lifeCycle));System.out.println(plantsByLifeCycle);}

// 성능 개선을 위해 EnumMap과 Set 사용 public static void streamEx2(List<Plant> garden) { Map plantsByLifeCycle = garden.stream()
        .collect(Collectors.groupingBy(plant->plant.lifeCycle,
        ()->new EnumMap<>(LifeCycle.class),Collectors.toSet()));System.out.println(plantsByLifeCycle);}

```

- EnumMap 버전은 언제나 식물의 생애주기당 하나씩의 중첩 맵을 만들지만, 스트림 버전에서는 해당 생애주기에 속하는 식물이 있을 때만 만든다.

### 정리

배열의 인덱스를 얻기 위해 ordinal을 쓰는 것은 일반적으로 좋지 않으니, 대신 EnumMap을 사용하라.

다차원 관계는 EnumMap<..., EnumMap<...>> 으로 표현하라