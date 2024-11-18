# ITEM 11. equals를 재정의하려거든 hashCode도 재정의하라

> **equals를 재정의할 때는 hashCode도 반드시 재정의해야 한다. 그렇지 않으면 프로그램이 제대로 동작하지 않을 것이다. 재정의한 hashCode는 Object의 API문서에 기술된 일반 규약을 따라야 하며, 서로 다른 인스턴스라면 되도록 해시코드도 서로 다르게 구현해야 한다. 이렇게 구현하기가 어렵지는 않지만 조금 따분한 일이긴 하다. 하지만 걱정마시라. 아이템 10에서 이야기한 AutoValue 프레임워크를 사용하면 멋진 equals와 hashCode를 자동으로 만들어준다. IDE들도 이런 기능을 일부 제공한다.**

## hashCode 규약

- equals 비교에 사용하는 정보가 변경되지 않았다면 hashCode는 매번 같은 값을 리턴해야 한다. ( 변경되거나, 애플리케이션을 다시 실행했다면 달라질 수 있다. )
- **두 객체에 대한 equals가 같다면, hashCode의 값도 같아야 한다.**
- 두 객체에 대한 equals가 다르더라도, hashCode의 값은 같을 수 있지만 해시 테이블 **성능을 고려해 다른 값을 리턴하는 것이 좋다.**
  - 항상 같은 hashCode를 리턴하게 되면 성능이 O(1) 에서 O(n) 으로 떨어지게 된다.

## hashCode 구현 방법

```java
@Override public int hashCode() {
  int result = Short.hashCode(areaCode);  // 1
  result = 31 * result + Short.hashCode(prefix);  // 2
  result = 31 * result + Short.hashCode(lineNum);  // 3
  return result;
}

// areacode, prefix, lineNum은 PhoneNumber(선언한 클래스)클래스의 변수다.
```

1. 핵심 필드 하나의 값의 해쉬값을 계산해서 result 값을 초기화 한다.
2. 기본 타입은 Type.hashCode
   참조 타입은 해당 필드의 hashCode
   배열은 모든 원소를 재귀적으로 위의 로직을 적용하거나, Arrays.hashCode
   result = 31 * result + 해당 필드의 hashCode 계산값
3. result를 리턴한다.

- 31인 이유
  - 홀수를 사용해야한다. (짝수를 사용하면 비트연산이 발생하여 값이 왼쪽으로 밀리면서 날아갈수 있다.)
  - 해시 충돌이 가장 적게 나는 숫자이다.

## 기타

- 그냥 자바의 Objects.hash()를 써라.
- equals와 hashCode를 구현하면 해당 구현에 대한 테스트도 해야한다. 하지만 lombok을 사용하면 해당 부분은 테스트 하지 않아도 된다.
