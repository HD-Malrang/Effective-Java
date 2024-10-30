# equals를 재정의하려거든 hashCode도 재정의하라

- 재정의 하지 않으면 hashCode 일반 규약을 어기게 되어 HashMap 같은 컬렉션의 원소로 사용할 때 문제가 발생함.

 

### Object 명세 규약

1.  equals 비교에 사용되는 정보가 변경되지 않았다면, 애플리케이션이 실행되는 동안 그 객체의 hashCode 메서드는 몇 번을 호출해도 일관되게 같은 값을 반환해야 한다.

2. equals(Object)가 두 객체를 같다고 판단했다면, 두 객체의 hashCode는 똑같은 값을 반환해야 한다.

3. equals(Object)가 두 객체를 다르다고 판단했더라도, 두 객체의 hashCode가 서로 다른 값을 반환할 필요는 없다. 단 다른 객체에 대해서는 다른 값을 반환해야 해시테이블의 성능이 좋아진다.

 

### eqauls만 재정의하고 hashCode를 재정의하지 않았을 때의 문제점
```java
Map<PhoneNumber, String> m = new HashMap<>();
m.put(new PhoneNumber(707, 867, 5309), "제니");
m.get(new PhoneNmber(707, 867, 5309)); -> null 반환
```
넣을때와 찾을때 2개의 인스턴스가 사용되었다. 논리적 동치인 두 객체가 서로 다른 해시코드를 반환.

hashMap은 해시코드가 다른 엔트리끼리는 동치성 비교를 시도조차 하지 않음.

 

 

### 최악의 hashCode 구현 - 사용 금지!
```java
@Override public int hashCode() { return 42; }
```
모든 객체에게 똑같은 값을 내어주기 때문에 모든 객체가 해시테이블의 버킷 하나에 담겨 마치 연결 리스트처럼 동작한다. <b>(해시충돌)</b>

--> 연결리스트처럼 동작한다는 뜻 : 해시코드가 모두 같기 때문에 하나씩 확인해야한다. 평균 수행시간도 O(1) 에서 O(n)으로 느려짐.

--> 자바 8 이후에는 연결 리스트 대신 이진 트리를 사용하도록 변경됨. 시간 복잡도는 O(logn)이라 성능은 더 좋음

 

 

### 전형적인 hashCode 메서드

- 필드를 사용하여 해시코드 계산 (핵심 필드를 생략하면 해시 품질이 나빠짐.)
```java
@Override public int hashCode() {
	int result = Short.hashCode(areaCode);
	result = 31 * result + Short.hashCode(prefix);
	result = 31 * result + Short.hashCode(lineNum);
	return result;
}
```
 

 

### 한 줄짜리 hashCode 메서드 - 성능이 살짝 아쉬움 (인텔리제이에서 생성해주는 메서드)
```java
@Override public int hashCode() {
	return Objects.hash(lineNum, prefix, areaCode);
}

// -> Arrays.hashCode(values); 사용
```

 

### hashCode를 캐싱하는 방식

- hashCode를 필드로 선언해놓고 hashCode 처음 호출시 계산해서 저장해놓은 값을 재사용한다

- 이때 지연초기화 방식을 쓴다면 동기화 문제가 발생할 수 있으므로 스레드 안정성 고려가 필요.
```java
private int hashCode; // 자동으로 0으로 초기화된다.

@Override public int hashCode() {
	int result = hashCode;
	if (result = 0) {
		result = Short.hashCode(areaCode);
		result = 31 * result + Short.hashCode(prefix);
		result = 31 * result + Short.hashCode(lineNum);
		hashCode = result;
	}
	return result;
}
```