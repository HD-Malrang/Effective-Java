# 생성자에 매개변수가 많다면 빌더를 고려하라



## 생성자나 정적 팩터리가 처리해야 할 매개변수가 많다면 빌더 패턴을 선택하는 것이 좋다.



### 선택적 매개변수가 많은 클래스의 생성자 혹은 정적 팩터리 패턴 3가지

 <br>

1. 점층적 생성자 패턴

- 받는 매개변수 개수에 따라 생성자를 여러개 만든다.

(-)코드 작성시 매개변수의 의미나 개수가 헷갈려서 실수 할 수 있다. 읽기도 어렵다.

```java
Test t = new Test(240, 8, 100, 0, 35, 27);
```

<br>
2. 자바빈즈 패턴

- 매개변수가 없는 생성자로 객체를 만든 후, 세터(setter) 메서드들을 호출해 원하는 매개변수의 값을 설정한다.

(+)인스턴스 만들기가 쉽고 읽기 쉽다.

(-) 객체 하나를 만들기 위해서는 세터 메서드를 여러개 호출해야한다. <br>객체 생성 코드와 값을 설정하는 부분이 떨어져있어 일관성 문제 발생. <br>불변성 문제도 있다. 외부에서 세터 메서드를 호출하여 값 조작이 가능하다.

 <br>

3. 빌더 패턴

- Builder 클래스를 만들어 메소드로 단계적으로 값을 입력받은 후, 최종적으로 build() 메소드로 하나의 인스턴스 생성

- 빌더의 세터 메서드들은 빌더 자신을 반환하기 때문에 연쇄적으로 호출 할 수 있다. <플루언트 API(fluent API) 혹은 메서드 연쇄(method chaining)>

(+) 코드 작성이 쉽고 읽기도 쉽다. 필수와 선택 매개변수 구분 가능, 매개변수 검증 분리 가능(매개변수 설정 메서드에서)

(-) 빌더 생성 비용이 들어 성능에 영향을 줄 수 있다. 

```java
Test t = new Test.Builder(240, 8) //필수값
.one(100).two(35).three(27) //선택값
.build();
```
- 빌더패턴은 롬복 @Builder으로 사용 가능 
  - 모든 파라미터를 받는 생성자가 생김.
  - 필수값 설정 불가능

### 확인해보고 싶은 내용

지금 프로젝트에도 매개변수가 많은 클래스들이 있는데 적용 가능한지.,
