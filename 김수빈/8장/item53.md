## 가변인수는 신중히 사용하라

- 가변인수 메서드 : 명시한 타입의 인수를 0개 이상 받을 수 있음

```java
static int sum(int...args){
        int sum=0;
        for(int arg:args)
        sum+=arg;
        return sum;
        }
```

### 인수가 1개 이상이어야 할때

- 잘못 구현한 예

-- 인수를 0개 넣어 호출하면 컴파일이 아닌 런타임에 실패. 코드 지저분

```java
static int min(int...args){
        if(args.length=0)
        throw new IllegalArgumentException("인수가 1개 이상 필요합니다.");

        int min=args[0];
        for(int i=1;i<args.length;i++)
        if(args[i]<min)
        min=args[i];
        return min;
        }
```

- 제대로 사용하기

: 매개변수를 2개 받기 - 하나는 평범한 매개변수, 두번째는 가변인수
```java
static int min(int firstArg, int... remainingArgs) { 
    int min = firstArg; for (int arg : remainingArgs)
    if (arg < min) min = arg; return min; 
}
```


### 성능을 고려해야할때

: 가변 인수 메서드는 호출될 때마다 배열을 새로 하나 할당하고 초기화 -> 성능에 민감하면 걸림돌이 될 수 있음

-> 다중정의를 활용하면 성능 최적화 가능
```java
public void foo() { } 
public void foo(int a1) { } 
public void food(int a1, int a2) { } 
public void foo(int a1, int a2,int a3) { } 
public void foo(int a1, int a2, int a3, int... rest) { }
```


- EnumSet의 정적 팩터리도 이 기법을 사용해 열거 타입 집합 생성 비용을 최소화함.
```java
EnumSet<DayOfWeek> weekend = EnumSet.of(DayOfWeek.SATURDAY, DayOfWeek.SUNDAY); 
```

```java
public static <E extends Enum<E>> EnumSet<E> of(E e) { 
    EnumSet<E> result = noneOf(e.getDeclaringClass()); result.add(e); 
    return result; 
}

    ...
public static <E extends Enum<E>> EnumSet<E> of(E e1, E e2, E e3, E e4, E e5){ 
    EnumSet<E> result = noneOf(e1.getDeclaringClass()); 
    result.add(e1); 
    result.add(e2); 
    result.add(e3); 
    result.add(e4);
    result.add(e5); 
    return result; 
}

@SafeVarargs public static <E extends Enum<E>> EnumSet<E> of(E first, E... rest) { 
    EnumSet<E> result = noneOf(first.getDeclaringClass()); 
    result.add(first); 
    for (E e : rest) result.add(e); 
    return result;}
```


