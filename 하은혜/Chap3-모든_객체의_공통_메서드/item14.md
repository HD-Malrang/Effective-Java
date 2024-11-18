# ITEM 14. Comparable을 구현할지 고민하라

> **순서를 고려해야 하는 값 클래스를 작성한다면 꼭 Comparable 인터페이스를 구현하여, 그 인스턴스들을 쉽게 정렬하고, 검색하고, 비교 기능을 제공하는 컬렉션과 어우러지도록 해야 한다. compareTo 메서드에서 필드의 값을 비교할 때 “<” 와 “>” 연산자는 쓰지 말아야 한다. 그 대신 박싱된 기본 타입 클래스가 제공하는 정적 compare 메서드나 Comparator 인터페이스가 제공하는 비교자 생성 메서드를 사용하자.**

## compareTo 규약

- Object.equals에 더해서 순서까지 비교할 수 있으며 Generic을 지원한다.
- 자기 자신이(this)이 compareTo에 전달된 객체보다 작으면 음수, 같으면 0, 크다면 양수를 리턴한다.
- 반사성, 대칭성, 추이성을 만족해야 한다.
- 반드시 따라야 하는 것은 아니지만 x.compareTo(y) == 0 이라면 x.equals(y)가 true여야 한다.
    - BigDecimal은 위 조건을 성립하지 못한다.
    - new BigDecimal(”1.0”)과 new BigDecimal(”1.00”)은 compareTo는 0이 나오지만, equals는 false다.

## compareTo 구현 방법 1
- 자연적인 순서를 제공할 클래스에 implements Comparable<T> 을 선언한다.

    ```java
    // PhoneNumber를 비교할 수 있게 만든다. (91-92쪽)
    public final class PhoneNumber implements Cloneable, Comparable<PhoneNumber> {
        private final short areaCode, prefix, lineNum;
    
        public PhoneNumber(int areaCode, int prefix, int lineNum) {
            this.areaCode = rangeCheck(areaCode, 999, "지역코드");
            this.prefix   = rangeCheck(prefix,   999, "프리픽스");
            this.lineNum  = rangeCheck(lineNum, 9999, "가입자 번호");
        }
    
        private static short rangeCheck(int val, int max, String arg) {
            if (val < 0 || val > max)
                throw new IllegalArgumentException(arg + ": " + val);
            return (short) val;
        }
    
        @Override public boolean equals(Object o) {
            if (o == this)
                return true;
            if (!(o instanceof PhoneNumber))
                return false;
            PhoneNumber pn = (PhoneNumber)o;
            return pn.lineNum == lineNum && pn.prefix == prefix
                    && pn.areaCode == areaCode;
        }
    
        @Override public int hashCode() {
            int result = Short.hashCode(areaCode);
            result = 31 * result + Short.hashCode(prefix);
            result = 31 * result + Short.hashCode(lineNum);
            return result;
        }
    
        /**
         * 이 전화번호의 문자열 표현을 반환한다.
         * 이 문자열은 "XXX-YYY-ZZZZ" 형태의 12글자로 구성된다.
         * XXX는 지역 코드, YYY는 프리픽스, ZZZZ는 가입자 번호다.
         * 각각의 대문자는 10진수 숫자 하나를 나타낸다.
         *
         * 전화번호의 각 부분의 값이 너무 작아서 자릿수를 채울 수 없다면,
         * 앞에서부터 0으로 채워나간다. 예컨대 가입자 번호가 123이라면
         * 전화번호의 마지막 네 문자는 "0123"이 된다.
         */
        @Override public String toString() {
            return String.format("%03d-%03d-%04d",
                    areaCode, prefix, lineNum);
        }
    
        // 코드 14-2 기본 타입 필드가 여럿일 때의 비교자 (91쪽)
        @Override
        public int compareTo(PhoneNumber pn) {
            int result = Short.compare(areaCode, pn.areaCode);
            if (result == 0)  {
                result = Short.compare(prefix, pn.prefix);
                if (result == 0)
                    result = Short.compare(lineNum, pn.lineNum);
            }
            return result;
        }
    
    
        private static PhoneNumber randomPhoneNumber() {
            Random rnd = ThreadLocalRandom.current();
            return new PhoneNumber((short) rnd.nextInt(1000),
                    (short) rnd.nextInt(1000),
                    (short) rnd.nextInt(10000));
        }
    }
    ```
  
- compareTo 메서드를 재정의한다.

    ```java
    @Override
    public int compareTo(PhoneNumber pn) {
        int result = Short.compare(areaCode, pn.areaCode);
    
        if (result == 0)  {
            result = Short.compare(prefix, pn.prefix);
    
            if (result == 0)
                result = Short.compare(lineNum, pn.lineNum);
        }
        return result;
    }
    ```

- compareTo 메서드 안에서 기본 타입은 박싱된 기본 타입의 compare을 사용해 비교한다.
- 핵심 필드가 여러 개라면 비교 순서가 중요하다. 순서를 결정하는데 있어서 가장 중요한 필드를 비교하고 그 값이 0이라면 다음 필드를 비교한다.
- 기존 클래스를 확장하고 필드를 추가하는 경우 compareTo 규약을 지킬 수 없다.
    - Composition을 활용할 것.

## compareTo 구현 방법 2

- 자바 8부터 함수형 인터페이스, 람다, 메서드 레퍼런스 와 Comparator가 제공하는 기본 메서드와 static 메서드를 사용해서 Comparator를 구현할 수 있다.
- Comparator가 제공하는 메서드 사용하는 방법
    - Comparator의 static 메서드를 사용해서 Comparator 인스턴스 만들기
    - 인스턴스를 만들었다면 default 메서드를 사용해서 메서드 호출 이어가기 (체이닝)
    - static 메서드와 default 메소드의 매개변수로는 람다 표현식 또는 메서드 레퍼런스를 사용할 수 있다.
- Comparator 인터페이스를 사용하면 읽기가 편하다는 장점이 있다.
- Comparator 인터페이스 사용시 성능이 약 10% 정도 느리다는 단점이 있다고는 하지만 크게 신경쓰지 않아도 될 것 같다.
